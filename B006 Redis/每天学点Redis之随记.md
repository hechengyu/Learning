# GEO

# Stream

Redis 5.0 新增的数据结构

主要用于消息队列，提供消息的持久化和主备复制功能。

由于发布订阅的的消息无法持久化。



# 数据备份与恢复

## 数据备份

持久化策略：`RDB`和`AOF`

**RDB**

从内存数据库持久化数据到RDB文件：持久化key之前，会检查是否过期，过期的key不进入RDB文件 从RDB文件恢复数据到内存数据库：数据载入数据库之前，会对key先进行过期检查，如果过期，不导入数据库（主库情况）。

**AOF**

从内存数据库持久化数据到AOF文件：当key过期后，还没有被删除，此时进行执行持久化操作（该key是不会进入aof文件的，因为没有发生修改命令） 当key过期后，在发生删除操作时，程序会向aof文件追加一条del命令（在将来的以aof文件恢复数据的时候该过期的键就会被删掉） AOF重写：重写时，会先判断key是否过期，已过期的key不会重写到aof文件



| 命令   | 描述                                  |
| ------ | ------------------------------------- |
| save   | 在 redis 安装目录中创建 dump.rdb 文件 |
| gbsave | 后台执行备份                          |



## 数据恢复

只需将备份文件 (dump.rdb) 移动到 redis 安装目录并启动服务即可。

查看安装目录：`config get dir`



# 性能测试

> **注意**：该命令是在 redis 的目录下执行的，而不是 redis 客户端的内部指令。



```shell
# linux
redis-benchmark -n 10000  -q
redis-benchmark -h 127.0.0.1 -p 6379 -t set,lpush -n 10000 -q

# windows
.\redis-benchmark.exe -h 127.0.0.1 -p 6379 -t set,lpush -n 10000 -q
```



| 选项                      | 描述                                       | 默认值    |
| :------------------------ | :----------------------------------------- | :-------- |
| **-h**                    | 指定服务器主机名                           | 127.0.0.1 |
| **-p**                    | 指定服务器端口                             | 6379      |
| **-s**                    | 指定服务器 socket                          |           |
| **-c**                    | 指定并发连接数                             | 50        |
| **-n**                    | 指定请求数                                 | 10000     |
| **-d**                    | 以字节的形式指定 SET/GET 值的数据大小      | 2         |
| **-k**                    | 1=keep alive 0=reconnect                   | 1         |
| **-r**                    | SET/GET/INCR 使用随机 key, SADD 使用随机值 |           |
| **-P**                    | 通过管道传输 <numreq> 请求                 | 1         |
| **-q**                    | 强制退出 redis。仅显示 query/sec 值        |           |
| **--csv**                 | 以 CSV 格式输出                            |           |
| ***-l\*（L 的小写字母）** | 生成循环，永久执行测试                     |           |
| **-t**                    | 仅运行以逗号分隔的测试命令列表。           |           |
| ***-I\*（i 的大写字母）** | Idle 模式。仅打开 N 个 idle 连接并等待。   |           |



# 管道技术

Redis是一种基于C/S模型以及请求/响应协议的TCP服务，存在如下特性

- 客户端：发送请求，并监听Socket响应
- 服务端：阻塞模式，消息队列，响应处理请求，返回结果

作用：在服务端未响应时，客户端可以继续向服务端发送请求，并最终**一次性读取所有服务端的响应**。

```shell
(echo -en "PING\r\n SET runoobkey redis\r\nGET runoobkey\r\nINCR visitor\r\nINCR visitor\r\nINCR visitor\r\n"; sleep 10) | nc localhost 6379

# PING
# +PONG
# SET runoobkey redis
# +OK
# GET runoobkey
# redis
# INCR visitor
# :1
# INCR visitor
# :2
# INCR visitor
# :3
```



# 分区

将数据映射到多个Redis实例，每个实例只保存key的一个子集。

优点：

- 多个实例，在硬件（内存，CPU，网络等）上提供更好的资源。

不足：

- 多个 key 之间的联合操作不支持，例如：无法对 set 执行交焦操作
- 事务无法适用多个key
- 数据备份和恢复处理复杂
- 增加或删除容量复杂



## 分区类型

### 范围分区

映射指定范围的的对象到特定的Redis实例。

不足点：需要管理和维护区间范围到实例的映射表。

### 哈希分区

提取键的特征值，根据不同的键分区：

- 计算 hash 值：通过hash函数将key转换为一个数字。例如：crc32("foobar")
- 取模：将 hash 转换为实例编号0~n：hash % (n+1) = x，根据实例编号将键存储对应的实例中。

优点：任何 key 都适用



# 客户端连接

| 命令                       | 描述                                       | 参数                                                   |
| :------------------------- | :----------------------------------------- | ------------------------------------------------------ |
| **CLIENT LIST**            | 返回连接到 redis 服务的客户端列表          | type：<br>- normal<br>-master<br/>-replica<br/>-pubsub |
| **CLIENT SETNAME**         | 设置当前连接的名称                         |                                                        |
| **CLIENT GETNAME**         | 获取通过 CLIENT SETNAME 命令设置的服务名称 |                                                        |
| **CLIENT PAUSE** [timeout] | 挂起客户端连接，指定挂起的时间以毫秒计     |                                                        |
| **CLIENT KILL**            | 关闭客户端连接                             |                                                        |

```shell
 1) CLIENT <subcommand> arg arg ... arg. Subcommands are:
 2) id                     -- Return the ID of the current connection.
 3) getname                -- Return the name of the current connection.
 4) kill <ip:port>         -- Kill connection made from <ip:port>.
 5) kill <option> <value> [option value ...] -- Kill connections. Options are:
 6)      addr <ip:port>                      -- Kill connection made from <ip:port>
 7)      type (normal|master|replica|pubsub) -- Kill connections by type.
 8)      skipme (yes|no)   -- Skip killing current connection (default: yes).
 9) list [options ...]     -- Return information about client connections. Options:
10)      type (normal|master|replica|pubsub) -- Return clients of specified type.
11) pause <timeout>        -- Suspend all Redis clients for <timout> milliseconds.
12) reply (on|off|skip)    -- Control the replies sent to the current connection.
13) setname <name>         -- Assign the name <name> to the current connection.
14) unblock <clientid> [TIMEOUT|ERROR] -- Unblock the specified blocked client.
```