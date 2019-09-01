## springboot
+ 构建maven-webapp项目工程jar包

```xml
<!-- 父级starter, -->
<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.1.5.RELEASE</version>
		<relativePath /> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>

		<!-- spring-boot 的核心包 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>

		</dependency>

		<!-- spring-boot的web容器 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<!-- spring-boot的热部署 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<scope>runtime</scope>
			<optional>true</optional><!-- 依赖不传递 -->
		</dependency>
		<!-- spring-boot的测试 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>

		<!-- Mybatis -->

		<!-- mysql -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
		<!-- druid -->

		<!-- json工具包 -->
		<!-- commons-lang3工具包 -->
		<!-- spring-boot的分页插件 -->
		<dependency>
			<groupId>com.github.pagehelper</groupId>
			<artifactId>pagehelper-spring-boot-starter</artifactId>
			<version>1.2.10</version>
		</dependency>

		<!-- spring-boot的Thymeleaf模板 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
	</dependencies>

```
## Spring boot 的注解
+ `@GetMapping("xxx")`:其实就是`@GetRequestMapping(name="xxx",Method=RequestMethod.GET)`