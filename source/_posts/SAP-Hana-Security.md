---
title: SAP S/4 Hana Security 学习笔记
copyright: true
top: 50
date: 2019-01-23 15:45:25
tags: 
- SAP Hana安全
- SAP S/4 Hana安全
- 学习笔记
---

开启一个新的学习计划：`SAP S/4 Hana安全`！**这篇博客只是记录我的学习过程和心得，不会有什么系统化的解读或看法。所以，这里的心得不保证正确哦。**:)

这是个浩大的学习过程，我会不定时更新这篇博客。等我有所成了，再就具体的话题写点东西。 

# 2019-1 #

参考资料：

- SAP HANA Security Technical Whitepaper
- [SAP HANA Security Guide](https://help.sap.com/viewer/b3ee5778bc2e4a089d3299b82ec762a7/2.0.03/en-US)
- [The XS Advanced Programming Model](https://help.sap.com/viewer/4505d0bdaf4948449b7f7379d24d0f0d/2.0.00/en-US/df19a03dc07e4ba19db4e0006c1da429.html)

**这个章节所有的图都来自于SAP官方**

## 认识SAP S/4 Hana的架构 ##

### 1. SAP Hana的架构 ###

- Hana数据库最大的特征就是`数据存储和读取都在内存`进行，这区别于所有其他数据库技术。这意味着，数据访问、查询和操作的速度都会大大加快，因为去掉了内存和其他存储间的I/O吞吐。
- 再看这个应用架构，所有类型的服务都模块化了：Application services和Integration & Quality Services属于支持型的服务；Processing Services是对用户可见的服务；安全（包括高可用、灾备）等设计是基于数据层的，这很有意思。

![](SAP-Hana-Security/1548255232998.png)

### 2. SAP S/4 Hana的架构 ##

- 仍然是三层架构， SAP S/4是应用层，Hana是数据库层

- BW或者其他SAP应用同理

  ![](SAP-Hana-Security/1548256842259.png)

### 3. SAP Hana安全服务构成 ###

- 基本上就是AAA: Authentication, Authorization, Accountability + Data Security: Anonymization, Masking, Encryption
- 这里的数据安全三大块服务可以很好地支持隐私数据保护

![](SAP-Hana-Security/1548258031749.png)

## SAP介绍的安全服务 ##

1. 数据和日志分别备份到”硬盘 Disk"的不同卷

2. Primary和Secondary系统的高可用模式是Active-Active，实现存储和系统级别的复制

3. 自定义数据分析报表可以将数据源导入Hana（即第三方数据源与Hana建立连接），然后在Hana定义针对此项目的数据访问权限，权限分配给用户

4. 基于SAP Hana的应用开发模型是XS Advanced，支持语言： Java, node.js, SQLScript。基于此模型开发出来的应用可以直接对口Hana数据服务；这意味着，不一定要运行在SAP S/4之上

   a. XS Advanced应用用户验证可以使用基于SAML2的验证服务或者SAP Hana自身的身份验证服务

   b. 用户权限控制可以到数据行（row-level）级别

   c. 用户权限在XS Advanced应用层级完成，不需要SAP Hana的角色role

   c. 源代码管理可以使用Git

   d. SAP将不支持传统开发模式 XS Classic

5. 用户管理：使用SAP Hana管理工具：SAP HANA cockpit

   a. 可以集成LDAP、SAP Identity Management和SAP Access Control；其他类型集成采用Hana SQL接口。再看看单点登录在三种连接情况下的支持性：

   | Single sign-on method | SAP HANA JDBC/ODBC | XS Classic HTTP(S) | XS Advanced HTTP(S) |
   | --------------------- | ------------------ | ------------------ | ------------------- |
   | Kerberos/SPNego       | yes                | yes                | planned             |
   | SAML                  | yes                | yes                | yes                 |
   | SAP Logon Tickets     | yes                | yes                | no                  |
   | SAP Assertion Tickets | yes                | yes                | no                  |
   | JWT                   | yes                | no                 | yes                 |
   | X.509                 | no                 | yes                | planned             |

   b. 用户组User Group用与安全策略，如SOD，不是用于数据访问权限管理

   c. 角色Role里定义一堆权限，然后分配给用户；也可以用LDAP的安全组管理角色分配

   d. 两类权限：SQL (Object)和Analytics；SQL(Object)就是管理对SQL对象的访问权限，Analytics则是数据级别的权限，可以细致到数据行；DB admin默认没有访问数据的权限，需要额外授权

   ![](SAP-Hana-Security/1548259129905.png)

   e. 最终用户通过应用层访问数据，需要应用ABAP或者XS Advanced为此用户在SAP Hana新建Analytics权限，权限检查依然可以在应用层完成

6. 支持Dynamic data masking，即不mask原始数据，当没有UNMASKED权限的用户读取数据时，数据会被mask

7. 数据匿名Anonymization同样支持在查询数据时才匿名化

   a. 可以将特定字段泛化处理

   b. 有专门给data privacy officer可见的视图

   c. 可以控制用户访问匿名化数据的权限，也有相关的审计记录

8. 数据加密

   a. 备份数据和日志都可以加密

   b. 生产数据，即内存里的数据可以针对某个字段column加密 （当然存在“硬盘”disk上也会被加密）

   ![](SAP-Hana-Security/1548260149032.png)

   ![](SAP-Hana-Security/1548260165646.png)

   c. 应用加密调用SAP Hana的encryption API

   d. 秘钥存储在SAP的SSFS文件系统，管理秘钥可用工具cockpit；但是字段column级别的加密秘钥需要在SAP Hana和客户端（应用）进行配置。

   e. 传输层加密支持TLS/SSL，这包括SAP Hana内部的数据传输

9. 审计日志支持syslog

10. SAP Hana默认部署为多租客模式Multi-tenancy；系统管理则可以集中进行。

    ![](SAP-Hana-Security/1548260868063.png)

11. cockpit提供安全配置、维护和管理功能，并提供如下方面的检查清单（和报告？）

    ![](SAP-Hana-Security/1548261000598.png)

12. 存在可用的第三方安全工具和SAP Hana的接口

    ![](SAP-Hana-Security/1548261084396.png)





--- 未完待续 ---