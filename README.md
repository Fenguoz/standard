# PHP开发规范指南

## 目录

- [0. 介绍](README.md)
- [1. 前言](Preface.md)
- [2. 项目开发规范](Project.md/#project)
- [3. PHP编码规范](PHP.md/#php)
  - [3.1. 命名风格](PHP.md/#name)
  - [3.2. 常量定义](PHP.md/#constant)
  - [3.3. 代码格式](PHP.md/#format)
  - [3.4. OOP规约](PHP.md/#oop)
  - [3.5. 并发处理](PHP.md/#concurrent)
  - [3.6. 控制语句](PHP.md/#control)
  - [3.7. 其它](PHP.md/#other)
- [4. Mysql数据库设计规范](Mysql.md/#mysql)
  - [4.1. 建表规约](Mysql.md/#buildtable)
  - [4.2. 索引规约](Mysql.md/#index)
  - [4.3. SQL语句](Mysql.md/#sql)
  - [4.4. ORM映射](Mysql.md/#orm)
- [5. 单元测试规范](UnitTest.md/#test)
- [6. 异常日志规范](Log.md/#exception-log)
  - [6.1. 异常处理](Log.md/#exception)
  - [6.2. 日志规约](Log.md/#log)
- [7. 安全规范](Safe.md/#safe)


## 版本历史

| 版本号 | 版本名 | 更新时间 | 备注 |
| :-----:| :----: | :----: | :---- |
| 0.1.0 | 试读版 | 2021.01.22 | - |

## 名词解释

1. ORM(Object Relation Mapping): 对象关系映射，对象领域模型与底层数据之间的转换， 本文泛指 Laravel,Hyperf 等框架。
1. NPE(java.lang.NullPointerException): 空指针异常。