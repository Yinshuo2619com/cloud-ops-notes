# 15 云数据库Mysql-V0.5

---

## 📋 内容汇总
本文档全面介绍了云数据库MySQL的产品概念、技术架构、操作管理以及最佳实践。涵盖MySQL基础（安装、SQL操作、备份还原）、云数据库MySQL架构（主从复制、高可用）、数据复制方式（异步、半同步、强同步）、备份恢复机制以及只读实例等核心内容。

## 🎯 知识要点
- MySQL特性：多用户多线程、ACID事务支持、多种存储引擎、开源免费
- 云数据库架构：主实例（一主一备）、只读实例（RO组负载均衡）、灾备实例（跨Region容灾）
- 数据复制方式：异步复制（高性能）、半同步复制（折中方案）、强同步复制（数据强一致）
- 高可用机制：HA自动检测故障，秒级切换，支持异步/半同步/强同步复制
- 备份方式：物理备份（基于文件）和逻辑备份（基于mysqldump），均在备库执行
- 连接方式：内网连接（免费）、公网连接（高风险）、专线连接、控制台连接
- 参数管理：全局参数永久生效，会话级参数当前会话生效
- 只读实例：RO组负载均衡，支持延迟剔除，按新连接请求分配

## 💡 重点难点
- 三种复制方式对比：异步（性能高但可能数据不一致）、半同步（折中）、强同步（一致但可能阻塞）
- 物理备份与逻辑备份区别：物理备份基于目录文件，逻辑备份基于mysqldump，适用场景不同
- 回档机制：基于冷备和binlog实现任意时间点回档，支持普通/快速/急速三种回档方式
- 安全组配置：云数据库无主动出站流量，需放通3306内网端口实现外网访问
- 参数调优：lower_case_table_name（大小写敏感）、innodb_large_prefix（索引长度限制）等关键参数

---

## 第 1 页

云数据库Mysql
产品培训


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

Mysql介绍
MySQL是由瑞典 MySQL AB 公司开发，属于 Oracle 旗下产品，是最流行的关系型数据库管理系统之一，因其开源、高效、可靠、易于使用以及丰富的功能而受到广泛欢迎。
它是LAMP（Linux + Apache + MySQL + PHP/Perl/Python）堆栈的重要组成部分，这使得它成为构建Web应用程序的理想选择。
MySQL数据库具有以下特性：
多用户和多线程：MySQL支持多个用户同时访问数据库，并且可以同时处理多个线程的请求，提高了数据库的并发性能。
支持多种操作系统：MySQL可以运行在各种操作系统上，包括Windows、Linux、Unix等。
灵活的存储引擎：MySQL支持多种存储引擎，如InnoDB、MyISAM、Memory等，可以根据具体需求选择适合的存储引擎。
ACID事务支持：MySQL支持ACID（原子性、一致性、隔离性和持久性）事务，确保了数据的一致性和完整性。
高性能和可扩展性：MySQL通过优化查询执行计划、索引和缓存机制等方式提高了数据库的性能，并且可以通过主从复制、分片等方式进行水平和垂直扩展。
数据库复制和备份：MySQL支持数据库的复制和备份，可以将数据复制到其他服务器，实现数据的冗余和灾备。
安全性：MySQL提供了对数据进行加密和访问控制的功能，可以保护数据的安全。
开源免费：MySQL是开源的数据库管理系统，可以免费使用，并且有活跃的开源社区提供支持和更新。


## 第 5 页

Mysql安装--CentOS7系统环境
添加 MariaDB yum 官网仓库
vi /etc/yum.repos.d/MariaDB.repo
[mariadb]
name = MariaDB
baseurl = https://mirror.mariadb.org/yum/10.11/rhel/$releasever/$basearch
module_hotfixes = 1
gpgkey = https://mirror.mariadb.org/yum/RPM-GPG-KEY-MariaDB
gpgcheck = 1
yum -y install MariaDB-client MariaDB-server
或使用其他第三方yum源，如 腾讯云
[mariadb]
name = MariaDB
baseurl = https://mirrors.cloud.tencent.com/mariadb/yum/10.11/rhel/$releasever/$basearch
module_hotfixes = 1
gpgkey = https://mirrors.cloud.tencent.com/mariadb/yum/RPM-GPG-KEY-MariaDB
gpgcheck = 1
登录Mariadb数据库并设置密码
mysql -h  192.168.1.2  -u root -p
ALTER USER root@localhost IDENTIFIED VIA mysql_native_password USING PASSWORD('输入您的密码');


## 第 6 页

Mysql操作--增删改查&&备份还原
create database xuexiao;
use xuexiao;
create table chengji (
StudentID INT,
StudentName VARCHAR(100),
CourseName VARCHAR(100),
Score DECIMAL(4)
);
INSERT INTO chengji (StudentID, StudentName, CourseName, Score) VALUES
(1, 'zhangsan', 'shuxue', 92),
(2, 'lisi', 'shuxue', 88),
(3, 'wangwu', 'shuxue', 90);
INSERT INTO chengji (StudentID, StudentName, CourseName, Score) VALUES (4, 'zhuliu', 'shuxue', 92);
INSERT INTO chengji  VALUES (5, 'zhuoqi', 'shuxue', 92);
SELECT * FROM chengji;
SELECT * FROM chengji WHERE StudentID = 1;
UPDATE chengji SET Score = 95 WHERE StudentID = 1 AND CourseName = 'shuxue';
DELETE FROM chengji WHERE StudentID = 1 AND CourseName = 'shuxue';
DROP TABLE chengji;
DROP DATABASE xuexiao;
mysqldump -u root -p xuexiao > xuexiao_backup.sql    （备份xuexiao库）
mysql -u root -p < xuexiao_backup.sql   （注意：还原之前要先创建对应的库）


## 第 7 页

云数据库Mysql介绍
云数据库Mysql是基于开源数据库Mysql打造的可弹性扩展、高可靠、高性能的PAAS产品。


## 第 8 页

云数据库Mysql架构
云数据库Mysql提供主实例、只读实例、灾备实例，充分为客户的业务保驾护航。
只读实例：
1.推荐2个以上RO，故障容灾
2.支持延迟自动剔除
主实例：
1.一主一备架构，秒级故障切换
2.主备可在不同Zone
灾备实例：
1.一主一备架构，跨Regionz容灾
2.内网专线同步，低时延


## 第 9 页

云数据库Mysql高可用架构
高可用：一主一从/一主两从；
HA：宕机自动检测，故障自动转移，故障秒级切换；
复制方式：异步、半同步、强同步；
云数据库提供物理备份和逻辑备份两种方式：物理备份是基于目录文件的，逻辑备份是基于mysqldump的。
控制台的备份都是在备库上操作的，不影响主库的性能。


## 第 10 页

云数据库Mysql的架构、版本、功能


## 第 11 页

主从数据复制方式--异步复制
异步复制（一主一从）
特点：主库仅保证自己的事务完成，并告知slave有新事务，即返回客户端。 即master无需等待slave的IO线程拉取binlog到relaylog中，主库完成即完成。异步复制拥有较高的性能，slave不可用不影响master对外提供服务，但由于数据并非实时同步，在发生故障切换时可能会出现数据不一致情况。


## 第 12 页

主从数据复制方式--半同步复制
半同步复制（一主一从）
特点：主库的事务完成后，并告知slave有新事务，slavede IO线程拉取binlog到自己的relaylog后，向master返回成功信息，master接受到成功信息后再向应用程序返回响应。说明：半同步复制再（slave节点不可用或者数据复制网络异常）的情况下，master暂停对应用的响应，复制方式变更为异步复制，当数据复制恢复正常，将恢复为半同步复制。


## 第 13 页

主从数据复制方式--强同步复制
强同步复制（一主两从）
特点：主库的事务完成后，并告知slave有新事务，仅需一台slave在接受到数据并执行完成后向master返回成功信息，master接受到slave成功信息后向应用程序返回响应。
master需等待slave的IO线程拉取binlog到relaylog中，sql线程回放binlog完成之后，主库才能向应用程序返回结果。
说明：强同步复制需要保证slave也执行成功，因此最大限度的保障了主从数据的一致性；但如果没有返回日志接受完成，会无限等待，会阻塞用户新的事务。


## 第 14 页

2
应用场景 & 计费指南
PART TWO


## 第 15 页

应用场景--单节点中小型网站架构
性价比
单节点实例具有更低的价格，底层存储采用增强型 SSD 云硬盘，性能表现优异。
快速变配
计算、存储可实现快速扩容


## 第 16 页

应用场景--集群式电商网站架构
高性能
解决热点数据高并发性能瓶颈
快速弹性扩展应对业务突发高峰
高可用
数据强一致
降低单点故障带来的损失
高安全
有效防止恶意攻击、数据泄漏或人为操作不当引发的安全风险问题


## 第 17 页

计费指南
内存和CPU：按照大小和核心数计费
存储空间：按照购买的硬盘空间大小计费
备份费用：按照备份的数据大小计费
流量费用：开通公网后，按照公网带宽流量费用计费
总费用 = 内存和CPU费用 + 存储空间费用 + 备份空间费用 + 流量费用


## 第 18 页

3
操作指南 & 最佳实践
PART THREE


## 第 19 页

创建云数据库Mysql


## 第 20 页

连接云数据库Mysql
连接方式 | 应用场景 | 计费
--- | --- | ---
内网 | CVM 和 MySQL 属于同一网络 | 免费
公网 | 客户端工具通过公网连接 （高风险） | 公网流量费用
专线 | 物理专线、虚拟专线 内网访问 | 专线费用
控制台 | 云服务商一般会提供控制台直接登录 | 由云服务商决定是否计费
数据库Mysql客户端连接工具：MySQL Workbench、Mysql-Client、Navicat、PHPMyAdmin 等


## 第 21 页

云数据库Mysql基础信息


## 第 22 页

云数据库Mysql管理--控制台登录管理
1.支持添加库和表、支持实例监控、查看实时会话、执行sql、kill会话。
2.支持视图、存储过程、函数、触发器、事件的单独展示。
3.通过返回旧版，可跳转至PMA（phpmyadmin）界面，对数据库进行管理。


## 第 23 页

云数据库Mysql管理--数据库列表
1、高可用版控制台支持导入功能，单个文件最大支持2GB，且文件名称必须是sql。
2、支持创建数据库，通过此页面跳转至DMC控制台，管理数据库。


## 第 24 页

云数据库Mysql管理--参数设置与修改
控制台提供的参数均为全局参数，修改之后永久生效；
控制台未提供的参数为会话级参数，部分参考可通过命令行进行修改，当前会话生效。


## 第 25 页

云数据库Mysql管理--参数修改
修改大小写敏感  lower_case_table_name
lower_case_table_names参数如果设置为0，表名将按指定存储，区分大小写。 如果设置为1，它们将以小写形式存储在磁盘上，不区分大小写。
查大写的表名
select table_schema,table_name from information_schema.tables where table_schema not in("mysql","information_schema") and (md5(table_name)<>md5(lower(table_name)) or md5(table_schema)<>md5(lower(table_schema)));
修改sql_mode
https://example.com/docs/mysql-optimization-guide
修改 innodb_strict_mode
InnoDB表的默认限制列是1017列（从mysql 5.6.9开始）；Mysql的表的行最大默认是65535 Bytes；除此之外，CDB for Mysql默认innodb_page_size为16k，InnoDB表（stored locally in database page）最大行不超过其值一半即8126bytes。
a.修改表的语句，使其符合行大小限制，例如列格式为TEXT or BLOB
b. 关闭 innodb_strict_mode 对行大小的校验
修改 innodb_large_prefix
由于 MySQL Innodb 引擎表索引字段长度的限制为 767 字节，因此对于多字节字符集的大字段（或者多字段组合索引），创建索引会出现max key length is 767bytes的错误。
以 utf8mb4 字符集 字符串类型字段为例：utf8mb4 是 4 字节字符集，则默认支持的索引字段最大长度是： 767 字节 / 4 字节每字符 = 191 字符，因此在 varchar(255) 或 char(255) 类型字段上创建索引会失败。
#  Innodb_large_prefix 修改为 on 后，对于 Dynamic 和 Compressed 格式的InnoDB 引擎表，其最大的索引字段长度支持到 3072 字节


## 第 26 页

云数据库Mysql管理--账号设置
1、支持创建账号时，指定多个主机地址，以分隔符分开；但实际创建完成之后，相同账号不同主机将分开展示。
2、root@% 系统初始化即存在的账户，不支持删除，支持重置密码。
3、修改权限，包括全局特权、对象级特权、表级特权。


## 第 27 页

云数据库Mysql安全组
1、基础版Mysql不支持安全组
2、Mysql安全组仅支持私有网络，基础网络需要加白
3、云数据库Mysql没有主动出站流量，因此出站规则对云数据库不生效
4、云数据库外网端口是通过内网端口映射实现的，若需要放通外网访问，需要入站放通3306（内网）端口


## 第 28 页

云数据库Mysql备份/恢复
备份设置：自动备份（物理）和手动备份（物理or逻辑）。
备份文件：数据备份（xb文件）和日志备份（binlog）。
备份方式：物理备份（基于物理文件）和逻辑备份（基于mysqldump）。
物理备份和逻辑备份均是先通过qpress压缩，然后经过xbstream 打包，生成的xb文件。
使用物理备份恢复数据库（以腾讯云为例）：https://cloud.tencent.com/document/product/236/33363
使用逻辑备份恢复数据库（以腾讯云为例）：https://cloud.tencent.com/document/product/236/33364
需注意：
1.自动物理备份是无法手动删除的，对标数据备份保留时间，逾期自动清除；手动备份可以进行删除。
2.日志备份可能会出现控制台展示为空的情况，该情况是由于日志未写满的原因。
官方逻辑：a.binlog写满256M进行一次切割 b.已经切割的binlog文件上传COS c.控制台上显示上传到COS的binlog文件。
方案：可在mysql命令行执行flush logs刷新日志，三分钟左右即可在控制台看到binlog文件。
3.备份均在备库上进行，不会影响主库的读写。


## 第 29 页

云数据库Mysql备份/恢复--自动备份


## 第 30 页

云数据库Mysqll备份/恢复--手动备份
1、支持手动逻辑备份和物理备份
2、物理备份只支持整实例备份
3、逻辑备份支持整实例和指定库表
4、逻辑备份指定库表方式只备份数据结构与数据内容，暂不支持视图、存储过程、用户自定义函数、触发器等内容。


## 第 31 页

云数据库Mysqll备份/恢复--回档
回档基于冷备和binlog，可以选择备份保留时间范围内的任意时间点进行回档，回档不会覆盖原库表，可以自定义回档后的库和表名。
普通回档
回档整个实例数据到指定时间点，数据全、耗时长
快速回档
回档单库到指定时间点，耗时较快，但是有跨库的约束也不适用
急速回档
回档指定表到单个时间点，速度最快，但是跨表的约束不适用


## 第 32 页

云数据库Mysql--只读实例
只读实例：可以支持读请求的单节点（无从机）实例。只读实例无法单独存在，每个只读实例都属于某个 RO 组。
RO 组：带有负载均衡功能的只读实例组。若 RO 组内有多个只读实例，可将用户读请求量均匀分配到组内的每个只读实例上。且 RO 组会对外提供 IP、PORT 供访问数据库。
RO组内支持延迟剔除，待延迟恢复到阈值后，自动加入。
均衡不是根据实例目前的链接数，而是根据新的连接请求，分配连接，比如来了5个新的连接，第一个连接会分到第一个只读实例，第二个连接会分到第二个只读实例，第三个连接会分到第三个只读实例，依次类推。


## 第 33 页

最佳实践
1、购买一个按量计费的云数据库，开通公网，使用Mysql客户端工具远程连接；
2、购买一个和MySQL同一网络的云服务器，在云服务器上远程登录数据库；
3、在云数据库里创建一个库，并展示输出出来；
4、在上面创建的数据库里面创建一个table，在table里面插入数据，将插入的数据展示出来；
5、备份数据库（物理备份和逻辑备份）
6、修改数据库内容
7、然后回档到修改前的状态；
8、使用sql语句查询插入的数据；
9、将数据库里面创建库数据导出到本地；
10、将导出的数据库重新上传导入到数据库。


## 第 34 页

4
常见问题
PART FOUR


## 第 35 页

常见问题
1、如何选择合适的云数据库 MySQL 版本及存储引擎？
2、如何连接云数据库 MySQL 实例？
3、数据库基础操作：增删改查
4、云数据库 MySQL 全表扫描的 SQL 语句有什么方法可以找到吗？
5、如何授权某个主机地址的客户端对云数据库 MySQL 的访问？
6、如何导出数据库数据？
7、如何把本地的 SQL 文件导入到 MySQL 数据库？
8、如何查看 binlog 日志？
9、我想查看最新的 binlog 日志，怎么办？
10、binlog 日志如何备份？


## 第 36 页

懂云 更懂服务
