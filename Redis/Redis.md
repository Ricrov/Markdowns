## 基础
#### 基本命令
`docker run -it -p 6379:6379 '镜像id'` 创建运行容器 '-it'不加则使用守护进程启动. <br>
默认端口6379, 使用`--port xxx`指定端口号 <br>

`docker ps` 查看运行中的容器 <br>
`docker ps -a` 查看系统中有哪些容器 <br>

`docker start 'container id'` 使用守护进程启动 redis <br>

`docker stop 'container id'` 停止运行容器 <br>
`docker rm 'container id'` 删除容器 (前提: 非启动状态) <br>
`docker rmi 'image repository:tag'` 删除镜像 <br>

`docker exec -it 'container id' sh` 进入虚拟系统 (-it 启用伪终端) <br>
`docker exec -it 'container id' redis-cli` 直接进入 Redis <br>

#### 更多
http://hub.docker.com 远程仓库, 配置好的镜像可以推送至此. <br>
`docker tag redis:5-alpine '用户名/镜像新 tag 名:标记(标记内容随意)'` 重新给镜像打标签 <br>
`docker push '用户名'/新 tag 名:标记名` 推送镜像至仓库 <br>
`docker login` 登录 <br>
`docker export '容器 id' > 自定义名.tar ~/ ` 导出容器 -- `>`把前一个文件的输出写到目标的文件里<br>
`docker inport '自定义名.tar'` 导入容器 <br>
`docker commit '容器 id' 自定义名:标记` 将容器重新打包进镜像以便接下来的推广 <br>

> 若想推送至其他终端, 需重新打标签 <br>
> `docker tag redis:5-alpine 172.16.16.227:5000/新 tag 名:标记` 重新打标签 <br>
> `docker push 172.16.16.227:5000/新 tag 名:标记` 推送镜像 <br>
> `docker pull 172.16.16.227:5000/新 tag 名:标记` 拉取镜像 <br>


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
_Redis 命令和 Java 的方法名相互对应_
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

    开发中合理的key名, `业务名:对象名:id:属性`
    > `set role:findone:100 超级管理员` -- 设置 id 为 100 的人为超级管理员

2. hash 哈希
    > 一个 key 可以对应多个键值对 <br>

    ```java
    与 Map<String, Map<String, Object>> 类似
    ```

    `hset key field value` 对 key 设置多个属性 <br>
      hset user:1 name ric <br>
      hset user:1 age 31

    `hget key field` 取 key 中的某个属性 <br>
      hget user:1 name

    `hdel key field` 删除 key 的某个属性 (或多个属性) <br>
      hdel user:1 name age

    `hlen key` 查看一个 key 中有几个属性 <br>
      hlen user:1

    `hmset key field value` 给一个 key 设置多个属性多个值 <br>
      hmset user:2 name wzc age 28

    `hmget key field` 获取 key 的多个属性多个值 <br>
      hmget user:2 name age

    `hexists key field` 查看该 key 中是否存在某个属性 <br>
      hexists user:1 age

    `hkeys key` 查看 key 中的所有属性 (key) <br>
      hkeys user:1

    `hvals key` 查看 key 中的所有属性的值 <br>
      hvals user:1

    `hgetall key` 查看 key 中的所有属性和属性的值 <br>
      hgetall user:1

3. list 列表

    存储多个有序的字符串 <br>
    `rpush key value ...` 右边插入元素 <br>
    `lpush key value ...` 左边插入元素 <br>
    `lrange key start end` 按范围查看列表 (0 ~ -1 从第一个取到最后一个) <br>
    `lpop key` 从左侧弹出一个元素 <br>
    `rpop key` 从右侧弹出一个元素 <br>
    `lrem key count value` <br>
      LREM 命令会删除列表中前 count 个值为 value 的元素, 根据 count 值不同, LREM 命令的执行方式会略有差异: <br>
        当 count > 0 时, 从左边开始
        当 count < 0 时, 从右边开始
        当 count = 0 时, 删除所有值为 value 的元素

    `llen key` 查看列表中元素数量

4. set 集合

    无序, 不允许有重复元素. <br>
    `sadd hobbies:user:1` 设置 user1 的爱好

    `sadd key element ...` 为 key 添加一个集合 <br>
    `exists key` 查看此 key 是否存在 <br>
    `srem key element ...` 删除 key 中的某个元素 <br>
    `scard key` 查看该 key 下元素的个数 <br>
    `sismember key element` 判断某元素是否在集合中 <br>
    `smembers key` 查看该 key 下的所有元素 <br>

    可同时处理多个集合的交集/并集/差集 <br>
    `SDIFF key1 [key2]` 所有集合的差集 (前减后 key1 剩下的元素) <br>
    `SINTER key1 [key2]` 所有集合的交集 <br>
    `SUNION key1 [key2]` 所有集合的并集 <br>
    `SSCAN key cursor [MATCH pattern] [COUNT count] ` 迭代集合中的元素 <br>

5. zset 有序集合

    zadd key score member [score member...] <br>
      > `zadd hotsearch 100 liyong 99 LOL 101 xiaomi` score 即权重, 实现可排序, 数字越小越靠前 <br>

    `zcard key` 查看集合数量 <br>

    `zrange key start end` 按正序显示所有对象 <br>
    `zrevrange key start end` 按倒序显示所有对象 <br>
    `zincrby key score member` 该 key 下的某个成员自增权重 n <br>
    `zscore key member` 返回有序集中，成员的分数值 <br>
    `zrem key member [member ...]` 移除有序集合中的一个或多个成员 <br>
