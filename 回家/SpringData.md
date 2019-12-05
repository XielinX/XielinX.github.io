# SpringData
[Spring Data](https://spring.io/projects/spring-data#overview):为数据访问提供一个熟悉且一致的基于Spring的编程模型，同时仍保留基础数据存储的特殊特征

# JPA
## 一、 简介
JPA是Java Persistence API的简称，中文名Java持久层API，是JDK 5.0注解或XML描述对象－关系表的映射关系，并将运行期的实体对象持久化到数据库中

Sun引入新的JPA ORM规范出于两个原因：其一，简化现有Java EE和Java SE应用开发工作；其二，Sun希望整合ORM技术，实现天下归一。
## 二、常用注解

| 注解                      | 说明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| @Entity                   | 表示这是以一个实体类                                         |
| @Table(name = "tab_name") | 实体类名与数据库表名不同时使用,与@Entity并列使用默认是pojo类名,首字母小写 |
| @Id                       | 声明主键                                                     |
| @GeneratedValue           | 数据库的自增,策略:IDENTITY(mysql)等                          |
| @Column                   | 当数据库与实体类的字段不同时使用                             |
|                           |                                                              |
|                           |                                                              |
## 三、 JPA的API
+ EntityManagerFactory
+ EntityManager
+ EntityTransaction
## 四、JPA的三种状态
+ 新建状态(瞬时状态)
实例化创建的对象

+ 持久化状态
当EntityManager.persist(Object obj)时

+ 游离状态
事务提交或者EntityManager关闭时
## 五、常用方法
### find()
### refence()
### merge()
## 六、JPA映射关系
### 6.1 多对一
