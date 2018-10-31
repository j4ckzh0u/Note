ZooKeeper 底层其实只提供了两个功能：①管理（存储、读取）用户程序提交的数据；②为用户程序提交数据节点监听服务。主要用来解决分布式一致性的问题。

**SEQUENTIAL** 序列的节点

```text
create /znoe/mynode -s -e value
-s 代表序列的节点 -e代表的是临时性的节点
```

版本？

每个ZNode，Zookeeper 都会为其维护一个叫作 **Stat** 的数据结构，Stat中记录了这个 ZNode 的三个数据版本，分别是version（当前ZNode的版本）、cversion（当前ZNode子节点的版本）和 cversion（当前ZNode的ACL版本）。

- **顺序一致性：** 从同一客户端发起的事务请求，最终将会严格地按照顺序被应用到 ZooKeeper 中去。
- **原子性：** 所有事务请求的处理结果在整个集群中所有机器上的应用情况是一致的，也就是说，要么整个集群中所有的机器都成功应用了某一个事务，要么都没有应用。
- **单一系统映像 ：** 无论客户端连到哪一个 ZooKeeper 服务器上，其看到的服务端数据模型都是一致的。
- **可靠性：** 一旦一次更改请求被应用，更改的结果就会被持久化，直到被下一次更改覆盖。

命令

```
create [-s] [-e] path data [acl]
ls path [watch]
get path [watch]
set path data [version]  //这里的version是乐观锁，对应数据版本
delete path [version]
```

readonly模式？

集群的机器和超过一半的机器失去联系就不再提供服务，使用readonly模式可以允许这些机器提供读服务。

session创建是一个异步过程。



