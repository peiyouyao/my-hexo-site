---
title: MySQL
date: 2024-05-04 00:00:00
tags: mysql
categories: tech
---

---

# MySQL Fundamental

## 基本概念

数据库持久化存储 - 文件系统; 数据库方便操作数据

常见软件: Oracle, MySQL, PostgreSQL, MongoDB, Redis, SQLite

## MySQL

安装的是一个服务 MySQL Service

MySQL 安装目录

MySQL 数据目录

MySQL - 数据库 - 数据表

## SQL

使用 SQL (Structured Query Language) 操作, 是一种操作**关系型数据库**规则

每一种数据库存在一些不一样, 成为**方言**

```mysql
show databases;  -- 单行注释
show databases; #也可以注释
/* 
多行
注释
*/
```

### SQL 分类

- DDL (Data Definition Lang) 操作数据库和表
- DML (Data Manipulation Lang) 增删改表中数据
- DQL (Data Query Lang) 查询表中数据
- DCL (Data Control Lang) 授权

## CRUD

### C (create)

```mysql
# 数据库
create database my_db1;
create database if not exists my_db1;
create databases my_db2 character set gbk;

# 表
create table my_t (
	col_1 data_type_1,
    col_2 data_type_2  -- 最后一列不要逗号
);

create table t_1 like my_t;
```

### R (Retrieve)

```mysql
# 数据库
show databases; -- 查所有数据库
show create database my_db;  -- 查看数据库的创建语句 DDL
select database();  -- 查询当前使用的数据库

# 表
show tables;  -- 查询所有表
desc my_t;  -- 查看表结构
```

### U (update)

```mysql
# 数据库
alter database my_db character set utf8;  -- 修改数据库字符集
use database my_db;  -- 使用数据库

# 表
alter table t_1 rename to t_2  -- 修改表名
alter table t_2 character set gbk; -- 修改表的字符集
alter table t_2 add new_col [data_type] -- 添加一列
alter table t_1 change col_1 new_col_name [data_type]  -- 修改列及数据类型
alter table t_1 modify col_1 [data_type]  -- 修改一列的数据类型
alter table t_1 drop col_1; -- 删除列
```

### D (delete)

```mysql
# 数据库
drop database my_db;
drop database if exists my_db;

# 表
drop table if exists my_t;
```

## SQL 数据类型

- `int`
- `double([一共的位数], [保留的位数])`
- `date`: yyyy-MM-dd
- `datetime`: yyyy-MM-dd HH:mm:ss
- `timestamp`: 时间戳 yyyy-MM-dd HH:mm:ss 如果不给改变量赋值或赋值为null, 它自动是当前时间
- `varchar([最大字符数量])`



# MySQL Advance

## Linux 安装 MySQL

...



## 索引

索引是帮助 MySQL 高效查询的一种数据结构

### 索引的优势劣势

优:

- 提交检索效率, 降低 IO 成本
- 降低数据排序成本

劣:

- 索引本身也是一张表, 指向实体类的记录, 占用磁盘空间
- 降低了更新表的速度

所以, 索引字段不是越多越好

### 索引的结构

B Tree 索引

Hash 索引

R Tree 索引

Full-Text 索引

### B Tree

m 叉平衡搜索树

- 每个结点最多含 m 个孩子
- 除根节点和叶子节点, 每个结点至少含有 `up_ceil(m / 2)` 个孩子
- 若根节点不是叶子节点, 它至少有两个孩子
- 所有叶子节点都在同一层
- 每个非叶子节点由 n 个 key 与 n + 1 个指针组成, 其中 `up_ceil(m / 2) - 1 <= n <= m - 1`. 但节点数超过 n 的最大值, 中间的 key 分裂到父节点中, 同时两边的 keys 分裂成两个节点.

### B+ Tree (MySQL 采用)

B Tree 的变种

- n 叉 B+ Tree 每个节点最多含有 n 个 key, 而 B Tree 最多含有 n - 1 个 key
- B+ Tree 的叶子节点保留所有的 key 信息, 依 key 大小顺序排列
- 所有非叶子节点都可以看作是 key 的索引部分

叶子节点存数据, 非叶子节点不存信息, 所有叶子节点用一个额外的指针连接兄弟节点

优势:

- 查找效率稳定
- 方便范围查找

### 索引分类

- 单值索引 -- 例如, 对列 name 建立索引, 一个表可以有多个单列索引
- 唯一索引 -- 索引列的值必须唯一, 允许空值
- 复合索引 -- 一个索引包含多个列, 例如, 把 id 和 name 两个列生成一个索引

### 索引语法

```mysql
# 创建索引
create [unique|fulltext|spatial] index index_name
[using index_type]
on tbl_name(index_col_name, ...);

# 查看索引
show index from tbl_name;

# 删除索引
drop index index_name on tbl_name;
```



```mysql
create database my_db default charset=utf8mb4;

use my_db;

create table `city` (
	`id` int(11) not null auto_increment,
    `name` varchar(40) not null,
    `country_id` int(11) not null,
    primary key (`id`)  -- 在主键会默认搞一个主键索引
) default charset=utf8;

create table `country` (
	`id` int(11) not null auto_increment,
    `name` varchar(40) not null,
    primary key (`id`)
) default charset=utf8;

create index idx_name on city(name);  -- 普通索引
show index from city;  -- 查看索引
```

### 索引设计原则

针对查询频次高, 数据量大的表创建索引

针对经常在 where 语句中出现的字段建立索引

尽量使用唯一索引

尽量使用短字段做索引

利用最左索引

### 视图

相当于是一个函数, 里面封装了 sql 语句, 可以把多张表或标准的数据关联起来, 给用户返回特定的数据