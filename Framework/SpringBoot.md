# SpringBoot
## 常用注解
+ `@GetMapping("xxx")`
其实就是`@GetRequestMapping(name="xxx",Method=RequestMethod.GET)`

+ `@Value`
  + usage1:`#{expression?:default value}`
  + usage2: `${property:default value}`
  + 注意事项
    + 不能作用于静态变量（static）
    + 不能作用于常量（final）
    + 不能在非注册的类中使用（类需要被注册在spring上下文中，如用@Service,@RestController,@Component等）
    + 使用这个类时，只能通过依赖注入的方式，用new的方式是不会自动注入这些配置的 
```java
//用法1
@Value("#{systemProperties['mongodb.port'] ?: 27017}")
private String mongodbPort;

@Value("#{config['mongodb.url'] ?: '127.0.0.1'}")
private String mongodbUrl;	
	
@Value("#{aBean.age ?: 21}")
private int age;
//用法2
@Value("${spring.redis.host}")
private String host;

@Value("${mongodb.url:127.0.0.1}")
private String mongodbUrl;

@Value("#{'${mongodb.url:172.0.0.1}'}")
private String mongodbUrl;
	
@Value("#{config['mongodb.url']?:'127.0.0.1'}")
private String mongodbUrl;
```
```yml
spring.redis.host=127.0.0.1
mongodb.url=1.2.3.4
mongodb.db=hello
```