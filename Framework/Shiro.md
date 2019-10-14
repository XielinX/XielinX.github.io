# Apache Shiro
## 一、 简介
`Apache Shiro` 是一个强大而灵活的开源安全框架，它干净利落地处理身份认证，授权，企业会话管理和加密。 

以下是你可以用`Apache Shiro` 所做的事情 :

+ 验证用户来核实他们的身份 
+ 对用户执行访问控制，如：
  + 判断用户是否被分配了一个确定的安全角色 
  + 判断用户是否被允许做某事 
+ 在任何环境下使用 `Session API`，即使没有 `Web` 或 `EJB` 容器
+ 在身份验证，访问控制期间或在会话的生命周期，对事件作出反应
+ 聚集一个或多个用户安全数据的数据源，并作为一个单一的复合用户“视图”
+ 启用单点登录（`SSO`）功能
+ 为没有关联到登录的用户启用`Remember Me`服务
+ 其他
## Shiro结构
+ Subject : 主体,用户
+ Realms : 域,类似DAO,获取数据源信息进行认证,授权
+ SecurityManger : 安全管理器,shiro的核心
+ **Authentication** : 身份认证或登录
+ **Authorization** : 授权,授予权限才能访问
+ **Session Management** : 用户会话管理
+ **Cryptography** : 加密数据
+ CacheManager : 缓存管理
## Shiro quickstart demo
1. pom文件配置
```xml
<!--shiro core-->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-core</artifactId>
    <version>1.4.1</version>
</dependency>

<!--shiro spring-->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring</artifactId>
    <version>1.4.1</version>
</dependency>
```

2. shiro配置
```ini
[users]
admin=123456
```
3. ShiroTest.java测试
```java
public void loginTest(){
// 1.创建SecurityManager的工厂,这里使用 init文件初始化
Factory<org.apache.shiro.mgt.SecurityManager> factory = new IniSecurityManagerFactory("shiro.ini");
// 2.获得SecurityManager的实例,并绑定给SecurityUtils
SecurityManager securityManager = factory.getInstance();
SecurityUtils.setSecurityManager(securityManager);
// 3.
Subject subject = SecurityUtils.getSubject();
// 登录
String username = "admin";
String password = "123456";
UsernamePasswordToken token = new UsernamePasswordToken(username,password);
try{
subject.login(token);
}catch(...){}
// exceptions
}
// 断言认证成功
Assert.assertEquals(true,subject.isAuthenticated());
```
