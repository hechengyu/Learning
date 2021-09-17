# 4.Redis 其他命令

## 4.1 连接命令

| 命令          | 描述               |
| ------------- | ------------------ |
| auth password | 验证密码是否正确   |
| echo message  | 打印字符串         |
| ping          | 查看服务是否运行   |
| quit          | 关闭当前链接       |
| select index  | 切换到指定的数据库 |



## 4.2 键相关命令

| 命令                                      | 描述                                                         | 样例                        |
| ----------------------------------------- | ------------------------------------------------------------ | --------------------------- |
| TYPE key                                  | 查看对应值的存储类型。<br />string/hash/list/set/zset/none   |                             |
| EXISTS key                                | 检查 key 是否存在。返回 1\|0                                 |                             |
| KEYS pattern                              | 查找所有符合模式( pattern)的 key                             | KEYS *                      |
| DEL key [key ...]                         | 删除 key。返回删除键的数量                                   |                             |
| RENAME key newkey                         | 重命名。<br />改名成功提示 OK<br />失败（键名相同或键不存在）返回一个错误。newkey 已存在其值被覆盖 |                             |
| REMAMENX  key newkey                      | 重命名<br />修改成功时，返回 1 。<br /> newkey 已经存在，返回 0 。 |                             |
| EXPIRE key seconds                        | 设置过期时间，以秒计。返回 1\|0                              | EXPIREAT test 1             |
| PEXPIRE key milliseconds                  | 设置过期时间，以毫秒计。返回 1\|0                            | EXPIREAT test 1000          |
| EXPIREAT key timestamp                    | 设置过期时间，时间戳，以秒计。<br />返回 1\|0                | EXPIREAT test 1631846625    |
| PEXPIREAT key ms-timestamp                | 设置过期时间，时间戳，以毫秒计。<br />返回 1\|0              | EXPIREAT test 1631846625000 |
| TTL key（time to live）                   | 返回 key 的剩余生存时间，以秒计。-1：持久存在                |                             |
| PTTL key                                  | 返回 key 的剩余生存时间，以毫秒计。-1：持久存在              |                             |
| PERSIST key                               | 移除 key 的过期时间                                          |                             |
| DUMP key                                  | 序列化 key。返回被序列化的值                                 |                             |
| MOVE key db_index                         | 将当前数据库的 key 移动到给定的数据库 db 当中。返回 1\|0     |                             |
| RANDOMKEY                                 | 从当前数据库中随机返回一个 key。当不存在键时，返回 nil。     |                             |
| SCAN cursor [MATCH pattern] [COUNT count] | 基于游标的迭代器迭代数据库中的键，返回一个新的游标和键集<br />- cursor：游标<br />- pattern：匹配模式<br />- count：指定返回多少数据，默认10 |                             |