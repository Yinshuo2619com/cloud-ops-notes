# 16 云数据库Redis-V0.5

---

## 📋 内容汇总
本文档全面介绍了云数据库Redis的产品概念、技术架构、操作管理以及最佳实践。涵盖Redis基础（安装、数据类型、操作命令）、云数据库Redis架构（标准版、集群版）、Proxy功能、监控指标以及常见问题排查，是Redis缓存数据库的完整指南。

## 🎯 知识要点
- Redis特性：高性能（读11万次/写8万次/秒）、丰富数据类型（string/list/set/zset/hash）、原子性操作
- 云数据库架构：标准版（1-9副本，读写分离）、集群版（1-128分片，水平扩展）
- Proxy功能：路由分配、兼容主从集群、变更分片无感、多数据库支持、副本只读
- 版本兼容：支持Redis 2.8/4.0/5.0/6.2/7.0等多版本协议
- 连接方式：内网连接（免费）、公网连接（高风险）、专线连接、控制台连接
- 备份机制：自动备份与手动备份，支持外网/内网下载，实例克隆功能
- 监控指标：key驱逐、key过期、大Value请求、读写请求延迟等关键指标
- 安全配置：disable-command-list禁用危险命令（flushdb/flushall/keys）

## 💡 重点难点
- 标准版与集群版区别：标准版适合中小规模，集群版支持千万级QPS水平扩展
- Proxy核心作用：路由分配、读写分离、分片无感迁移、多数据库访问的关键组件
- key驱逐机制：内存满时按maxmemory-policy策略淘汰key，no-eviction策略会导致OOM
- 大Key优化：识别和处理超过32KB的大Value请求，避免性能瓶颈
- 慢查询分析：区分Redis节点慢查询与Proxy节点慢查询，定位性能问题

---

## 第 1 页

云数据库Redis


## 第 2 页

目录
2、应用场景 & 计费指南
3、操作指南 & 最佳实践
1、产品介绍 & 原理架构
4、常见问题


## 第 3 页

1
产品介绍 & 原理架构
PART ONE


## 第 4 页

Redis 介绍
Redis 是一个高性能（NOSQL）的key-value数据库，是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。Redis是完全开源免费的，遵守BSD协议。
Redis特点：
● 性能极高 – Redis能读的速度是110000次/s,写的速度是81000次/s 。
● 丰富的数据类型 – Redis支持的类型包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。
● 原子性 – Redis的所有操作都是原子性的，意思就是要么成功执行要么失败完全不执行。单个操作是原子性的。多个操作也支持事务，即原子性，通过MULTI和EXEC指令包起来。
● 丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性。
● 高速读写，redis使用自己实现的分离器，代码量很短，没有使用lock（MySQL），因此效率非常高。


## 第 5 页

Redis 安装
第三方仓库源：（适用RHEL7系列）rpm -Uvh http://rpms.remirepo.net/enterprise/remi-release-7.rpm
yum-config-manager --enable remi
yum install redis
官网仓库源：（适用RHEL系列）
vi  /etc/yum.repo.s/redis.repo
[Redis]
name=Redis
baseurl=https://packages.redis.io/rpm/rockylinux10
enabled=1
gpgcheck=1
gpgkey=https://packages.redis.io/gpgyum install redis


## 第 6 页

Redis 操作
其他Redis练习
# 创建一个新的用户并设置密码（需Redis6.0及以上版本支持）
acl setuser username on >password +@all
如：acl setuser admin on >YourRedis@Password123 +@all
username 是你想创建的用户名。
password 是分配给该用户的密码。
+@all 表示赋予该用户所有权限。
# 用户创建后，可以使用以下命令进行身份验
auth admin YourRedis@Password123
# info stats：获取统计信息# 包含了缓存的命中和未命中次数
#（keyspace_hits / keyspace_misses）
# 或系统命令：
redis-cli -a YourRedis@Password123 info stats | grep keyspace_hits
redis-cli -a YourRedis@Password123 info stats | grep keyspace_misses
# Redis默认空密码，首次登录后需设置密码
redis-cli
config set requirepass YourRedis@Password123
config rewrite
config get requirepass
quit
# 使用密码登录（推荐第一种）
redis-cliauth YourRedis@Password123
或redis-cli -a YourRedis@Password123
# Redis基础练习
help
set name zhangsan
get name


## 第 7 页

Redis 应用
3、赋予Wordpress网站目录权限
cd /usr/share/nginx/html/wordpresschmod -R 777  wp-content/
4、登录Wordpress网站后台：
安装插件Redis Object Cache，并启用即可。
5、若有报错或告警或缺少组件，可重启所有服务后重试
systemctl restart nginx
systemctl restart php-fpm
systemctl restart mariadb
systemctl restart redis
# WordPress启用Redis
1、安装php扩展
yum install php-pecl-redis6
systemctl restart php-fpm
2、修改WordPress配置文件添加Redis连接
vi wp-config.php
# redis连接信息：
# 一定要放到数据库连接信息后面才能生效
/*redis config*/
define('WP_REDIS_HOST', '127.0.0.1');
define('WP_REDIS_PORT', '6379');
define('WP_REDIS_PASSWORD', 'YourRedis@Password123');
# 如下权限设置，放到最后即可
define("FS_METHOD", "direct");
define("FS_CHMOD_DIR", 0777);
define("FS_CHMOD_FILE", 0777);


## 第 8 页

云数据库Redis 介绍
云数据库 Redis是基于原生Redis打造，100%兼容社区版本，具有高可用、高性能、高弹性等特征，兼容redis2.8、redis4.0、redis5.0、redis6.2、redis7.0等版本协议。提供标准版和集群版。
高吞吐：标准版性能高达10万+ QPS 并发响应，集群版随着分片线性增长，最大支持千万级 QPS。
低时延：提供稳定的低时延响应，50%负载以内99%响应时延 < 1ms，90%负载以内响应时延 < 10ms。
读扩展：热 Key 场景，支持动态增加副本扩展读性能，最大支持9副本，提供最大50W的热 Key 读取性能。
写扩展：集群版的性能 = 分片数 * 单分片性能。性能水平扩展线性增长，支持从3分片到128分片扩展。


## 第 9 页

版本与功能


## 第 10 页

架构--标准版
1、支持1-9个副本（副本指非主节点的节点）
2、兼容 Redis 4.0、Redis 5.0、Redis 6.2 版本、Redis 7.0 版本的协议和命令
3、主节点提供日常服务，从节点提供HA高可用
4、副本大于等于1时，可开启读写分离


## 第 11 页

架构--集群版
1、支持1-9个副本（副本指非主节点的节点）
2、支持1-128分片的水平方向扩展
3、故障自动切换、读写分离
4、自动启动分片模式，提供数据均衡、迁移功能
5、跨 Slot（槽位）数据访问
6、命令兼容性


## 第 12 页

Proxy 作用
功能 | 说明
--- | ---
路由 | Proxy 维护与后端数据分片的持久连接，承担着请求的均衡分配与精确路由的关键角色，确保请求的高效处理和系统的稳定运行。
兼容主从集群访问模式 | 当业务增长超出了标准架构的承载能力时，您可以轻松地将数据迁移到配备了 Proxy 的集群架构中，非多 Key 操作的情况下都可以兼容，无缝实现架构转换，显著减少了业务调整的成本，确保了业务的平滑过渡和持续增长
变更分片 无感 | Proxy 本身并不直接调整数据分片的数量和大小，而是通过与后端的数据分片管理机制协同工作来实现数据分片弹性伸缩。在数据分片需要迁移（如扩容或缩容操作）时，Proxy 可以支持透明的数据迁移过程，确保客户端操作不受影响。
多数据库（DB） | 原生 Redis 及其 Cluster 客户端通常仅支持单一数据库操作，且默认情况下仅使用数据库0，同时不提供对`SELECT`命令的支持。通过使用 Proxy 来连接集群实例，可以实现对多个数据库的访问，并且能够使用`SELECT`命令在不同的数据库间切换。在集群版中，每个实例默认提供高达256个数据库，为多数据库功能提供了强大支持。
副本只读 | 读写分离架构，Proxy 持续监控只读节点的运行状况，以便在特定情况下实施流量管理措施
多账号 权限控制 | Proxy 可以根据用户设定的权限来控制用户能够执行的命令类型。不同的账户能执行他们被授权的命令。


## 第 13 页

2
应用场景 & 计费指南
PART TWO


## 第 14 页

应用场景
互联网、App 应用产品中，可以将用户的基础资料缓存至 Redis 中，提高读写性能。
同时也可以将静态的图片，资源缓存到 Redis 中，提高应用加载速度。


## 第 15 页

应用场景
电商展示中，可以将商品展示、购物推荐等数据存储在 Redis 中进行快速访问。
同时在大型促销秒杀活动中，Redis 达千万级的 QPS 能轻松应对高并发访问。


## 第 16 页

计费指南
内存版（标准架构）费用 = 单节点内存 * 单节点定价 * 节点数量
如1主1副本 = 2个节点
内存版（集群架构）费用 = 单分片内存 * 单分片定价 * 节点数量 * 分片数量
如1主1副本 = 2个节点
01
02
计费


## 第 17 页

3
操作指南 & 最佳实践
PART THREE


## 第 18 页

创建云数据库Redis--以腾讯云数据库Redis为例


## 第 19 页

Redis控制台--连接Redis
连接方式 | 应用场景 | 计费
--- | --- | ---
内网 | 云服务器 和 Redis 属于同一网络 | 免费
公网 | 客户端工具通过公网连接 （高风险） | 公网流量费用
专线 | 物理专线、虚拟专线 内网访问 | 专线费用
控制台 | 云服务商一般会提供控制台直接登录 | 由云服务商决定是否计费
云数据库Redis客户端连接工具：redis-cli、RedisInsight、Tiny RDM 等


## 第 20 页

Redis控制台--实例详情
支持更换网络、升级版本、带宽调整 等操作


## 第 21 页

Redis控制台--大Key优化策略


## 第 22 页

Redis控制台--账号管理
支持多账号、支持副本只读


## 第 23 页

Redis控制台--参数配置
如重点参数 disable-command-list， 可通过禁用flushdb   flushall   keys  等命令。


## 第 24 页

Redis控制台--备份与克隆
云数据库 Redis支持自动备份和手动备份，并提供外网与内网两种备份下载地址，可根据需求选择下载方式。
外网下载：您在任意可以访问 Internet 网络的地方可下载备份数据。默认开启外网下载，可根据场景选择性关闭外网下载功能。
内网下载：为防止数据库备份数据被拖库，云数据库 Redis支持在用户允许的内网指定具体的服务器下载备份文件。
云数据库 Redis内存版支持实例克隆功能，即基于当前实例克隆一个完整的新实例，数据保持一致。


## 第 25 页

Redis控制台--慢查询
云数据库 Redis慢查询分别统计了通过 Redis 与 Proxy 节点的慢查询统计，包含慢查询的命令、产生时刻、请求耗时以及具体详情。
Redis 慢查询：统计请求经过 Redis 节点产生的慢查询请求。
Proxy 慢查询：统计指经过中间件代理节点所产生的慢查询请求。


## 第 26 页

Redis控制台--节点管理
云数据库 Redis支持多节点的管理，包括：调整节点规格、副本节点提升为主节点、开启副本只读、主备故障切换。
运维人员通过节点管理可以高效管理实例节点、定位节点运行过程中的相关异常。


## 第 27 页

Redis控制台--监控概览
1、支持实时监控
2、支持热key展示
3、支持访问命令TOP
4、支持CPU消耗命令TOP
5、支持时延分布
6、支持key大小分布
7、支持key类型分布


## 第 28 页

Redis控制台--监控指标
多维度监控：实例监控、节点监控（主从）、proxy监控
重点监控指标：
key驱逐：出现key驱逐表明内存已满，按照maxmemory-policy的策略，进行key驱逐。
key过期：expire设置的key过期
大 Value 请求：请求命令大小超过32KB的执行次数。
读/写请求延迟：Proxy 到 Redis Server的读写时延。


## 第 29 页

最佳实践
1、用一台云服务器手动安装Redis，本地登录；2、设置Redis密码，并通过内外网连接登录；
3、云服务器部署WordPress网站并启用Redis；
4、访问WordPress看是否命中Redis缓存；
5、购买一台云数据库Redis，并通过内外网连接登录；
6、WordPress配置启用云数据库Redis。


## 第 30 页

4
常见问题
PART FOUR


## 第 31 页

Redis常见问题
1、Redis是用来做什么的？
2、Redis有哪些优势？
3、如何连接 Redis 实例？
4、账号误删或忘记密码怎么办？
5、Redis的到底是缓存还是存储？
6、Redis的持久化数据是怎么存储的？
7、如何下载云数据库 Redis® 的客户端？
8、数据保存在云数据库 Redis® 是否可靠？


## 第 32 页

Redis问题分析
【问题描述】Redis连接打满：应用连接redis，提示too many connections
【原因分析】
1、内存占用率过高2、业务侧连接泄露，大量连接持续接入
3、慢查询导致请求堆积。
【解决方法】
1、重启客户端释放连接
2、通过client list 定位异常的IP进行临时限制
3、手动调整连接数或者发起HA切换
【问题描述】
Redis出现OOM：用户反馈redis实例报out of memory
【原因分析】
1、查看内存利用率情况
2、key驱逐情况
3、淘汰策略maxmemory-policy  设置为了 no-eviction
4、QPS过高内存规格较小
【解决方法】
1、调整参数maxmemory-policy，开启内存淘汰机制
2、及时对实例进行扩容操作


## 第 33 页

懂云 更懂服务
