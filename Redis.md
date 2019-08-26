# Redis[^redis]
[^redis]:[REmote DIctionary Server],远程字典服务器,Redis 是完全开源免费的，遵守BSD协议，是一个高性能的key-value形式的NoSQL数据库


## NoSQL[^no]
[^no]:not only sql

+ CA**P**原理:一个分布式系统不可能同时满足一致性,可用性,**分区容错性**这三个需求,最多满足2个
  + C`consistency`:强一致性,数据是精准确切的	
  + A`availability`:高可用性,?数据高度可用,
  + P`partion tolerance`:分区容错性,?支持多个服务器
+ 三个原则/需求:
  + CA:单点集群,满足一致性,可用性的系统,扩展性不强,如Oracle
  + CP:满足一致性,分区容错性的系统,性能不是特别高,如Redis,MangoDB
  + AP:满足可用性,分区容错性的系统,一致性要求低,如大多数网站架构
## Redis的安装
+ 下载地址：[https://github.com/microsoftarchive/redis/releases](https://github.com/microsoftarchive/redis/releases "点击下载")
## Redis的使用
+ Redis数据库有16个[索引:0-15]

## Redis的数据类型
### **键(Key)**
+ `dbsize`: 统计数据库里数据个数
+ `select n`:连接第(n+1)个数据库
+ `flushdb`:清空当前数据库
+ `flushall`:清空所有库
+ `keys *`:列出所有key数据
+ `set key value`:设置key-value的数据(重复会覆盖),如姓名=zhangsan,命令:set name zhang
+ `get key`: 获取key对应的value值, 如,命令: get  name 结果为zhangsan
+ `del key`:删除key
+ `expire key second`:设置key的过期时间(秒),如,expire name 60 ,结果:60秒后自动删除
+ `ttl key`: 查看还有多少秒过期,-1:永不过期;-2:过期,如,ttl name  --\> -2
+ `type key`:查看key对应的数据类型
+ `move key db_index`:将key转移到指定数据库,如: move name 2
+ `exists key`:查询key是否存在,1:存在;0:不存在
### 字符串(String)
+ `append key val`:在key对应的value后面追加val,如,append name 123,结果为zhangsan123
+ `strlen key`: key对应的value长度
+ `incr key`: key对应的value(value是数字)自增1
+ `decr key`: key对应的value(value是数字)自减1
+ `incrby key n`:加法, key对应的value(value是数字) = value + n 
+ `decrby key n`: 减法,key对应的value(value是数字) = value - n 
+ `getrange key start end`: 从[start,end]截取字符,如: getrange name 0 3 结果:  zhan
+ `setrange key offset val`: 在offset位置用val替换,如, setrange name 0 AAA;结果:AAA ngsan
+ `setex key ex value`: 和set key value类似,只是设置了过期时间ex
+ `setnx key value`: 和set key value 类似,**会判断key不存在时才赋值**
+ `mset k1 v1 k2 v3 ...`: **一次设置多个k-v**
+ `mget k1 k2 k3...`:**一次获取多个对应的v值**
+ `msetnx k4 v4 k5 v5..`:带判断(**都不存在时有效**)一次设置多个k-v, 
+ `getset key newValue`: 先get在set,返回结果为旧值(get)
### 列表(List): List\<E\>
+ `lpush key value1,value2...`: 将一个或多个值插入到列表头部,倒序输出
+ `rpush key value1,value2...`:在列表中添加一个或多个值,按正常顺序进出
+ `lrange key start end`:集合输出,**lpush的倒序输出**(类似栈先进后出) 0:第一个;1:第二个;-1:最后一个;-2:倒数第二个
+ `lpop key`: 移出并获取列表的**第一个元素**
+ `rpop key`:移除列表的**最后一个元素**，返回值为移除的元素
+ `lindex key index`: 按照列表key的索引index取出元素
+ `llen key`: 列表key的长度,类似集合的size();方法
+ `lrem key count value`:删除集合中n个value值,如,list:1  1  43  2  2  2  2  `lrem list 3 2`;结果:list = 1 1 43 2
+ `ltrim key start stop`: 截取指定个数值,如,ltrim list 0 3;结果:list=1 1 43 2
+ `rpoplpush k1 k2`:先将k1执行rpop,再将出栈的元素lpush到k2
+ `lset key index value`: 对指定索引元素赋值
+ `linsert key before/after key1 newKey`:在集合元素key1前/后插入值newKey
### 集合(Set): Set\<E\>
+ `sadd key value1 value2 ...`: **集合key添加多个元素**
+ `smembers key`:**集合输出**
+ `scard key`: 统计集合个数
+ `sismember key member`:判断元素member是否属于该集合的成员
+ `srandmember key num`:随机从集合key取出n个元素
+ `srem key value1 value2..`:**删除集合key元素value**
+ `smove key1 key2 value`:把集合key1的元素value移动到集合key2去
+ `inter key1 key2`:集合的交集(会去重),类似mysql的inner join语法
+ `diff key1 key2`: 集合的差集,key1为参考,在key1中key2没有的元素,如,看k1:[a b c] k2:[1 a] 差集:b c
### 哈希(Map):Map<String,Object>
+ `hset key value[k-v]`: **哈希新增一个键值对,map.put(key,value)**
+ `hget key value[k]`:**依靠键获取哈希的值,value = map.getValue(key)**
+ `hmset key v1[k1-v1] v2[k2-v2]..`:**设置多个键值对**
+ `hmget key v1[k1] v2[k2]...`:**取出对应键的值**
+ `hdel key value[k]`:删除某个[k]对应的键值对
+ `hgetall key`:**取出所有的键值对,map的EntrySet**
+ `hlen key`: 哈希的长度
+ `hexists key value[k]`: 查询哈希的元素是否存在
+ `hkeys key`:取出所有的[k]键,map.keys()
+ `hvals key`:取出所有的[v]值,map.values();
+ `hincrby key value[k] integer`: 对哈希值为整数的值进行加法操作,加数也要整数
+ `hincrbyfloat key value[k] increment`:对哈希值为数值的值执行浮点型加法
+ `hsetnx key value[k-v]`:键不存在才插入
### 有序集合(Sorted set)
+ `zadd key [score1] member1 [score2] member2...`:**新增可带分数的有序集合元素**
+ `zrange key start end (withscore)`: **带分数的输出,0:表是第一个元素,1:第二个,-1:最后一个;-2:倒数第二**
+ `zrangebyscore member min max`:按分数范围输出[min,max]
+ `zrangebyscore member (min (max`:按分数范围输出(min,max)
+ `zrangebyscore member min max limit offset size`:按分数范围分页输出
+ `zrank key member`:获取集合元素对应的索引
+ `zscore key member`:返回有序集指定成员的分数值
+ `zrem key member..`:移除若干成员
+ `zcard key`:**获取有序集合的成员数**
+ `zcount key min max`:统计在有序集合中指定区间分数的成员数
+ `zrevrank key member`:按分数从大到小返回该成员的排名
+ `zrevrange key start end (withscore)`:返回有序集中指定区间内的成员，通过索引，分数从高到底排序
+ `zrevrangebyscore key max min [WITHSCORES] [LIMIT offset count]`:按指定分数区间返回成员,可带分数,分页
## Redis的配置
+ 备份最初未修改文件redis.conf
+ 使用备份文件
### redis.conf配置文件
+ includes: 包含多个配置文件,类似spring整合框架的多xml配置
+ modules: 多模块配置,启动时就加载
+ network: 网络绑定,多用户连接
  + `port`:redis的端口,默认6379
  + `TCP listen backlog`: 连接队列,在并发环境下,解决慢客户连接问题;backlog队列总和=(未完成+已完成)tcp三次握手
  + `timeout`:空闲后关闭连接
  + `tcp-keepalive`:间隔检测是否还连接
+ **general:通用**
  + `daemonize`: 守护线程(default no)
  + `supervised`:(default no)
  + `loglevel`:日志级别,notice[warning]\> [notice]\>[verbose]\>[debug]
  + `logfile`:日志文件
  + `databases`:数据库数量
+ **snapshotting:快照 **(rdb)
  + 默认三种保存方式: ①15min 改动1次 ②5min改动10次③1min改动1W次
  + save:立即保存到dump.rdb中,线程阻塞;不保存使用`save ""`
  + bgsave:异步保存,还可以处理其他事
  + stop-writes-on-bgsave-error yes:启动保存时发生错误就停止写入
  + rdbcompression yes:开启对dump.rdb(快照)LZF算法压缩存储
  + rdbchecksum yes:开启对存储后的快照CRC64算法数据校验
  + dbfilename dump.rdb:快照文件,默认dump.rdb
  + dir ./:工作路径,config get dir命令获取
+ **security:安全**
+ **limits:限制**
+ **replication:复制**
+ **append only file:追加**(aof)
  + appendonly: redis.conf配置开启: yes,默认no
  + appendfilename:默认appendonly.aof
  + **Appendfsync**:①Always:同步持久化,每次修改就会记录到磁盘,性能差但数据完整②**Everysec**:默认追加方式,异步操作,每秒记录,一秒内宕机,有数据丢失③No
  + no-appendfsync-on-rewrite no: 默认重写时不使用appendfsync(追加方式),保证数据完整
  + auto-aof-rewrite-percentage 100: 设置重写的要求,默认是上次重写文件大小1倍(至少)
  + auto-aof-rewrite-min-size 64mb:设置重写的要求,默认当期文件大于64M
### Maxmemory-policy缓存策略
+ volatitle-lru:使用LRU算法移除设置了过期时间的key
+ allkeys-lru:LRU算法移除所有key
+ volatile-random:使用LRU算法移除随机的设置了过期时间的key
+ allkeys-random:移除随机的key
+ volatile-ttl:移除ttl值最小的key
+ noeviction:不移除,针对写操作,只返回错误信息
+ `config get 配置名称`:查看密码信息,如 config get requirepass
+ `config get *`:查看所有的配置信息
+ `config set 配置名称 值`:设置某个配置的值,如设置密码:config set requirepass "123"

## 持久化
###  RDB: 
+ Redis DataBase(redis数据库)
+ dump.rdb文件保存(在设置时间内,改变n次后就会保存一个dump.rdb) 
+ 备份:在另一个机器上备份 `cp dump.rdb dum_bak.rdb`
+ 恢复:备份dump.rdb到redis根目录
+ 优点: ①适合大规模数据恢复,②对数据的一致性,完整性要求不高
+ 缺点:①间隔时间内备份,如果发生意外,会丢失最后一次快照修改的所有数据②fork大数据量会增加性能消耗
+ `redis-cli config set save ""`:动态停止所有rdb保存
### AOF
+ Append Only File(仅日志文件追加)
+ `appendonly yes`:redis.conf配置中启用持久化
+ apppendonly.aof:默认存储文件
+ 修复:使用`redis-check-aof --fix appendonly.aof`修复apppendonly.aof文件,重启Redis
+ ReWrite:重写
  + 定义:aof使用文件追加方式,文件会越来越大,采用重写机制,当aof文件大小超过规定值就会启动aof文件压缩
  + 原理:文件变大会fork一个子进程,对记录的aof文件重写
  + 触发机制:记录上次重写aof大小,默认是当AOF文件大小是上次Rewrite后大小的一倍且大于64M时触发(见配置)
+ 优点: 参考追加方式appendfsync
+ 缺点:①相同数据集下,aof文件要大于rdb,恢复速度慢于rdb② aof运行效率低于rdb,everysec策略较好,no策略效率与rdb相同
+ 以日志形式保存写操作命令,只在后面追加,恢复时只执行
## Redis的事务
+ 事务:批量执行多个命令,命令是原子性,事务不是,部分支持
+ `multi`:事务开始标志
+ `exec`:执行
+ `discard`:终止事务
+ `watch key`:监视若干个key,如果**该key**在事务执行前(exec前)被改动,事务会被打断
+ `unwatch`:取消watch命令对所有key的监视
+ 事务执行状态:
  + 正常执行
  + 放弃事务
  + 一个错全错(只要有个命令放入对列失败,事务全不执行)
  + 有错,不干扰其他(命令放入队列都成功,执行命名时,错误命令会失败,正确命令依然成功)
+ 乐观锁:
  + 乐观的认为每次读数据别人都不会修改,不上锁,只是提交更新时才确认是否有人修改过
+ 悲观锁
  + 悲观的认为每次读数据都会有人修改,每次读数据时会加锁,其他线程都阻塞,直到获得锁
+ CAS:check and set
## Redis发布订阅
+ `subscribe channel..`:订阅一个或多个频道
+ `unsubscribe channel..`指退订给定的频道。
+ `psubscribe pattern..`:订阅一个或 多个**给定模式**的频道
+ `punsubscribe pattern..`:退订所有**给定模式**的频道
+ `publish channel message`:将消息发送个指定频道
## Redis主从复制/读写分离
+ 主机(master)/ 从机(slave)关系:
  + ①一主多从:master [slave1,slave2,..]
  + ②薪火相传: master[slave1(m2),slave2,..] 
  + ③反客为主:master突然关闭,从slave中选择一个为master,原master无联系
  + ④哨兵模式:
    + sentinel.conf配置: sentinel monitor pid  host port 1(票数>1)
    + 启动: `redis-sentinel sentinel.conf`
    + 主机关闭,会开启哨兵模式投票选出新master,原master会归为slave   
  + 主机能读写,从机只读
  + 主机断开,主从关系依然存在
  + 从机断开,需要重新连接主机(除非在redis.config中配置) 
+ `slave no one`:master关机,在slave中选master
+ `salveof host port`:选择作为master
+ `info replication`:查看日志信息
## Redis常用命令
+ `redis-server redis.conf`:启动服务端
+ `redis-cli`:启动客户端
+ `ps -ef|grep redis`或`lsof -i : 6379`:检测redis启动
+ `cp file newFile`:复制file,新文件为newFile
+ `ping`:检测redis服务是否启动(已打开客户端),开启PONG
+ `redis-cli [--raw]  -h host -p port -a password`:在远程服务上执行命令,如:
  > 连接主机为127.0.0.1,端口为6379 密码为root的redis服务,[--raw]代表可选,防止中文乱码
  > redis-cli  -h  127.0.0.1  -p  6379  -a  "root"   顺便ping下 

