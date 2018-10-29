docker run -it -p 6379:6379 05635 运行容器 '-it'不加则使用守护进程启动
默认端口6379, 使用`--port xxx`指定端口号

docker ps 查看运行中的容器
docker ps -a 查看系统中有哪些容器

docker stop 'container id' 停止运行容器

docker start 'container id' 使用守护进程启动 redis

docker exec -it 'container id' sh 进入虚拟系统 (-it 启用伪终端)

docker exec -it 550 redis-cli 直接进入 Redis
`/data #`

apk add git 安装git

## Redis

> Redis (Remote Dictionary Server) 是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。

> Redis 是单线程模型, 只供单线程使用. 由于其存储于内存, 因此速度极快.

#### Redis特性
1. 速度快

    基于内存, C语言实现, 单线程架构, 集性能和优雅于一身的代码

2. 基于键值对的数据结构服务器

    REmote DIctionary Server. 值可以是字符串/哈希/列表/集合/有序集合, Bitmap等等

3. 丰富的功能

    键过期功能. 发布-订阅功能(实现消息系统), Lua脚本, 简单的事务, 流水线(Pipeline)

4. 简单稳定

    代码少, 早期版本2万行. 3.0版本以后加入集群功能, 5万行. 单线程模型

5. 客户端语言多

6. 持久化

    (RDB和AOF方式)存放到硬盘中

7. 主从复制

8. 高可用和分布式

#### Redis使用场景

1. 缓存/session
2. 排行榜系统
3. 计数器应用

    视频的播放/浏览次数. 并发量太大, 对传统数据库挑战较大

4. 社交网络

    赞/踩, 粉丝, 共同好友/喜好等等杂的数据

5. 消息队列系统

    主要用于业务解耦

#### Redis安装

使用docker安装redis

#### Redis Shell

1. redis-server 启动Redis

    默认端口6379, 使用`--port xxx`指定端口号
    指定配置文件

2. redis-cli 命令行接口

### Redis使用

##### 全局命令

`set`
  - `set age 120 ex 10` 直接设置过期时间
  - `set age 10 nx` 'nx' -> 新增, 已存在即不创建
  - `set height 100 xx` 'xx' -> 更新一个值, 原 key 必须存在
  - `setex code 10 999888` 简写, 生成 key 为 code, 值为 999888, 存活时间10秒
  - `setnx code 100` 简写, 新增一个 key, 值为100

`keys *` 查看所有的 key

`dbsize` 查看 key 的数量

`exists key` 查看是否有某个 key

`del key [key ...]` 删除 key (可删除多个 key)

`expire key seconds` 设置过期时间 -- `expire code 10` 设置 key 为 'code' 的过期时间为10秒

`type key` 查看类型

##### key 的命名规范
`set role:findone:100 超级管理员` -- 设置 id 为 100 的人为超级管理员

### 数据结构和内部编码

##### i. 数据结构

1. string 字符串

    key 都是字符串类型, 而且其他几种数据结构都是在字符串类型基础上构建的.

    字符串类型的值实际上可以是简单或复杂的字符串(JSON/XML), 数字, 甚至是二进制(图片, 音频, 视频, 最大512M)

    `mset` 批量设置值, 可以提高开发效率 (Java 循环设置) <br>
        `mset a 1 b 2 c 3 d 4` 设置 a b c d 四个 key
    `mget` 批量获取值 <br>
        `mget a b c d e` 空的位置自动使用 nil 代替

    计数 <br>
    incr key, ++ `incr a` key 为 'a' 的值自增 <br>
    decr key, -- `decr a` key 为 'a' 的值自减 <br>
    incrby key xx, `incrby a 10` key 为 'a' 的值自增 10 <br>
    decrby key xx, `decrby a 10` key 为 'a' 的值自减 10 <br>

    开发中合理的key名, 业务名:对象名:id:属性

2. hash 哈希

    hset key field value <br>
    hget key field <br>
    hdel key field <br>
    hlen key <br>
    hmget key field <br>
    hmset key field value <br>
    hexists key field <br>
    hkeys key <br>
    hvals key <br>
    hgetall key <br>

3. list 列表

    存储多个有序的字符串 <br>
    rpush key value ... 右边插入元素 <br>
    lpush key value ... 左边 <br>
    lrange key start end <br>
    lpop key 左侧pop <br>
    lrem key count value <br>

4. set 集合

    不允许有重复元素.

    同时处理多个集合的交集/并集/差集

    sadd key element ... <br>
    exists key <br>
    srem key element ... 删除元素 <br>
    scard key 元素个数 <br>
    sismember key element 判断是否在集合中 <br>
    smembers key 所有元素 <br>



5. zset 有序集合

    zadd key score member [score member...] <br>
    zcard key <br>
    zscore key member <br>
    zrem key member <br>
    zincrby key score member <br>
    zrevrangebyrank key start end <br>






















<<<<<<<<<<<<<>>>>>>>>>>>>>

- redis-cli 进入 redis 环境 (-cli 命令行接口)
- `set name zhangsan`  可理解为`map.put('name', 'zhangsan')`
- `get name`           可理解为`map.get('name')`
- `keys *` 查看 key 的列表, 实际部署环境中严禁使用 (单线程)
- Redis 命令和 Java 的方法名相互对应 <br>

<<<<<<<<<<<<<>>>>>>>>>>>>>
