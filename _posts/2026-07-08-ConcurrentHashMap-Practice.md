# Build Kafka Producer Proxy On HTTP Protocol with ConcurrentHashMap

Build a **PoolProducer** for pooling management 

```java
import org.apache.kafka.clients.producer.KafkaProducer;

public class PoolManagedProducer {

    volatile long lastAccessTime;
    private volatile boolean closed = false;

    private final KafkaProducer<String, String> kafkaProducer;

    public PoolManagedProducer(KafkaProducer<String, String> kafkaProducer) {
        this.kafkaProducer = kafkaProducer;
        this.lastAccessTime = System.currentTimeMillis();
    }

    public void markAccessTime() {
        lastAccessTime = System.currentTimeMillis();
    }

    public boolean isClosed() {
        return closed;
    }

    public void markClosed() {
        this.closed = true;
    }

    public KafkaProducer<String, String> getKafkaProducer() {
        return kafkaProducer;
    }

    public void close() {
        this.closed = true;
        if (kafkaProducer != null) {
            kafkaProducer.close(Duration.ofSeconds(5));
        }
    }
}
```


Build a manager for **PoolProducer**

```java
@Component
public class PoolKafkaProducerManager {

    private final Logger log = LoggerFactory.getLogger(PoolKafkaProducerManager.class);

    private final ConcurrentMap<String, PoolManagedProducer> producerMap = new ConcurrentHashMap<>();
    private final KafkaProducerDefaultConfigProviderService configProviderService;
    private final ScheduledExecutorService producerCleanupExecutor;

    private static final long CLEANUP_INTERVAL_MS = 30_000; // 30seconds
    private static final long MAX_IDLE_TIME_MS = 300_000;   // 5minutes

    public PoolKafkaProducerManager(KafkaProducerDefaultConfigProviderService configProviderService) {
        this.configProviderService = configProviderService;
        this.producerCleanupExecutor = Executors.newSingleThreadScheduledExecutor(r -> {
            Thread t = new Thread(r, "producerIdleCleanup");
            t.setDaemon(true);
            return t;
        });

        // Start scheduled cleanup task
        this.producerCleanupExecutor.scheduleAtFixedRate(
                this::cleanupIdleProducers,
                CLEANUP_INTERVAL_MS,
                CLEANUP_INTERVAL_MS,
                TimeUnit.MILLISECONDS
        );
    }

    public KafkaProducer<String, String> acquireProducer(String resourceId) {
        return producerMap.compute(resourceId, (resourceIdKey, existingProducer) -> {
            // If exists and not closed, refresh access time and reuse
            if (existingProducer != null && !existingProducer.isClosed()) {
                existingProducer.markAccessTime();
                return existingProducer;
            }

            // Does not exist or has been marked closed by cleanup thread, create a new producer
            Properties properties = configProviderService.provide(resourceId);
            return new PoolManagedProducer(new KafkaProducer<>(properties));
        }).getKafkaProducer();
    }

    private void cleanupIdleProducers() {
        if (producerMap.isEmpty()) {
            log.warn("producer map was empty");
            return;
        }

        long now = System.currentTimeMillis();
        List<PoolManagedProducer> toClose = new ArrayList<>();

        producerMap.forEach((resourceId, managedProducer) -> {
            if ((now - managedProducer.lastAccessTime) > MAX_IDLE_TIME_MS) {
                // Mark closed first, then atomically remove: only removes if value is still the same object
                managedProducer.markClosed();
                if (producerMap.remove(resourceId, managedProducer)) {
                    toClose.add(managedProducer);
                    log.info("producer idle timeout, marked for close. resourceId:{}", resourceId);
                }
            }
        });

        // Close outside the map to avoid blocking business threads
        for (PoolManagedProducer producer : toClose) {
            try {
                producer.close();
            } catch (Exception e) {
                log.error("close producer failed. error:{}", e.getMessage());
            }
        }
    }
}
```


## Race Condition Analysis

The core issue is that the **"close first, then remove"** two-step operation in `cleanupIdleProducers()` is not atomic, while `acquireProducer()`'s `compute()` only checks `existingProducer != null` without verifying whether it has already been closed.

Here's a timeline diagram illustrating the race window:

```
Timeline →

Cleanup Thread (producerIdleCleanup)        Business Thread (HTTP Request)
────────────────────────────────────        ──────────────────────────────

① Iterates entrySet, finds resourceId="A"
   timed out, executes managedProducer.close()
   ↓ producer's underlying connection is closed
                                            ② acquireProducer("A")
                                               enters compute()
                                               existingProducer != null ✓
                                               (not yet removed from map, still present)
                                               executes markAccessTime()
                                               returns this already-closed producer

                                            ③ Uses this producer to send a message
                                               → throws IllegalStateException:
                                                 "Cannot perform operation
                                                  after producer has been closed"

④ readyToRemoveResourceIds.add("A")
⑤ producerMap.remove("A")
   (too late, business thread already got the bad producer)
```

### Root Cause Analysis

The problem lies in this code within `cleanupIdleProducers()`:

```java
// Lines 74-78
if ((now - managedProducer.lastAccessTime) > MAX_IDLE_TIME_MS) {
    try {
        managedProducer.close();           // ← ① Close the producer first
        readyToRemoveResourceIds.add(resourceId);
    } catch (Exception e) { ... }
}

// Lines 83-85
if (!readyToRemoveResourceIds.isEmpty()) {
    readyToRemoveResourceIds.forEach(producerMap::remove);  // ← ② Remove from map later
}
```

**Key contradiction**:
- `acquireProducer()` uses `compute()` to guarantee atomicity of "get or create"
- But `cleanupIdleProducers()`'s `close()` + `remove()` are **two separate non-atomic operations**
- Between ① and ② there exists a time window where the map still holds a **closed** producer object

Although `compute()` internally holds a bin lock on a single key, the cleanup thread's `close()` operation is not executed under any `compute` lock protection, so there is no mutual exclusion between them.

### Why Can't `compute()` Protect Against This Scenario?

`ConcurrentHashMap.compute()` only guarantees: **during the execution of compute's lambda, the same key will not be concurrently modified by other compute/put/remove operations**.

However, the cleanup thread calls `managedProducer.close()` — this is a modification to the **internal state of the value object**, not a modification to the map structure, so `compute()`'s lock simply cannot govern it.

### Fix Approach

The correct approach is to **atomically remove from the map first, then close**:

```java
private void cleanupIdleProducers() {
    long now = System.currentTimeMillis();
    List<PoolManagedProducer> toClose = new ArrayList<>();

    producerMap.forEach((resourceId, managed) -> {
        if ((now - managed.lastAccessTime) > MAX_IDLE_TIME_MS) {
            // Mark closed first, then atomically remove: only removes if value is still the same object
            managed.markClosed();
            if (producerMap.remove(resourceId, managed)) {
                toClose.add(managed);
            }
        }
    });

    // Close outside the map to avoid blocking business threads
    // At this point, business thread's compute() finds existingProducer == null or isClosed(),
    // and will create a new one
    toClose.forEach(PoolManagedProducer::close);
}
```

This ordering guarantees:
1. `remove(key, value)` is an atomic operation of ConcurrentHashMap
2. Once remove succeeds, the business thread's `compute()` will take the `existingProducer == null` branch and create a new producer
3. `close` is executed after remove, so it won't affect any business thread


## 竞态条件详解

核心问题在于 `cleanupIdleProducers()` 中的 **"先 close，后 remove"** 这两步操作不是原子的，而 `acquireProducer()` 中的 `compute()` 只检查了 `existingProducer != null`，没有检查它是否已经被 close。

让我用时间线图来说明这个竞态窗口：

```
时间轴 →

清理线程 (producerIdleCleanup)          业务线程 (HTTP 请求)
─────────────────────────────          ─────────────────────────
                                        
① 遍历 entrySet，发现 resourceId="A"
   超时，执行 managedProducer.close()
   ↓ producer 底层连接已关闭
                                        ② acquireProducer("A")
                                           进入 compute()
                                           existingProducer != null ✓
                                           (map 里还没 remove，所以还在)
                                           执行 markAccessTime()
                                           返回这个已关闭的 producer
                                        
                                        ③ 用这个 producer 发消息
                                           → 抛出 IllegalStateException:
                                             "Cannot perform operation 
                                              after producer has been closed"
                                        
④ readyToRemoveResourceIds.add("A")
⑤ producerMap.remove("A")
   (为时已晚，业务线程已经拿到坏的 producer)
```

### 根本原因分析

问题出在 `cleanupIdleProducers()` 的这段代码：

```java
// 第 74-78 行
if ((now - managedProducer.lastAccessTime) > MAX_IDLE_TIME_MS) {
    try {
        managedProducer.close();           // ← ① 先关闭 producer
        readyToRemoveResourceIds.add(resourceId);
    } catch (Exception e) { ... }
}

// 第 83-85 行
if (!readyToRemoveResourceIds.isEmpty()) {
    readyToRemoveResourceIds.forEach(producerMap::remove);  // ← ② 后从 map 移除
}
```

**关键矛盾**：
- `acquireProducer()` 使用 `compute()` 保证了"获取或创建"的原子性
- 但 `cleanupIdleProducers()` 的 `close()` + `remove()` 是**两个独立的非原子操作**
- 在 ① 和 ② 之间存在一个时间窗口，此时 map 中仍然持有一个**已关闭**的 producer 对象

`compute()` 内部虽然对单个 key 加了 bin 锁，但清理线程的 `close()` 操作并不在任何 `compute` 锁的保护下执行，所以两者之间没有互斥关系。

### 为什么 `compute()` 保护不了这个场景？

`ConcurrentHashMap.compute()` 只保证：**在 compute 的 lambda 执行期间，同一个 key 不会被其他 compute/put/remove 并发修改**。

但清理线程调用的是 `managedProducer.close()`——这是对 **value 对象内部状态** 的修改，不是对 map 结构的修改，所以 `compute()` 的锁根本管不到它。

### 修复方向

正确的做法是**先从 map 原子移除，再 close**：

```java
private void cleanupIdleProducers() {
    long now = System.currentTimeMillis();
    List<PoolManagedProducer> toClose = new ArrayList<>();

    producerMap.forEach((resourceId, managed) -> {
        if ((now - managed.lastAccessTime) > MAX_IDLE_TIME_MS) {
            // remove(key, value) 是原子操作：只有当 value 仍是同一个对象时才移除
            if (producerMap.remove(resourceId, managed)) {
                toClose.add(managed);
            }
        }
    });

    // 已经从 map 移除后，再安全地 close
    // 此时业务线程 compute() 发现 existingProducer == null，会创建新的
    toClose.forEach(PoolManagedProducer::close);
}
```

这样的顺序保证了：
1. `remove(key, value)` 是 ConcurrentHashMap 的原子操作
2. 一旦 remove 成功，业务线程的 `compute()` 就会走 `existingProducer == null` 分支，创建新 producer
3. close 在 remove 之后执行，不会影响任何业务线程
