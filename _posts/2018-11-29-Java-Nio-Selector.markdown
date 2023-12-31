# Selector Example

Selector 是 Java NIO 中核心的三部分之一，其余两个为 **Channel, Buffer**

## 事件注册

每个Channel实例需要通过注册某个事件，来完成select 查询的准备完成的事件


```java
Selector selector = Selector.open();
serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
```

## 循环查询完成的事件

```java
while (true) {
		int selectedKeyCount = selector.select(1000);
		// 没有完成的事件
		if (selectedKeyCount == 0) {
				continue;
		}
		// 完成的事件集
		Set<SelectionKey> selectionKeySet = selector.selectedKeys();
		//
		{
			....
		}
		// 清除完成的事件集合
		selectionKeySet.clear();
		
}
```

## 完成的事件中向下完成类型转换


### Accept 事件
```java

if (selectionKey.isAcceptable()) {
	ServerSocketChannel serverSocketChannel = (ServerSocketChannel)selectionKey.channel();		
	// 获取到客户端的socket 通道
    // 服务端可以通过把数据写入 socketChannel 里面
    SocketChannel socketClientChannel = serverSocketChannel1.accept();
    socketClientChannel.configureBlocking(false);
    // 注册 Socket 客户端通道上面 read 事件
    socketClientChannel.register(selector, SelectionKey.OP_READ);
    
    ...
}
```

### Read 事件

```java
if (selectionKey.isReadable()) {
    SocketChannel socketClientChannel = (SocketChannel) selectionKey.channel();
    // 客户端数据全部读出来
    while (socketClientChannel.read(readBuffer) > 0) {
        ...
    }
}
```
    