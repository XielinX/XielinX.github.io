## 项目介绍
一直想做一个有权限分配的的项目
## 使用技术
#### 前端
+ Layer
+ Thymeleaf

#### 后端
+ SpringBoot
+ SpringMVC
+ Mybatis/plus
+ Shiro
+ Maven
+ Lombok
+ OkHttp3
+ MySQL
+ Git

## 项目结构
```xml

```
## 项目演示

## 工作记录
#### 2019年7月11日
+ 设计数据库
+ 项目环境搭建
+ `entity`,`dao`,`mapper`,`controller?`层文件自动生成
+ 单元测试
+ 业务编程
#### 2019年7月12日
+ **RBAC:基于角色/资源的访问控制**
> 基于角色/role
```java
//假设只有项目组长monitor角色,才有权限查看核心代码
if(user.hasRole("monitor)){
  //do something
}
//现在需求变更,项目经理manager角色也能查看
if(user.hasRole("monitor") || user.haoRole("manager")){
  //do something
}
//可以看出,如果角色频繁变更,代码判断逻辑也要频繁的修改
```

> 基于资源 /resource
```java
//权限判断与角色无关,只判断是否拥有该字符权限
if(user.hasPermission("project:core:view")){
  //do something
}
/*这样用户权限变更,只需修改数据库中用户对应角色的权限,而权限与对应的资源通常不需改变
 * 基于资源方式,仍然需要角色,用户权限分配依据角色(例如:admin角色,同时会有CRUD权限)
 * 访问控制时,不判断角色,只关心是否拥有该权限,有就可访问
 * /
```

+ 表结构 
+ 环境搭建
  + IDEA编程 
  + 先完成基本自动生成
  + 再学习JWT
  + 前端不使用,使用PostMan测试
