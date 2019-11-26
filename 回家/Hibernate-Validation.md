# Hibernate-Validation

## 一、简介  
Bean Validation是Java定义的一套基于注解的数据校验规范 ,JSR303: **Java Specification Requests** , 意思是Java 规范提案。 是指向[JCP](https://baike.baidu.com/item/JCP)(Java Community Process)提出新增一个标准化技术规范的正式请求。任何人都可以提交JSR，以向Java平台增添新的API和服务。JSR已成为Java界的一个重要标准。  
## 二、demo
1. 获取validator实例
```java
ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
Validator validator = factory.getValidator();
```
2. 验证器方法
该**Validator**接口包含三种方法，可用于验证整个实体或仅验证实体的单个属性。
这三种方法均返回`Set<ConstraintViolation>`。如果验证成功，则该集合为空。否则，ConstraintViolation将为每个违反的约束添加一个实例 

> 1. validate()方法对给定bean的所有约束进行验证
```java
Car car = new Car(null,"DD-AB-123",4);    
// 验证成功,返回的集合是空的,错误的会加入到集合里
Set<ConstraintViolation<Car>> constraintViolations = validator.validate(car);
```
> 2. validateProperty(Object obj,String bean)方法,您可以验证给定对象的单个命名属性。该属性名称是JavaBeans属性名称。
```java
 Car car = new Car("Morris", "D", 1);
Set<ConstraintViolation<Car>> violations = validator.validateProperty(car, "licensePlate");
```
> 3. validateValue()方法,可以检查给定类的单个属性是否可以成功验证（如果该属性具有指定的值）
```java
 Set<ConstraintViolation<Car>> violations = validator.validateValue(Car.class,"licensePlate","A");// 类名,属性,属性值
```

3. 需要的jar包
```xmlx
<!--hibernate 校验, 引入spring-boot-starter-web的jar包可省略-->
<dependency>
  <groupId>org.hibernate.validator</groupId>
  <artifactId>hibernate-validator</artifactId>
  <version>6.1.0.Final</version>
</dependency>

 <!--统一表达式语言 JSR341-->
<dependency>
  <groupId>org.glassfish</groupId>
  <artifactId>javax.el</artifactId>
  <version>3.0.1-b11</version>
</dependency>
```

## 三、基本注解

| **meta-data** | 说明              |
| ------------- | ----------------- |
| @Null         | 为空              |
| @NotNull      | 不为空            |
| @AssertTrue   | 布尔,为true       |
| @AssertFalse  | 布尔,为false      |
| @Min(value)   | 数字，最小为value |

## 四、开始使用Validation
### 传统参数校验
大多是使用if条件判断
