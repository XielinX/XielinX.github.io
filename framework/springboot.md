# SpringBoot
##  一﹑配置
springboot默认全局配置:(,建议二者取其一)
①application.yml
②application.properties

+ 示例
```java
@Component
@ConfigurationProperties(prefix = "person")//prefix值为yml里的
public class Person{
//直接使用注解为属性注入,
  @Value("${person.firstName}")
  private String name;
  @Value("#{10*2}")
  private Integer age;
  private Boolean female;//男
  private Date birth;
  private Map<String,Object> map;
  private List<String> list;
  private Dog dog;
}
@Validate
public class Dog{
  @Email
  private String name;//假设为邮箱格式,注入值时会校验
  private Integer age;
}
```
### 1.YAML[^yml]
[^yml]:YAML Ain't Markup Language
```yml
person:
  firstName: 张三
  age: 20
  female: true
  birth: 1995/09/19
  map: {k1: value1,key2: 中文}
  list:
    - 广东省
    - 江西省
  dog:
    name: 旺财
    age: 2
```
### 2.properties
```properties
#还是乱码的,要把中文重新写一遍(因为原先的已经编译过了)
person.age=20
person.first-name=谢林祥
person.birth=1995/09/19
person.female=false
#随机数${random.value}/${random.int(10)}
person.map.skill1=${random.uuid}
person.map.skill2=${random.int}
person.list=谢林祥,a,b,c
#占位符${value:default_value}
person.dog.name=${person,first-name:peter}
person.dog.age=1
```
### 3.@ConfigurationProperties与@Value比较
|      |@ConfigurationProperties|@Value|
|:-----|:------|:-----|
|功能   | 批量注入 | 一个个指定 |
|松散绑定| 支持驼峰/下划线/横杠格式 | 不支持 |
|SpringEL  | 不支持#{} | 支持#{} |
|JSR303数据校验| 支持@Validate/@Email等 | 不支持 |
|复杂类型| 支持,Map,List | 不支持 |
### 4.加载指定的配置文件
+ @PropertySource(value = {"classpath:person.properties"})
### 5.加载spring配置文件
+ @ImportResource(locations = {"classpath:bean.xml"})
+ 该注解放在主启动类上,自己配置的xml文件是不会自动加载的
### 6.使用java代码配置
+ @Configuration
+ 该注解表示为一个spring的配置文件
```xml
<bean id="userService"class="com.demo.basic.service.UserService"></bean>
```
```java
@Configuration
public class BeanConfig {
	@Bean
	public UserService userService(){
	    //方法名对应bean名称
		return new UserService();
	}
}
```
### 7.Profile
+ 多配置文件,默认application.propertites
+ 激活方式
  + 配置文件: spring.profiles.active=dev
  + 命令行: \-\-spring.profiles.active=dev
  + jvm参数: -Dspring.profiles.active=dev
#### propertites
```propertites
#默认:application.propertites
server.port=8080

#开发环境:application-dev.propertites
server.port=80
#使用此配置
spring.profiles.active=dev
```
#### ymal
+ 多文档块模式
```yml
server:
  port: 80
spring:
  profiles:
    active: dev
---
server:
  port: 81
spring:
  profiles: dev
---
server:
  port: 82
spring:
  profiles: prod

```
### 8.配置文件的加载位置
+ 根目录下的config目录 file: ./config/            优先级①
+ 根目录下  file: ./                                              优先级②
+ classpath:/config/                                          优先级③
+ classpath:/                                                       优先级④

## 二﹑日志
### 介绍
| 日志门面(统一抽象层) | 日志实现       |
| -------------------- | -------------- |
| Slf4j                | logback或log4j |

+ 实现log4j日志
  + slf4j-api.jar(抽象层)
  + slf4j-log4j12.jar(适配)
  + log4j.jar
+ 实现logback日志
  + slf4j-api.jar(抽象层)
  + logback-classic.jar
  + logback-core.jar(支持)
### 配置文件
#### 命名
+ logback.xml
+ logback-spring.xml(建议,可以使用高级属性,`springProfile`等)
### Maven配置
```xml
<!--logback日志-->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>2.0.0-alpha0</version>
</dependency>

<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.3.0-alpha4</version>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-core</artifactId>
    <version>1.3.0-alpha4</version>
</dependency>
```
### xml内容
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!--彩色输出-->
    <substitutionProperty name="log.pattern" value="%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){faint} %clr(%5p) %clr(---){faint} %clr(%-80.80logger{79}){cyan} %clr(:){faint} %m%n%wEx"/>
    <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter"/>
    <conversionRule conversionWord="wex" converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter"/>
    <conversionRule conversionWord="wEx" converterClass="org.springframework.boot.logging.logback.ExtendedWhitespaceThrowableProxyConverter"/>

    <!--日志存放路径-->
    <property name="log.path" value="/home"/>
    <!--日志输出格式-->
    <property name="log.pattern2" value="%d{yyyy-MM-dd HH：mm：ss.SSS}  [%thread] %-5level --- %logger{50} : [%method,%line] - %msg%n"/>
    <!--控制台输出-->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <!--输出格式-->
        <encoder calss="ch.qos.logback.classic.PatternLayout">
            <pattern>${log.pattern}</pattern>
            <charset>UTF-8</charset>
        </encoder>
    </appender>

    <!--按时间记录日志-->
    <appender name="file_info" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!--日志文件名称-->
        <file>${log.path}/system-info.log</file>
        <!--设置以时间记录-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--修改日志文件名: 文件名+时间,%i:i为变量-->
            <fileNamePattern>${log.path}/system-info.%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <!--日志文件最大保存期限:3天-->
            <maxHistory>3</maxHistory>
            <!--限制日志文件的大小:最大5MB-->
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>5MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
        <!--指定格式输出-->
        <encoder>
            <pattern>${log.pattern2}</pattern>
        </encoder>
    </appender>

    <!--日志输出级别-->
    <root lever="info">
        <appender-ref ref="STDOUT"/>
    </root>

</configuration>
```
## 三﹑Web开发
### 1. 静态资源
```xml
"classpath:/META-INF/resources/",
"classpath:/resources"
"classpath:/static"
"classpath:/public"
"/":当前项目的根路径
```
### 2.SpringMVC
+ 视图解析配置
+ 静态资源路径
  + 欢迎页
  + 访问图标`/**/favicon.ico` 
+ 转换器
  + `Converert`:转换器,如数据类型转换,String\-\-\>boolean
  + `Formatter`:格式化器,字符串日期转Date
+ 数据绑定-自动
+ 拦截器

### 3.国际化
1)  编写国际化配置文件(properties)
2)  使用ResourceBundleMessageSource管理国际化资源文件
3)  在页面使用`fmt:message`抽取国际化内容

spring-boot步骤:
1) 	编写国际化配置文件(properties),国际化默认的basename为`message`
```properties
#1.自定义basename需要在配置中指明
spring.messages.basename: i18n.login

#2.login.en_US.properties
login.user-name=please input your username
login.password=please input your password
#3.login.zh_CN.properties
login.password=请输入密码
login.user-name=请输入用户名
```
2) 自定义本地解析
```java
public class MyLocalResolver implements LocaleResolver {
	@Override
	public Locale resolveLocale(HttpServletRequest request) {
	    //url形式判断
		String language = request.getParameter("l");
		//默认是系统解析
		Locale locale = Locale.getDefault();
		if (!StringUtils.isEmpty(language)){
			String[] s = language.split("_");
			locale = new Locale(s[0],s[1]);
		}
		return locale;
	}
```
3) HTML页面抽取
```html
<input type="text" th:placeholder="#{login.user-name}">
<input type="text" th:placeholder="#{login.password}">
<p class="mt-5 mb-3 text-muted">
	<a href="#" th:href="@{/login(l='zh_CN')}">中文</a> | 
	<a href="#" th:href="@{/login(l='en_US')}">English</a>
</p>

```
### 4.定制错误页面
