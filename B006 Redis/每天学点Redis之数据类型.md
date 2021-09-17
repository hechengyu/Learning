# 3.数据类型

支持五种数据类型：string、hash、list、set和zset。

| 类型   | 简介                                            | 特性                                                         | 场景                                                         |
| :----- | :---------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| string | 二进制安全，基础类型，，最大能存储 512 M        | 可以包含任何数据（例如：图片，序列化对象等）                 |                                                              |
| hash   | 键值对集合，类似 Map，可以存储 2^32 -1 个键值对 | 适合存储对象，支持只修改某一项属性值                         | 存储、读取、修改用户属性                                     |
| list   | 链表(双向链表)，可以存储 2^32 -1 个元素         | 增删快                                                       | - 最新消息排行等功能<br/>- 消息队列                          |
| set    | 哈希表，可以存储 2^32 -1 个元素                 | 无序集合，唯一不重复。<br>增删改<br/>集合求交集、并集、差集等操作 | - 共同好友<br/>- 统计访问网站的所有独立ip<br/>- 好友推荐时,根据tag求交集，大于某个阈值就可以推荐 |
| zset   | 哈希表，有序集合，成员不重复。                  | 将Set中的元素增加一个权重参数 score，元素按 score 有序从小到大的排除 | - 排行榜<br/>- 带权重的消息队列                              |



## 3.1 string

| 命令                                                   | 描述                                                         |
| ------------------------------------------------------ | ------------------------------------------------------------ |
| SET key value [EX seconds / PX milliseconds] [NX / XX] | 设置键值<br />- EX：n秒后过期<br/>- PX：n毫秒后过期<br/>- NX：键不存在<br/>- XX：键存在 |
| SETNX key value                                        | 设置键值<br />设置成功，返回1<br/>键存在，返回 0             |
| SETEX key seconds value                                | 设置键值和过期时间，以秒计                                   |
| PSETEX key milliseconds value                          | 设置键值和过期时间，以毫秒计                                 |
| GET key                                                |                                                              |
| GEYSET key value                                       | 获取旧值，替换新值<br />- 没有旧值返回 nil<br />- 键不存在或不是字符串时返回错误 |
| SETRANGE key offset value                              | 从偏移量 offset 开始，替换字符串，并返回被修改后的字符串长度。<br />- offset：0~n |
| GETRANGE key start end                                 | 返回 key 中字符串值的子字符<br />- start：起始位，为负标识倒序，超过长度限制则返回空字符串<br />- end：结束位，为负标识倒序，可超过字符串长度 |
| GETBIT key offset                                      | 获取键值指定偏移量上的位(bit)<br />- offset：0~n             |
| SETBIT key offset value                                | 设置或清除键值指定偏移量上的位(bit)<br />- offset：0~n       |
| MSET key value [key value ...]                         | 批量设置键值对<br />返回 1\|0                                |
| MSETNX key value [key value ...]                       | 批量设置键值对，如果某键存在，则全部失败<br />返回 1\|0      |
| MGET key [key ...]                                     | 获取所有(一个或多个)给定 key 的值。                          |
| STRLEN key                                             | 返回 key 所储存的字符串值的长度。                            |
| append key value                                       | 将指定的 value 追加到该 key 原来 value 的末尾（键存在且为string 类型） |
| INCR key                                               | 键值+1                                                       |
| INCRBY key increment                                   | 键值增加给定的增量值（increment）                            |
| INCRBYFLOAT key increment                              | 键值增加给定的浮点增量值（increment）                        |
| DECR key                                               | 键值-1                                                       |
| DECRBY key decrement                                   | 键值减去给定的减量值（decrement）                            |



## 3.2 hash

| 命令                                           | 描述                                                         |
| ---------------------------------------------- | ------------------------------------------------------------ |
| HMSET key field value [field value ...]        | 批量增加字段                                                 |
| HSET key field value                           | 增加字段                                                     |
| HSETNX key field value                         | 增加字段，字段不存在                                         |
| HDEL key field [field...]                      | 删除字段                                                     |
| HEXSIST key field                              | 字段是否存在                                                 |
| HLEN key                                       | 字段数量                                                     |
| HKEYS key                                      | 获取所有字段                                                 |
| HGETALL key                                    | 获取所有字段和值                                             |
| HGET key field                                 | 获取指定字段的值                                             |
| HMGET key field [field...]                     | 获取所有指定字段的值                                         |
| HINCREBY key field increment                   | +n                                                           |
| HINCREBYFLOAT key field increment              | +n（浮点）                                                   |
| HSCAN key cursor [MATCH pattern] [COUNT count] | 基于游标的迭代器迭代哈希表中的键值对，返回一个新的游标和集合<br />- cursor：游标<br />- pattern：匹配模式<br />- count：指定返回多少数据，默认10 |



## 3.3 list

| 命令                                  | 描述                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| LPUSH key value [value...]            | 将一个或多个值插入到列表头部                                 |
| LPUSHX key value [value...]           | 将一个或多个值插入到**已存在**的头部                         |
| RPUSH key value [value...]            | 将一个或多个值追加到列表尾部                                 |
| RPUSHX key value [value...]           | 将一个或多个值追加到**已存在**的列表尾部                     |
|                                       |                                                              |
| LPOP key                              | 移出并获取列表第一个元素                                     |
| RPOP key                              | 移出并获取列表最后一个元素                                   |
| BLPOP key1 [key2] timeout             | 移出并获取列表的第一个元素<br />如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。 |
| BRPOP key1 [key2] timeout             | 移出并获取列表的最后一个元素<br />如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。 |
| RPOPLPUSH srcList destList            | 移除列表的最后一个元素，并将该元素添加到另一个列表并返回     |
| BRPOPLPUSH srcList destList timeout   | 从列表中弹出一个值，将弹出的元素插入到另外一个列表中并返回它； <br />如果列表没有元素会**阻塞列表**直到等待超时或发现可弹出元素为止。 |
|                                       |                                                              |
| LLEN key                              | 获取列表的长度                                               |
| LSET key index value                  | 设置列表指定位置的值                                         |
| LTRIM key start stop                  | 保留指定范围的列表                                           |
| LINDEX key index                      | 获取指定位置的元素                                           |
| LRANGE key start stop                 | 获取列表指定范围的元素                                       |
| LREM key count value                  | 从列表中删除 count 与 value 相同的元素<br />count > 0：从列头开始搜索<br />count < 0：从列尾开始搜索<br />count = 0：移除所有相同值 |
| LINSERT key BEFORE\|AFTER pivot value | 将 value 插入到 pivot 之前或者                               |



## 3.4 set

| 命令                                       | 描述                                                         |
| ------------------------------------------ | ------------------------------------------------------------ |
| SADD key member [member ...]               | 向集合添加一个或多个成员                                     |
| SCARD key                                  | 获取结合成员数                                               |
| SMEMBERS key                               | 返回集合中的所有成员                                         |
| SISMEMBER key member                       | 判断 member 元素是否是集合 key 的成员                        |
|                                            |                                                              |
| SINTER key1 [key ...]                      | 返回 key1 与其他集合之间的交集                               |
| SUNION key [key ...]                       | 返回所有给定集合的并集                                       |
| SUNIONSTORE dest key [key ...]             | 所有给定集合的并集存储在 dest 集合中                         |
| SDIFF key1 [key ...]                       | 返回 key1 与其他集合之间的差集                               |
| SDIFFSTORE dest key [key ...]              | 返回给定所有集合的差集并存储在 dest 中                       |
|                                            |                                                              |
| SPOP key                                   | 移除并返回集合中的一个**随机**元素                           |
| SREM key member [member ...]               | 移除集合中一个或多个成员                                     |
| SRANDMEMBER key [count]                    | 返回集合中一个或多个随机数                                   |
| SMOVE src dest member                      | 将 member 元素从 source 集合移动到 destination 集合          |
|                                            |                                                              |
| SSCAN cursor [MATCH pattern] [COUNT count] | 基于游标的迭代器迭代集合中的元素，返回一个新的游标和元素集合<br />- cursor：游标<br />- pattern：匹配模式<br />- count：指定返回多少数据，默认10 |



## 3.5 zset

| 命令                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ZADD key score member [score member ...]                     | 向有序集合添加一个或多个成员，或者更新已存在成员的分数       |
| ZCARD key                                                    | 获取有序集合的成员数                                         |
| ZCOUNT key min max                                           | 指定区间分数的成员数                                         |
| ZLEXCOUNT key min max                                        | 指定字典区间内成员数量                                       |
| ZSCORE key member                                            | 返回有序集中，成员的分数值                                   |
| ZINCRBY key increment member                                 | 指定成员的分数加上增量 increment                             |
|                                                              |                                                              |
| ZINTERSTORE dest countKeys key [key ...]                     | 计算指定个数的有序集的交集<br />将结果集存储在新的有序集合 dest 中 |
| ZUNIONSTORE dest countKeys key [key ...]                     | 计算给定的一个或多个有序集的并集，并存储在新的 key 中        |
|                                                              |                                                              |
| ZRANGE key start stop [withscopres]                          | 通过索引区间返回有序集合指定区间内的成员                     |
| ZRANGEBYLEX key min max [LIMIT offset count]                 | 通过字典区间返回有序集合的成员                               |
| ZRANGEBYSCORE key min max [withscopres] [LIMIT offset count] |                                                              |
|                                                              |                                                              |
| ZRANK key member                                             | 返回有序集合中指定成员的索引                                 |
|                                                              |                                                              |
| ZREM key member [member ...]                                 | 移除有序集合中的一个或多个成员                               |
| ZREMRANGEBYLEX key min max                                   | 移除有序集合中给定的字典区间的所有成员                       |
| ZREMRANGEBYRANK key star stop                                | 移除有序集合中给定的排名区间的所有成员                       |
| ZREMRANGEBYSCORE key star stop                               | 移除有序集合中给定的分数区间的所有成员                       |
|                                                              |                                                              |
| ZREVRANGE key start stop [withscopres]                       | 返回有序集中指定区间内的成员，通过索引，分数从高到低         |
| ZREVRANGEBYSCORE key start stop [withscopres]                | 返回有序集中指定分数区间内的成员，分数从高到低排序           |
| ZREVRANK key member                                          | 返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序 |
|                                                              |                                                              |
| ZSCAN cursor [MATCH pattern] [COUNT count]                   | 基于游标的迭代器迭代集合中的有序集合中的元素（包括元素成员和元素分值），返回一个新的游标和元素集合<br />- cursor：游标<br />- pattern：匹配模式<br />- count：指定返回多少数据，默认10 |