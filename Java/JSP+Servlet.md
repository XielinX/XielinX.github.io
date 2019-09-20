## javaWeb基础知识
### servlet
+ **定义**:运行在web服务器或应用服务器上的程序/java类,
+ **生命周期**:一个servlet被创建到被销毁的过程.一个servlet只会有一个对象,服务于所有请求.(单例,减少开销)
	1. 实例化(使用构造方法创建对象)
	2. 初始化,执行init方法 (该方法在整个生命周期里只会执行一次)
	3. 服务 ,请求时执行service方法
	4. 销毁,执行destroy方法(tomcat服务器停止时,调用此方法)
+ **创建servlet的三种方式**:
	1. 实现Servlet接口
	2. 继承GenericServlet类
	3. 继承HttpServlet类

#### cookie
+ **定义**:存储在客户端/浏览器上的文件.安全性不好,生命周期默认是一次会话,单个cookie(4k大小),一个浏览器一般最多存储20个cookie
+ **cookie创建/获取/删除/修改**:
```java
/*如果cookie值为Unicode字符，需要为字符编码。如果cookie值为二进制数据，则需要使用BASE64编码*/
Cookie cookie = new Cookie("username","admin");
/*设置生命周期,单位:秒,正数,表示经过expire秒后失效,负数表示临时cookie,浏览器关闭就失效,0表示将删除该cookie*/
cookie.setMaxAge(7*24*60*60);
//设置cookie的访问路径,此处表示根目录下
cookie.setPath("/");
//添加到客户端
response.addCookie(cookie);

//获取cookie
Cookie[] cookies = request.getCookies();

//删除cookie,只需要新建一个同名的cookie,修改maxAge,在添加即可
Cookie cookie = new Cookie("username","admin");
cookie.setMaxAge(0);
response.addCookie(cookie);

/*修改cookie,修改cookie值其他都相同即可,尤其name,path,domain否值将看作2个不同cookie,不会去覆盖原先的cookie*/
Cookie cookie = new Cookie("username","G0009263");
cookie.setMaxAge(7*24*60*60);
response.addCookie(cookie);
```

+ cookie跨域问题:
 > 跨域:就是不同域名之间不能传递cookie,如域名www.google.com的cookie不会被提交到www.baidu.com域名去.因为浏览器**同源策略**(两个页面的协议，端口（如果有指定）和主机都相同)
 > 限制了从同一个源加载的文档或脚本如何与来自另一个源的资源进行交互。这是一个用于隔离潜在恶意文件的重要安全机制。
 >
 > 即使在同一个一级域名下的两个二级域名如www.agx.com和images.agx.com也不能交互使用Cookie，因为二者的域名并不严格相同。如果想所有agx.com名下的二级域名都可以使用该Cookie，则需要设置Cookie的domain参数为”.agx.com
 > 使用`setDomain("域名")`;来解决不同域名无法跨域问题
 > 注意：domain参数必须以点(“.”)开始。另外，name相同但domain不同的两个Cookie是两个不同的Cookie。如果想要两个域名完全不同的网站共有Cookie，可以生成两个name相同的Cookie，domain属性分别为两个域名。

#### session
+ **定义**:会话,浏览器开始到关闭的过程叫做一次会话.session将数据存储到服务端,会生成一个JSESSIONID,通过cookie存储,
+ **生命周期**:从创建(执行request.getSession()开始)到销毁(服务器关闭/session失效,默认30min),
	可以在web.xml文件通过`<session-config><time-out>30</timeout></sessionconfig>`设置时长
有效时长的计算规则:从对应用程序没有任何操作时开始计时,30min内有操纵过,计时清零,重新计时,30min内无操作,session失效.

+  **session的获取/创建**
```java
//此方法实质是获取cookie储存的jsessionid,查找对应的session,有就返回,没有就会创建
HttpSession session = request.getSession();
//true:有就返回,没有就会创建;falsle:有就返回,没有就不创建
HttpSession session = request.getSession(false);
//获取session的jsessionid
String sessionId = session.getId();
//session存,取,删除数据
session.setAttribute(String key,Object valueu);
session.getAttribute(String key);
session.removeAttribute(String key);
//手动销毁
session.invalidate();
//
```

+ 如果浏览器禁止cookie,那就必须使用url重定向了,就是把jsessionid作为url参数发送过去

#### Filter
+ 定义:过滤器,实现了Servlet API中的Filter接口,对目标资源的请求和响应进行拦截,如登录验证,编码过滤.依赖servlet容器,实现函数的回调,几乎可以过滤所有请求,但只在初始化时调用一次(生命周期),
在web.xml文件配置,在所有servlet之前
> Filter接口有三个方法:
> ①ini(FilterConfig config)方法,Filter对象实例化后只初始化一次getInitParameter(String name)方法可以获得过滤器初始化参数值
> ②doFilter()方法,filter的过滤操作方法,对request,response预先处理,FilterChain将处理后的request,respons传递到过滤链的下一 个资源
> ③destroy()方法,容器销毁Filter对象前调用 

+ 过滤器的工作方式:
> 默认过滤的url:地址栏输入,表单,超链接,响应重定向,如果要过滤其他,要设置dispatch
> REQUEST:默认的请求发方式,通过RequestDispatcher的include()或forward()方法访问时，那么该过滤器就**不会被调用**
> FORWORD:转发方式,目标资源是通过RequestDispatcher的forward()方法访问时，那么该过滤器将被调用
> INCLUDE:包含方式,目标资源是通过RequestDispatcher的include()方法访问时，那么该过滤器将被调用
> ERROR:错误方式,目标资源是通过声明式异常处理机制调用时，那么该过滤器将被调用

+ 执行顺序
> 按照web.xml配置顺序,执行FilterChain.doFilter()方法前的代码,
> 

#### Listenner
+ 定义:监听器, 某些操作如容器关闭,会话创建,触发某种事件,容器将创建对应的事件类对象,该对象至少对应一种监听器接口,用于监听处理事件;应用场景:登录操作的单点登录,要在web.xml文件配置
> 6个事件类,以Event结尾,                                                           
> **上下文**事件ServletContextEvent                             
> 上下文属性事件ServletContextAttributeEvent  
> **请求**事件ServletRequestEvent
> 请求属性事件ServletRequestAttributeEvent
> **会话**事件HttpSessionEvent
> 会话绑定事件HttpSessionBindingEvent

> 8中监听器接口,以Listener结尾
> **上下文**监听ServletContextListener
> 上下文属性监听
> **请求**监听ServletRequestListener
> 请求属性监听
> **会话**监听HttpSessionListener
> 会话属性监听
> 会话活化监听
> 会话绑定监听

#### Interceptor
+ 定义:拦截器,依赖于web容器,只对Controller请求进行拦截,基于java的反射机制,可以调用多次(生命周期),在springmvc.xml文件配置,应用场景:登录拦截,权限拦截
> 一般实现HandlerInterceptor接口,有三个方法:
> ①preHandler(),在controller方法执行前执行,return true可以继续下一步
> ②afterCompletion(),页面渲染完成返回客户端前执行,
> ③postHandler(),controller方法执行后执行


##### forward和redirect的区别？
> forward: 转发，*数据*:,转发页面,转发到的 页面都能获取;*地址栏*:不会显示url链接地址,安全效率高;使用场景:一般时用户登录时候
> redirect: 跳转，*数据*:不传递request对象;*地址栏*:会显示url链接地址,不安全,效率低;使用场景:一般注销登录时返回其他页面

+ Get请求与Post请求
> Get:从服务器上获取获取资源,将数据放在URL后面?key1=value1?key2=value2在地址栏显示数据敏感,url长度受限(1024kb),使用MIME类型application/x-www-form-urlencoded的URL编码(百分号编码)
> Post:向服务器提交数据,将数据放在http协议的请求头或消息体中,地址栏不显示,安全,url长度不受限,可大量传输数据
> 使用场景:
> post:修改,创建,文件上传,下载
> get:删除,

+ MVC的各个部分都有那些技术来实现?如何实现?
> MVC 是Model－View－Controller的简写。”Model” 代表的是应用的业务逻辑（通过JavaBean EJB组件实现）， “View” 是应用的显示面，用于与用户的交互（由JSP页面产生），”Controller” 是提供应用的处理过程控制（一般是一个Servlet），通过这种设计模型把应用逻辑，处理过程和 显示逻辑分成不同的组件实现。这些组件可以进行交互和重用。


### JSP
+ 定义:
> java server page,动态网页开发技术,也是一种servlet,后缀名.jsp
> jsp执行过程:客户端发送请求->web容器将jsp转化为servlet类,实例化jsp对象,调用\_jspService方法提供服务

+ 生命周期
> **编译**阶段,servlet容器编译源文件,生成servlet类
> **初始化**阶段,加载servlet类,创建实例,调用初始化方法
> **执行**阶段,调用jsp对应的servlet类实例服务方法
> **销毁**阶段,调用servlet类的销毁方法

#### jsp九大内置对象
> request		(javax.servlet.http.HttpServletRequest)客户端请求
> response		(javax.servlet.http.HttpServletResponse)客户端响应
> session		(javax.servlet.http.HttpSession)浏览器对象
> application	   (javax.servlet.ServletContext)上下文对象
> page		      (java.lang.Object)代表jsp对象
> pageContext		(javax.servlet.jsp.PageContext)任何作用范围
> out		(javax.servlet.jsp.jspWriter)输出浏览器信息
> exception		(java.lang.Thread)异常对象
> config		(javax.servlet.Servletconfig)配置信息

#### EL表达式的隐式对象
> 作用域访问对象:requestScope,sessionScope,applicationScope,pageScope
> 参数访问对象:*param*,paramValues
> JSP隐式对象:pageContext

+ EL表达式的`.`与`[]`:
> 都可以用来获取数据,但一下情况只能使用`[]`
> ①通过数组或集合的索引返回值,如usrList[0].username,array[1]
> ②属性值包特殊字符,如`-`,`.`,`#$%`
> ③属性值为变量

#### jsp标准标签库(JSTL)
> `<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>`:导包

标签| 描述
:-------|:---------
<c:out>   |用于在jsp显示数据,就算<%=..>
<c:set> |用于保存数据
<c:remove>	|用于删除数据
<c:choose>	|本身只当做<c:when>和<c:otherwise>的父标签
<c:when>	|<c:choose>的子标签，用来判断条件是否成立
<c:otherwise>|<c:choose>的子标签，在<c:when>标签后,当<c:when>标签判断为false时被执行

+ 四种会话跟踪技术:
  + 表单/url?
  + hidden
  + cookie
  + session


+ jsp的URL编码/解码
```java
//编码(将字符串变成字节就是编码,getbytes())
String str = java.net.URLEncoder.encode("中文"，"UTF-8");
// 解码(将字节变成字符串就是解码,new String(xx,xx))
String str = java.net.URLDecoder.decode("编码后的字符串","UTF-8");   
```

####  jsp文件上传
 + 使用form元素的`enctype="multipart/form-data"`

```html
<form method="post" action="" enctype="multipart/form-data">
    选择一个文件:
    <input type="file" name="uploadFile" />
    <br/><br/>
    <input type="submit" value="上传" />
</form>
```

####  jsp语法
> `<%@ page ..%>`, `<%@ include file="相对地址"%>`:指令,
> `<%= ... %>`:表达式 a=?
> `<%....%>`:代码片段,String a= "dd";
> `<jsp:include page="page.jsp",flush="true"/>`:动作/行为,此处为刷新缓存区(动作包含)
> `<%@ include file="a.jsp" %>`:静态包含

> `<jsp:forward page="login.jsp/>"`:跳转方式,完全的跳转
> `<jsp:include page="page.jsp",flush="true"/>`:跳转方式?,其实不是跳转,只是会动态的将该页面显示的结果,然后返回