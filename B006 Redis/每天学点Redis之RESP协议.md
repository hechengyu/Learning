# 15.RESP协议

## 15.1 什么是 RESP？

 RESP(REdis Serialization Protocol) ，基于TCP的应用层协议。通过 TCP 进行数据传输，然后根据解析规则解析相应信息。支持以下数据类型的序列化协议：简单字符串，错误类型，整数，批量字符串和数组。

优点：

- 结构简单，容易实现，
- 解析快
- 人类可读

Redis Cluster使用不同的二进制协议(gossip)，以便在节点之间交换消息。



请求-响应协议过程：



参考：

- [Redis Protocol specification](https://redis.io/topics/protocol)

- [Redis底层协议RESP详解](https://www.jianshu.com/p/b11b593ccaf5)