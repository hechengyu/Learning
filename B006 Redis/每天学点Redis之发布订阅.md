# 6.发布订阅

## 6.1 什么是发布订阅？

 发布订阅 (pub/sub) 是一种消息通信模式：发送者 (pub) 发送消息，订阅者 (sub) 接收消息

客户端可以订阅任意数量的频道。

## 6.2 相关命令

| 命令                                      | 描述                     |
| ----------------------------------------- | ------------------------ |
| subscribe channel [channel ...]           | 订阅给定的频道           |
| psubscribe pattern [pattern ...]          | 订阅符合给定模式的频道。 |
| unsubscribe channel [channel ...]         | 退订给定的频道           |
| punsubscribe pattern [pattern ...]        | 退订符合给定模式的频道。 |
| pubsub subcommand argument [argument ...] | 查看订阅与发布系统状态。 |
| publish channel message                   | 将信息发送到指定的频道。 |

测试：

redis-cli sub A

```shell
subscribe cpub
# 1) "subscribe"
# 2) "cpub"
# 1) "message"
# 3) (integer) 1
# 1) "message"
# 2) "cpub"
# 3) "test"
```



redis-cli sub B

```shell
subscribe cpub
# 1) "subscribe"
# 2) "cpub"
# 3) (integer) 1
# 1) "message"
# 2) "cpub"
# 3) "test"
```



redis-cli pub

```shell
publish cpub test
# (integer) 2
```