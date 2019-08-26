## Log4j日志[^1]
[^1]:java编写的日志框架()API

#### Log4j的配置说明
+ Log4j的3个主要组成部分
  1. `loggers`:负责捕获记录信息(级别)
  2. `appenders`:发布日志信息(输出目的地)	
  3. `layouts`:日志信息输出格式(布局)

+ 日志等级
  + OFF [^01] > FATAl [^02] > **ERROR** [^03] > **WARN** [^04] > **INFO** [^05] > **DEBUG** [^06] > ALL[^07]
  [^01]:OFF-最高等级，为了关闭日志记录
  [^02]:FATAL-非常严重的错误事件，这可能导致应用程序中止
  [^03]:ERROR-错误事件可能仍然允许应用程序继续运行!
  [^04]:WARN-具有潜在危害的情况.
  [^05]:INFO-能够突出在粗粒度级别的应用程序运行情况的信息的消息.
  [^06]:DEBUG-细粒度信息事件是最有用的应用程序调试
  [^07]:ALL-各级包括自定义级别
  
1. 配置根Logger
```properties
log4j.rootLogger=[level],appenderName1,appenderName2
```
  > level:日志级别, appenderName指日志信息输出到哪个地方,可以多个(把它当作地名吧)

2. 配置日志信息输出目的地Appender
```properties
log4j.rootLogger=debug,stdout
#控制台打印
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.option1=value1
```

+ `org.apache.log4j.ConsoleAppender`:控制台
+ `org.apache.log4j.FileAppender`:文件
+ `org.apache.log4j.RollingFileAppender`:文件大小到达一定尺寸时产生一个新文件
+ `org.apache.log4j.DailyRollingFileAppender`:每天产生一个日志文件
+ `org.apache.log4j.WriterAppender`:将日志信息以流格式发送到任意指定地方

3. 配置日志信息的格式 (布局)
```properties
log4j.rootLogger=debug,appenderName
log4j.appender.appenderName=org.apache.log4j.ConsoleAppender
#布局
loj4j.appender.appenderName.layout=fully.qualified.name.of.layout.class 
loj4j.appender.appenderName.layout.option1=value1
```

+ `org.apache.log4j.HTMLLayout`:以HTML表格形式布局
+ `org.apache.log4j.PatternLayout`:可以灵活地指定布局模式
+ `org.apache.log4j.SimpleLayout`:包含日志信息的级别和信息字符串
+ `org.apache.log4j.TTCCLayout`:包含日志产生的时间、线程、类别等等信息

4. 打印格式化日志信息
  + `%m`: 输出代码中指定的消息
  + `%p`: 输出优先级,即DEBUG,INFO,WARN,ERROR(从低到高)
  + `%r`: 输出 自应用启动到输出该log信息耗费的毫秒数
  + `%c`: 输出全类名
  + `%t`: 输出产生该日志事件的线程名
  + `%n`: 输出一个换行符,windows为"rn",Unix为"n"
  + `%d`: 输出日志的时间,可指定格式如,%{yyyy-MM-dd HH:mm:ss} -->2019-06-16 20:09:33
  + `%l`: 输出日志事件的发生位置,包括类目名,发生的线程,以及在代码中的行数,如Testlog4.main(TestLog4.java:10)


#### log4j.properties的配置样例
+ 控制它打印,分别记录DEBUG,ERROR级别的日志
```properties
#根日志器
log4j.rootLogger=INFO,stdout,debugFile,errorFile

#Appender_stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
#只输出[level]级别及以上的日志,这里DEBUG级别
log4j.appender.stdout.Threshold=DEBUG
log4j.appender.stdout.ImmediateFlush=true
#layout
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern = %d{yyyy-MM-dd HH:mm:ss}  %p --- [%c] %m%n
#打印sql
log4j.logger.java.sql.Connection=debug
log4j.logger.java.sql.Statement=debug
log4j.logger.java.sql.PreparedStatement=debug,stdout

#Appender_debugFile
log4j.appender.debugFile=org.apache.log4j.DailyRollingFileAppender
log4j.appender.debugFile.File=D:/log4j/debug/debug_
log4j.appender.debugFile.DatePattern=yyyy-MM-dd-HH-mm'.log'  每分钟#log4j.appender.debugFile.DatePattern=yyyy-MM-dd-HH'.log'    每小时#log4j.appender.debugFile.DatePattern=yyyy-MM-dd'.log'	每天#log4j.appender.debugFile.DatePattern=yyyy-MM-dd-a'.log'	每半天
#只输出[level]级别及以上的日志,这里DEBUG级别
log4j.appender.debugFile.Threshold=DEBUG

log4j.appender.debugFile.layout=org.apache.log4j.PatternLayout
log4j.appender.debugFile.layout.ConversionPattern = %d{yyyy-MM-dd HH:mm:ss}  %p --- [%c] %m%n

#Appender_errorFile
log4j.appender.errorFile=org.apache.log4j.RollingFileAppender
log4j.appender.errorFile.File=D:/log4j/error/error.log
#日志文件大小
log4j.appender.errorFile.MaxFileSize=10M
#追加,最后的文件不会覆盖原先的
log4j.appender.errorFile.Append=true
#只输出[level]级别及以上的日志,这里ERROR级别
log4j.appender.errorFile.Threshold=ERROR

log4j.appender.errorFile.layout=org.apache.log4j.PatternLayout
log4j.appender.errorFile.layout.ConversionPattern = %d{yyyy-MM-dd HH:mm:ss}  [%p] %l %m%n
```
#### java代码使用
```java
import org.apache.log4j.Logger;
priavte static final Logger logger = Logger.getLogger(当前类名.class);
if(logger.isDebugEnabled()){
	//只有开启debug级别才会执行,不建议使用字符拼接会影响性能, 使用slf4j的{}
	logger.debug("debug中" + username)
}

```

+ 需要的jar包
```xml
	<!--slf4j-log4j的兼容jar包 -->
	<dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>2.0.0-alpha0</version>
        <scope>test</scope>
   </dependency>
   
	<!--slf4j的jar包 -->
	<dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
        <version>2.0.0-alpha0</version>
	</dependency>
	
	<!--log4j的jar包 -->
	<dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-core</artifactId>
        <version>2.11.2</version>
	</dependency>

```

## SLF4J日志[^4]
[^4]:slf4j效率更高,内存消耗更低

#### java代码的使用
```java
import org.slf4j.LoggerFactory;
private static final Logger logger = LoggerFactory.getLogger(类名.class);
//可以使用{},来读取后面的参数并放到里面
logger.info("my first name is {}",firstName);
logger.info("my first name is {} and my last name is {}",firstName,lastName);
```

+ Springboot默认的日志框架为logback
