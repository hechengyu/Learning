# 8.Lua 脚本

## 8.1 什么是 Lua 脚本？



## 8.2 相关命令

| 命令                                             | 描述                                                         |
| ------------------------------------------------ | ------------------------------------------------------------ |
| EVAL script numkeys key [key ...] arg [arg ...]  | 使用 Lua 解释器执行脚本<br />- **script**: Lua 脚本程序<br/>- **numkeys**：指定参数个数<br/>- **key [key ...]**：用到的Redis 键，通过全局变量KEYS[1~N]数组获取键名<br/>- **arg [arg ...]**：附加参数，通过全局变量ARGV[1~N]数组获取 |
| evalsha sha1 numkeys key [key ...] arg [arg ...] | 根据给定的 sha1 校验码，执行缓存在服务器中的脚本<br />- **sha1** ： 通过 script load 命令 生成的 sha1 校验码 |
| script load script                               | 将脚本 script 添加到脚本缓存中，但并不立即执行这个脚本。脚本可以在缓存中保留无限长的时间，直到执行 script flush 为止。<br />- **script**：脚本内容 |
| script flush                                     | 从脚本缓存中移除所有脚本。                                   |
| script kill                                      | 杀死当前正在运行的 Lua 脚本。                                |
| script exists script [script ...]                | 查看指定的脚本是否已经被保存在缓存当中。<br />- **script**：通过 script load 命令 生成的 sha1 校验码 |

测试：

```shell
eval "return {KEYS[1],KEYS[2],ARGV[1],ARGV[2]}" 2 key1 key2 first second
# 1) "key1"
# 2) "key2"
# 3) "first"
# 4) "second"

script load "return 1"
# "e0e1f9fabfc9d4800c877a703b823ac0578ff8db"

script exists e0e1f9fabfc9d4800c877a703b823ac0578ff8db
# 1) (integer) 1

evalsha e0e1f9fabfc9d4800c877a703b823ac0578ff8db 0
# (integer) 1

script flush
# OK

script exists e0e1f9fabfc9d4800c877a703b823ac0578ff8db
# 1) (integer) 0
```