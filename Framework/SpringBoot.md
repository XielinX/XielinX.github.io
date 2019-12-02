# SpringBoot
## yml

## web开发
### 错误页面的定制
#### 原理
1. defaultErrorAttribute : 
2. BasicErrorController : 处理默认/error请求
```java
@Controller
@RequestMapping("${server.error.path:${error.path:/error}}")
public class BasicErrorController extends AbstractErrorController {

    /**
     * text/html,产生html类型的数据
     */
	@RequestMapping(produces = MediaType.TEXT_HTML_VALUE)
	public ModelAndView errorHtml(HttpServletRequest request, 
        HttpServletResponse response) {
	HttpStatus status = getStatus(request);
		Map<String, Object> model = Collections
				.unmodifiableMap(getErrorAttributes(request, isIncludeStackTrace(request, MediaType.TEXT_HTML)));
		response.setStatus(status.value());
		
		//
		ModelAndView modelAndView = resolveErrorView(request, response, status, model);
		return (modelAndView != null) ? modelAndView : new ModelAndView("error", model);
	}

    /**
     * 产生json数据
     */
	@RequestMapping
	public ResponseEntity<Map<String, Object>> error(HttpServletRequest request) {
		Map<String, Object> body = getErrorAttributes(request, isIncludeStackTrace(request, MediaType.ALL));
		HttpStatus status = getStatus(request);
		return new ResponseEntity<>(body, status);
	}
}

```
3. ErrorPageCustomizer : 
```java
@Value("${error.path:/error}")
private String path = "/error";// 系统出现错误,来到/error请求路径(默认)
```
4. DefaultErrorViewResolver
```java
public ModelAndView resolveErrorView(HttpServletRequest request, 			HttpStatus status, Map<String, Object> model) {
	ModelAndView modelAndView = resolve(String.valueOf(status.value()), model);
		if (modelAndView == null && SERIES_VIEWS.containsKey(status.series())) {
			modelAndView = resolve(SERIES_VIEWS.get(status.series()), model);
		}
		return modelAndView;
	}


private ModelAndView resolve(String viewName, Map<String, Object> model) {
	String errorViewName = "error/" + viewName;// 视图名:error/400
	// 模板引擎可用,
	TemplateAvailabilityProvider provider = this.templateAvailabilityProviders.getProvider(errorViewName,
				this.applicationContext);
		if (provider != null) {
			return new ModelAndView(errorViewName, model);
		}
		// 没有模板引擎,error/状态码.html
		return resolveResource(errorViewName, model);
	}
```

系统出现4xx或5xx之类的错误,ErrorPageCustomizer就会生效,来到/error请求,会被BasicErrorController处理
#### 定制
+ 返回html页面
  1) 有模板引擎;error/状态码: 错误页面命名为 `状态码.html`,并放在模板引擎文件夹下的error文件夹下(templates/error/),可以使用4xx和5xx来泛指4或5开头的状态码信息,按精确优先原则(优先寻找精确的状态码.html)
  2) 模板引擎找不到这个页面,静态资源文件夹下找(static/)
  3) 都没有,默认来到SpringBoot默认的错误提示页面,Whitelabel Error Page

+ 页面获取的信息:
  timestap : 时间戳
  status : 状态码 
  error : JSR303数据校验的错误
  message : 错误信息
  exception : 异常对象

2. 返回json数据

## 定时任务
## AOP
## Redis
## 日志
## 常用注解
### @GetMapping("/url")
其实就是`@GetRequestMapping(name="xxx",Method=RequestMethod.GET)`

### @Value
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
### @RestControllerAdvice或@ControllerAdvice
+ 区别
  + `@RestControllerAdvice`多了一个`@ResponseBody`注解
+ 作用
全局异常处理,只要作用在@RequestMapping上，所有的异常都会被捕获
```java
@Slf4j
@RestControllerAdvice
public class MyGlobalExceptionHandler {
	/**
	 * @param e Exception
	 * @return dto
	 */
	@ExceptionHandler(value = Exception.class)
	public ResultDTO handleException(Exception e) {
		log.error("exception:{}", e.getMessage());
		return ResultDTO.failed(0, e.getMessage());
	}
	
	/**
	 * 处理自定义异常
	 * @param e CustomizeException
	 * @return dto
	 */
	@ExceptionHandler(value = CustomizeException.class)
	public ResultDTO handleCustomizeException(CustomizeException e){
		log.error("自定义异常:{}",e.getMessage());
		return ResultDTO.failed(1,e.getMessage());
	}
}
```
```java
@RestController
public class IndexController {
	/**
	 * 测试Exception
	 */
	@GetMapping("/index")
	public String index(){
		int i = 10 / 0 ;
		return "index";
	}
	
	/**
	 * 测试CustomizeException
	 */
	@GetMapping("/hello")
	public String hello(){
		throw new CustomizeException(100,"抛出自定义异常");
	}
}
```