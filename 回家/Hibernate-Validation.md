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
// 验证给定对象的某个属性 
Set<ConstraintViolation<Car>> violations = validator.validateProperty(car, "licensePlate");
```
> 3. validateValue()方法,可以检查给定类的单个属性是否可以成功验证（如果该属性具有指定的值）
```java
//  
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
+
## @Valid与@Validated
+ 都是参数校验
### @Valid
+ 分组功能
  + 没有 
+ 注解地方
  + 属性
  + 方法
  + 方法参数(重写的方法里的参数不能修饰)
  + 构造
### @Validate
+ 分组功能
  + 有 
+ 注解地方
  + 类上 
  + 方法上
  + 方法参数上
  + **不能用在属性(字段)上**
## 四、级联校验
级联校验: 也叫嵌套验证,校验某参数时,该参数又包含其他需要校验的参数,例如:员工类Employee包含部门类对象属性,校验部门类对象属性时需要校验对象的每个字段
```java
// 员工类
public class Employee {
    @Null
    private Integer id;
   
    @NotEmpty
    private String empName;
    
    //@Valid // 嵌套验证必须使用@Valid
    @NotNull
    private Department dept; // 需要嵌套校验部门类每个属性
    }
    
// 部门类    
public class Department{
    @Null
    private Long id;

    @NotNull
    @PastOrPresent
    private LocalDateTime createTime;
}

@RestController
@Validated
public class EmployeeController {
@PostMapping("/add")
public ResultDTO addEmp(@RequestBody @Valid Employee employee){
  // 业务处理      
  return ResultDTO.success();
    }
  }   
```
在方法参数上校验时,不主动申明是不会进行嵌套验证的,如这里只会校验dept的非Null,不会校验其部门类对象的每个属性,要嵌套校验需要手动在属性(字段)上加上@Valid注解,(@Validated注解不能作用于属性上)
## 五、分组校验
### 什么是分组
像上面的Employee类,
新增时:
> id,必须为null
> empName,不为空

修改时:
> id,必须不为null
> empName,按需修改

这样就需要分组校验了
### 如何分组
```java
// 员工类
public class Employee {
  // 分组设置的接口
  public interface Add extends Default{}
  public interface Update{}

  @Null(groups = Add.class) // 新增时生效
  @NotNull(groups = Update.class) // 修改时生效
  private Integer id;
   
  @NotEmpty
  private String empName;
    
  @Valid // 嵌套验证必须使用@Valid
  @NotNull
  private Department dept; // 需要嵌套校验部门类每个属性
  }
    
// 部门类    
public class Department{
    @Null
    private Long id;

    @NotNull
    @PastOrPresent
    private LocalDateTime createTime;
}

@RestController
@Validated
public class EmployeeController {

  @PostMapping("/add")
  public ResultDTO addEmp(@RequestBody @Validated({Employee.Add.class})   Employee employee){
    // 业务处理      
    return ResultDTO.success();
    }
 
  @PostMapping("/update")
  public ResultDTO updateEmp(@RequestBody    @Validated({Employee.Update.class, Default.class}) Employee employee){
    // 业务处理
    return ResultDTO.success();
  }
}  
```
1) 为什么要写分组接口???? 
两个普通的接口，用于标识，类似于java.io.Serializable 
2) 指定了分组时,只会对分组参数校验,不会对其他参数校验
3) 需要校验其他参数时,要添加默认分组
4) 默认分组:除了指定分组,其他都是默认分组

## 自定义注解
### 说明
+ 一般注解会自动忽略值参数值为null的校验,但可以使用`@NotNull`注解校验,也可以自定义注解
+ 同一个注解,可以使用在多种数据类型上

## 使用自定义注解
> 工作类
```java
public class Job {
  
  @MultiplesOfThree
  private Long id;
    
  @Size(min = 1)
  private String name;
    
  @Size(min = 1,max = 10)
  @NotNull
  @MultiplesOfThree
  private List<String> labels;
}
```
> 自定义注解类
> 要求:
> Long类型,值为3的倍数
> List类型,元素个数为3的倍数
> 作用2中数据类型,Long,List

```java
@Target({FIELD})
@Retention(RUNTIME)
@Documented
@Constraint(validatedBy = {MultiplesOfThreeForLong.class, MultiplesOfThreeForList.class})
public @interface MultiplesOfThree {

    String message() default "必须是 3 的倍数";
    
    Class<?>[] groups() default { };
    
    Class<? extends Payload>[] payload() default { };
}
```
> @Constraint的校验实现类MultiplesOfThreeForLong
```java
public class MultiplesOfThreeForLong implements ConstraintValidator<MultiplesOfThree, Long> {
    
  @Override
  public void initialize(MultiplesOfThree constraintAnnotation) {}
    
@Override
public boolean isValid(Long value, ConstraintValidatorContext context) {
  // 检验规则
  if (value == null) {
   return true;
  }
   return value % 3 == 0;
  }
}
```
> @Constraint的校验实现类MultiplesOfThreeForList
```java
public class MultiplesOfThreeForList implements ConstraintValidator<MultiplesOfThree, List> {
  @Override
  public void initialize(MultiplesOfThree constraintAnnotation) {}
    
 @Override
 public boolean isValid(List value, ConstraintValidatorContext context){
   // 判断是否符合校验条件
   if (value == null) {
     return true;
   }
     return value.size() % 3 == 0;
  }
}
```
> 测试
> 参数  Job  job = { "id": 3, "name" : "销售经理", "labels" : ["可爱","活泼","帅气"] }
```java
@RestController
@RequestMapping("/job")
@Validated
public class JobController {
    
    @PostMapping("/add")
    public ResultDTO addJob(@RequestBody @Valid Job job){
        //
        return ResultDTO.success();
    }
}
```