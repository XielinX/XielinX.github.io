# Spring
+ spring介绍:
> 一个轻量级框,6个模块
> 1. Core Container（Spring的核心）【重要】
> 2. AOP（面向切面变成）【重要】
> 3. Messaging（消息发送的支持）
> 4. Data Access/Integration（数据访问和集成）
> 5. Web（主要是SpringWeb内容，包括MVC）【重要】
> 6. Test（Spring测试支持，包含JUint等测试单元的支持） 7、Instrumentation（设备支持，比如Tomcat的支持） 


+ Bean
属性	|描述
:--------|:---------------
class	|指定bean的类名
name	|bean的唯一标识符,也可以使用id标识符

+ bean的作用域(scope)
作用域	|描述
:--------|:---------------
singleton	|bean以单例方式存在,默认值
prototype	|多例,每次调用bean时都返回一个新的实例
request	|
session	|
global-session	|

+ bean的生命周期
  1. 实例化,(构造方法,创建对象)
  2. 装配,(可选,为属性赋值)
  3. 回调,(可选)
  4. 初始化,(init_method方法)
  5. 就绪,(使用)
  6. 销毁(destroy方法)
+ 依赖注入/Dependence Inject
 1. 概念:给bean注入属性的值 
 2. **注入方式**
   + ①setter方法注入
```xml
<bean id="" class="">
	<property name="属性" value="属性值"></property>
	<!--4种注入类型-->
	<property name="permission">
	<!-- ①List -->
		<list>
			<value>role:*</value>
		</list>
		<!-- ②Set -->
		<set></set>
		<!-- ③Map -->
		<map>
			<entry key="" value="" />
		</map>
		<!-- ④Propertites-->
		<props>
			<prop key="">value</prop>
		</props>
		
	</property>
</bean>
```
> ②构造器注入
```xml
<bean id="" class="">
	<constructor-arg ref="下面bean的id"/>
</bean>

<bean id="构造" class="" />
```
> ③静态工厂的方法注入(接口方法)
> ④实例工厂的方法注入(接口方法)

+ 依赖检查模式:确保所要求的属性可设置或注入
> `<bean id="" class="" dependency-check=""></bean>
> 1. none – 没有依赖检查，这是默认的模式。
> 2. simple – 如果基本类型(int, long,double…)和集合类型(map, list..)的任何属性都没有设置，UnsatisfiedDependencyException将被抛出
> 3. objects – 如果对象类型的任何属性都没有设置，UnsatisfiedDependencyException将被抛出
> 4. all – 如果任何类型的任何属性都没有被设置，UnsatisfiedDependencyException将被抛出

+ Spring支持的5种自动装配 模式
> 1. no:缺省情况下，自动配置是通过“ref”属性手动设定 
> 2. byName: 根据属性名称自动装配如果一个bean的名称和其他bean属性的名称是一样的，将会自装配它
> 3. byType: 按数据类型自动装配。如果一个bean的数据类型是用其它bean属性的数据类型，兼容并自动装配它。
> 4. constructor – 在构造函数参数的byType方式。
> 5. autodetect – 如果找到默认的构造函数，使用“自动装配用构造”; 否则，使用“按类型自动装配”

+ spring的注解
  + spring的EL表达式
    + `@value(#{...})`:配置文件值
    + `@Component`:类的自动扫描组件,相当于xml里设置`<bean name=""/>`
    + `@Repository`: 表示在持久层DAO组件。
    + `@Service`: 表示在业务层服务组件。
    + `@Controller`: 表示在表示层控制器组件
    + `@Autowired`: byType,和`@Qualified`一起使用(spring框架的)
    + `@Resources`: byName,未找到再byType(java的Annotation包)

## IOC
inversion of control 控制反转

+ 核心思想:将设计好的类交给容器去实例化及装配,不用自己去手动创建
## AOP
### 概念
aspect oriented programming  面向切面编程,是一种对面向对象编程(oop)的补充思路.oop的模块化是pojo类,而aop的模块化是切面,不单指某个pojo类,可以是多个类型或对象

例如,单一的登录过程,1.http请求 2. controller层(可能有登录日志log) 3.service层(有权限校验right) 4.dao层(有事务处理tx) 这些产生的横切性问题都统一为切面 
### **基本术语:**
1. Aspect:切面(pointcut+Advice),很多模块都有通用的功能,抽取出通用的功能就是切面,如编码都要utf-8
2. Joinpoint:连接点,程序执行过程中的特定点/位置(方法前,后....)
3. **Advice**:增强/通知,切面的具体实现,他会在某个特定的点上执行动作(调用方法)如保存订单模块时,记录日志,实现保存日志功能的类就是通知
4. Pointcut: 切入点,连接点的集合,通知在连接点上运行(定义一个方法何时被拦截)
5. Introduction:引入,允许你添加新方法或属性到现有类中
6. **Target object**: 目标对象,就是被代理对象
7. AOP  Proxy:目标对象的代理对象
8. Weaving:织入,把切面连接到其他运用程序上,创建一个被通知的对象的过程(切面的的实现过程)

+ AOP实现原理
> 代理,AOP代理主要分为静态代理和动态代理，静态代理的代表为AspectJ；而动态代理则以Spring AOP为代表。
> Spring AOP中的动态代理主要有两种方式:
> JDK动态代理和CGLIB动态代理。JDK动态代理通过反射来接收被代理的类，并且要求被代理的类必须实现一个接口
> 如果目标类没有实现接口，那么Spring AOP会选择使用CGLIB来动态代理目标类,,CGLIB是通过继承的方式做的动态代理(final类无法代理)

+ AOP应用场景:
  + Authentication 权限 ，Caching 缓存 
  + Lazy loading 懒加载 
  + Synchronization 同步，Transactions 事务

+ 通知类型
通知	|	描述
:---------|:--------------
前置通知MethodBeforeAdvice接口	|在一个方法执行前,执行通知
后置通知MethodafterAdvice接口	|在一个方法执行后,不考虑结果,执行通知
返回后通知AfterReturningAdvice接口	|在一个方法执行后,且方法执行成功返回时,执行通知
抛出异常后通知	ThrowsAdvice接口 |在一个方法执行后,抛出异常时,执行通知
环绕通知` MethodInterceptor`接口 	|在方法调用之前和之后,,执行通知

#### 常见AspectJ的注解
+ `@Before` – 方法执行前运行`<aop:before>`(xml)
+ `@After` – 运行在方法返回结果后`<aop:after>`
+ `@AfterReturning` – 运行在方法返回一个结果后，在拦截器返回结果。`<aop:After-returning>`(xml)
+ `@AfterThrowing` – 运行方法在抛出异常后，`<aop:after-throwing>`(xml)
+ `@Around`– 围绕方法执行运行，结合以上这三个通知。`<aop:after-around>`(xml)
#### spring的事务
+ 概念: 数据库的操作管理遵从数据的完整性[^1]约束,例如操作失败的需要进行回滚到刚开始的样子

  [^1]:数据完整性指数据库中的所有数据值均正确的状态。如果有存储有不正确的数据值，则该数据库称为已丧失数据完整性

+ 事务分为全局事务和局部事务,、commit()方法（提交事务）、rollback()方法（回滚事务）,savepoint()方法(保存点)

+ 4大特性(ACID)
  + 原子性`Atomicity`:事务单独操作时,要么全部成功,要么全部失败,参考银行取钱
  + 一致性`Consistency`:事务前后数据的完整性必须保持一致
  + 隔离性`Isolation`:多个用户并发访问数据库时，事务不会相互干扰，多个并发事务之间数据要相互隔离,参考数据脏读,*例如:A转钱100给B,在这个交易未完成之前,B查询账户余额是不会看见增加100的.*
  + 持久性`Durability`:事务全部操作完成后,结果是永久性的,不会因为故障而改变
+ 隔离机制
  + 读未提交(Read Uncommited) : 事务未提交前,就可被其他事务读取(幻读,脏读,不可重复读)
  + 读已提交(Read Commited) : 一个事务提交后,才可以被其他事务读取到(幻读,不可重复读)
  + 可重复读(Reparable Read) : 默认,保证多次读取同一个数据时,其值都和事务开始时的内容一致,禁止读取到别的事务未提交的数据(幻读)
  + 串行化(Serializable) : 最可靠的级别,能防止前三者


#### 数据库
+ 三大范式
> 1. 第一范式（1NF）:**元素不可再分**
      强调的是列的原子性，即列不能够再分成其他几列。
> 2. 第二范式（2NF）: **不能部分依赖**
首先是 1NF，另外包含两部分内容，一是表必须有一个主键；二是没有包含在主键中的列必须完全依赖于主键，而不能只依赖于主键的一部分。
在1NF基础上，任何非主属性不依赖于其它非主属性[在2NF基础上消除传递依赖]。
> 3. 第三范式（3NF）:**不存在传递依赖**
第三范式（3NF）是第二范式（2NF）的一个子集，即满足第三范式（3NF）必须满足第二范式（2NF）。
首先是 2NF，另外非主键列必须直接依赖于主键，不能存在传递依赖。即不能存在：非主键列 A 依赖于非主键列 B，非主键列 B 依赖于主键的情况。

+ 数据库乐观锁和悲观锁
> 悲观锁
> 很悲观,每次拿数据的时候都会认为别人会修改,于是每次拿数据时都会上锁,这样别人想拿这个数据时就会block(阻塞)直到拿到锁.
> **假定会发生并发冲突,屏蔽一切可能违反数据完整性的操作**
> 补充:java中的synchronized就属于悲观锁的实现,线程修改数据时都会先获得锁,保证同一时刻只有一个线程能操作数据,其他线程block 

> 乐观锁
> 很乐观,,每次拿数据的时候不会认为别人会修改,不上锁,只是在提交更新的时候判断一下有没有人更新过这个数据.(适用于多读少写的场景,提高吞吐量)
> **假设不会发送并发冲突,只是在提交时检测是否违反数据完整性**
> 实现方式:
> 1. 版本version记录,修改过一次就+1,提交更新时与原版比较
> 2. 使用时间戳timestamp,提交更新时与原版比较

+ 脏数据(脏读)
> 一个事务能够读取到另一个事务中还未提交的数据
> 不合法/不规范的的数据,编码,业务逻辑就是脏数据, 在数据库中,脏数据在临时更新(脏读)中产生
> 理解:
> 事务A更新了某个数据X,由于某种原因,事务A出现异常需要回滚,在回滚之前,事务B读取数据项X值
> (A更新后),A回滚了事务,数据项回复原值,事务B读取的数据项X就是一个临时值,就是脏数据

+ 不可重复读
> 指在一个事务内,多次读取同一数据,数据不一致
> 理解:
> 另一个事务修改过了
+ 幻读
> 指同一个事务内,多次查询返回的结果集不一样, 
> 理解:
> 同一个事务A第一次查询的时候有n条记录,在同等查询条件下第二次查询时结果不一样,好像产生了幻觉一样
> 原因:  
> 可能时另外一个事务新增/删除了第一个事务结果里的数据集

## 规范术语:
+ `Maven`:美[ˈmeɪvn],n,专家,内行
+ `Shiro`:译:希洛
+ `private`: adj,美[ˈpraɪvət]
+ `synchronized`:美[ˈsɪŋkrənaɪzd] ,v,同步(捆)
+ `transient`:英[ˈtrænziənt],adj,短暂的,临时的
+ `isolation`: 美[ˌaɪsəˈleɪʃn],n,隔离,独立
+ `durability`:美[dərəˈbɪlɪti],n,耐久,持久
