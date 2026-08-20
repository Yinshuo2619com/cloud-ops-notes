# 微专业-云数据库 考试题库

> 来源：青书云考试平台
> 共40题：30道单选题 + 10道多选题
> ⚠️ 注意：本试卷为正在进行的考试，API未返回正确答案，以下答案为参考答案（基于专业知识判断）
## 答案仅供参考（96分）
---

## 一、单选题（1-30题，每题2分）

### 1. MySQL数据库的默认端口号是多少
- **A. 3306 ✅**
- B. 1433
- C. 5423
- D. 1521

### 2. 在MySQL数据库中，以下哪个命令可以创建一个新的数据库
- A. CREATE VIEW
- B. CREATE INDEX
- C. CREATE TABLE
- **D. CREATE DATABASE ✅**

### 3. 在MySQL数据库中，以下哪个命令可以删除一个数据库
- **A. DROP DATABASE ✅**
- B. DELETE DATABASE
- C. DELETE TABLE
- D. DROP TABLE

### 4. 在MySQL中，以下哪个命令可以向表中插入一条新的记录
- A. SELECT
- B. UPDATE
- **C. INSERT INTO ✅**
- D. DELETE

### 5. 在MySQL中，以下哪个命令可以更新表中的记录
- A. DELETE
- **B. UPDATE ✅**
- C. SELECT
- D. INSERT INTO

### 6. 在MySQL中，以下哪个命令可以查询表中的记录
- A. UPDATE
- B. DELETE
- C. INSERT INTO
- **D. SELECT ✅**

### 7. 在MySQL中以下哪个命令可以创建一个新的表
- A. CREATE VIEW
- B. CREATE DATABASE
- **C. CREATE TABLE ✅**
- D. CREATE INDEX

### 8. 在MySQL中，用于创建新用户的命令是
- A. ADD USER
- B. MAKE USER
- **C. CREATE USER ✅**
- D. INSERT USER

### 9. 在MySQL中，用于授予用户权限的命令是
- A. PERMIT
- B. ALLOW
- **C. GRANT ✅**
- D. AUTHORIZE

### 10. 在MySQL中显示所有数据库的命令是
- A. LIST DATABASES
- **B. SHOW DATABASES ✅**
- C. SHOW DATABASE
- D. LIST DATABASE

### 11. 查看student表结构的SQL语句是
- **A. describe student ✅**
- B. show student
- C. show create student
- D. show create table student

### 12. 为student表增加一个年龄字段的正确语法是
- A. add sAge to student
- B. change table student add sAge
- C. alter table student add sAge
- **D. alter table student add sAge int ✅**

### 13. 恢复数据库下列命令正确的是
- **A. mysql -u root -p < /root/backup.sql ✅**
- B. mysql -u root -p > /root/backup.sql
- C. mysqldump -u root -p > /root/backup.sql
- D. mysqldump -u root -p < /root/backup.sql

### 14. 远程连接MySQL数据库使用下列格式正确的是
- A. mysql -u -proot
- **B. mysql -h 远程主机名 -u root -p ✅**
- C. mysql -u root -p
- D. mysql root -u -p

### 15. 在数据库Mysql中存储的是
- A. 数据模型
- B. 信息
- **C. 数据以及数据之间的关系 ✅**
- D. 数据

### 16. 在MySQL配置文件中，用于设置监听地址的配置项是
- A. server_address
- **B. bind_address ✅**
- C. host_address
- D. listen_address

### 17. 在MySQL配置文件中，用于设置数据目录的配置项是
- **A. data_dir ✅**
- B. database_dir
- C. storage_dir
- D. db_dir

### 18. 在MySQL配置文件中，用于设置最大连接数的配置项是
- **A. max_connections ✅**
- B. connections_limit
- C. max_clients
- D. clients_limit

### 19. Redis是一种什么类型的数据库
- **A. 非关系型数据库 ✅**
- B. 文件型数据库
- C. 关系型数据库
- D. 对象型数据库

### 20. Redis支持哪种数据结构
- A. 仅支持键值对
- B. JSON文档
- **C. 字符串、哈希、列表、集合、有序集合 ✅**
- D. 二进制大对象（BLOB）

### 21. Redis的默认端口号是多少
- A. 6360
- **B. 6379 ✅**
- C. 6378
- D. 6380

### 22. 什么是 Redis 数据持久化
- **A. 将数据保存到硬盘上以防止数据丢失 ✅**
- B. 将数据存储在内存中以提高读取速度
- C. 一种数据压缩算法
- D. 将数据加密以确保安全性

### 23. 在Redis启动后，如何通过命令行客户端连接到Redis服务器
- A. redis-cli connect <ip> <port>
- B. redis-client <ip> <port>
- C. redis-connect <ip> <port>
- **D. redis-cli -h <ip> -p <port> ✅**

### 24. 在Redis中，用于设置键值对的命令是
- A. POST
- B. PUT
- **C. SET ✅**
- D. GET

### 25. Redis中的"GET"命令用于做什么
- A. 设置键的值
- B. 修改键的值
- **C. 获取键的值 ✅**
- D. 删除键

### 26. Redis中的"AUTH"命令用于做什么
- A. 用户注销
- B. 用户注册
- **C. 用户认证 ✅**
- D. 设置用户权限

### 27. Redis中的"SCAN"命令用于做什么
- A. 管理并发访问
- B. 执行全文搜索
- **C. 扫描数据库中的所有键 ✅**
- D. 进行内存管理

### 28. 在Redis配置文件中，用于设置监听地址的配置项是
- **A. bind ✅**
- B. port
- C. listen
- D. address

### 29. 在Redis配置文件中，用于设置密码的配置项是
- A. password
- **B. requirepass ✅**
- C. auth
- D. authenticate

### 30. MySQL主从架构的主要目的是
- **A. 实现数据库的高可用性和容错性 ✅**
- B. 简化数据库的管理和维护
- C. 提高数据库的安全性
- D. 提高数据库的性能

---

## 二、多选题（31-40题，每题4分）

### 31. 如下那些是数据库Mysql的特点
- **A. 支持结构化数据存储 ✅**
- **B. 支持跨平台 ✅**
- **C. 提供了强大的数据完整性和一致性的机制 ✅**
- **D. 开源免费 ✅**
> 参考答案：A,B,C,D

### 32. 在MySQL读写分离架构中（主从架构），如下描述正确的是
- **A. 读库处理读请求 ✅**
- **B. 主库处理写请求 ✅**
- **C. 主库和从库可以进行故障切换 ✅**
- **D. 从库可以有多个，采用负载均衡技术实现轮询访问 ✅**
> 参考答案：A,B,C,D

### 33. 如下关于腾讯云数据库Mysql产品特性描述正确的是
- **A. 定时冷备、增量备份、实时热备保障数据可靠 ✅**
- **B. 基于TXSQL内核开发 ✅**
- **C. 加密、审计等内核支持的特性 ✅**
- **D. 丰富的数据库管理能力：备份、回档、监控、升级、迁移等 ✅**
> 参考答案：A,B,C,D

### 34. 如下关于数据库Mysql OOM的描述正确的是
- A. cpu 或 硬盘资源不足也会导致oom
- **B. 配置不当会导致出现oom ✅**
- **C. oom是指out of memory ✅**
- **D. oom就是内存资源不够用了 ✅**
> 参考答案：B,C,D

### 35. 下面关于存储引擎的描述正确的是（）
- **A. 使用SHOW ENGINES语句可以查看系统支持的数据库引擎类型 ✅**
- **B. 不同的存储引擎所具备的能力也有所不同 ✅**
- **C. 存储引擎就是存储数据、为存储的数据建立索引和更新、查询数据等技术的实现方法 ✅**
- **D. MySQL数据库提供了多种存储引擎类型，用户可以根据不同的需求为数据表选择不同的存储引擎 ✅**
> 参考答案：A,B,C,D

### 36. MySQL可以存储的的数据类型有那些
- **A. 数值类型 ✅**
- **B. 文本类型 ✅**
- **C. 日期和时间类型 ✅**
- **D. 字符串类型 ✅**
> 参考答案：A,B,C,D

### 37. 以下关于数据库Mysql说法正确的是
- **A. MySQL服务的安装路径可以修改 ✅**
- **B. MySQL数据文件的存放位置可以修改 ✅**
- **C. MySQL数据库的访问端口可以修改 ✅**
- **D. MySQL的最大连接数可以修改 ✅**
> 参考答案：A,B,C,D

### 38. 关于远程连接数据库MySQL的来源配置，正确的是
- **A. % ✅**
- B. *
- **C. 192.168.1.2 ✅**
- D. all
> 参考答案：A,C

### 39. 腾讯云数据库MySQL主备复制（冷备）有哪几种复制方式
- **A. 半同步 ✅**
- **B. 异步 ✅**
- **C. 强同步 ✅**
- D. 完全同步
> 参考答案：A,B,C

### 40. 如下那些是腾讯云数据库Mysql提供的架构能力
- **A. 数据缓存加速架构 ✅**
- **B. 读写分离架构 ✅**
- **C. 异地灾备架构 ✅**
- **D. 主备架构 ✅**
> 参考答案：A,B,C,D
