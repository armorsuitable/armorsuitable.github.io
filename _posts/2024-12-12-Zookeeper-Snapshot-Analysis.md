# Zookeeper Snapshot 分析


使用zookeeper 自带的工具类可分析

```bash
java -classpath .:lib/slf4j-api-1.6.1.jar:zookeeper-3.4.9.jar org.apache.zookeeper.server.SnapshotFormatter /data/zookeeper/data/version-2/snapshot.101b5b8b3
```

可以修改 **org.apache.zookeeper.server.SnapshotFormatter** 文件


打印所有 snapshot 节点数据
