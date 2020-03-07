# Hibernate-Validation

## 一、简介  
Bean Validation是Java定义的一套基于注解的数据校验规范 ,JSR303: **Java Specification Requests** , 意思是Java 规范提案。 是指向[JCP](https://baike.baidu.com/item/JCP)(Java Community Process)提出新增一个标准化技术规范的正式请求。任何人都可以提交JSR，以向Java平台增添新的API和服务。JSR已成为Java界的一个重要标准。  
## 二、Validator
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
// 验证给定对象的所有属性,验证成功,返回的集合是空的,错误的会加入到集合里
Set<ConstraintViolation<Car>> constraintViolations = validator.validate(car);
```
> 2. validateProperty(Object obj,String bean)方法,您可以验证给定对象的单个命名属性。该属性名称是JavaBeans属性名称。
```java
 Car car = new Car("Morris", "D", 1);
// 验证给定对象的licensePlate属性
Set<ConstraintViolation<Car>> violations = validator.validateProperty(car, "licensePlate");
```
> 3. validateValue()方法,可以检查给定类的单个属性是否可以成功验证（如果该属性具有指定的值）
```java
//  验证给定对象的单个属性licensePlate的指定值A是否可以成功校验
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
## @Valid与@Valid
+ 都是参数校验

| 注解      | 分组功能 | 类上   | 属性/字段 | 方法 | 方法参数               | 构造   |
| --------- | -------- | ------ | --------- | ---- | ---------------------- | ------ |
| @Valid    | 没有     | 不可以 | 可以      | 可以 | 可以(重写方法参数不行) | 可以   |
| @Validate | 有       | 可以   | 不可以    | 可以 | 可以                   | 不可以 |

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
### 5.1 什么是分组
像上面的Employee类,
新增时:
> id,必须为null
> empName,不为空

修改时:
> id,必须不为null
> empName,按需修改

这样就需要分组校验了
### 5.2 如何分组
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

## 六、自定义注解
### 6.1说明
+ 一般注解会自动忽略值参数值为null的校验,但可以使用`@NotNull`注解校验,也可以自定义注解
+ 同一个注解,可以使用在多种数据类型上
+ JSR303还是Hibernate-Validation都是对Object类型的参数校验,不对单个参数校验

### 6.2使用自定义注解
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
## 七、List中做分组校验
> 有一个Controller请求,如下
```java
@RestController
@RequestMapping("/job")
@Validated
public class JobController {

  /*
   * 参数为集合对象,需要校验集合里的每一个对象
   */
  @PostMapping("/list")
  public ResultDTO addEmpByBatch(@RequestBody  @Validated({Employee.Add.class, Default.class}) List<Employee> empList){
        //
        return ResultDTO.success();
    }
}    
```
> 会发现,不会校验集合里的每个Employee对象,于是自定义一个注解类和校验器类
```java
@Target({FIELD,PARAMETER})
@Retention(RUNTIME)
@Documented
@Constraint(validatedBy = {ValidListForList.class})
public @interface ValidList {
    
    // 自定义分组
    Class<?>[] grouping() default { };
    
    // 快速失败:有一个错误,后面都不会去校验
    boolean quickFail() default false;
    
    String message() default "";
    
    Class<?>[] groups() default { };
    
    Class<? extends Payload>[] payload() default { };
}
```
```java
// 校验器类
public class ValidListForList implements ConstraintValidator<ValidList, List> {
    
    private Class<?>[] grouping;
    private boolean quickFail;
    
    @Override
    public void initialize(ValidList constraintAnnotation) {
        grouping = constraintAnnotation.grouping();
        quickFail = constraintAnnotation.quickFail();
    }
    
    /**
     * 循环校验集合元素,出现错误立即抛出异常
     * @param value 待校验集合
     * @param context ?
     * @return 校验成功,返回true
     */
    @Override
    public boolean isValid(List value, ConstraintValidatorContext context) {
        Map<Integer,Set<ConstraintViolation<Object>>> errors = new HashMap<>();
        for (int i = 0; i < value.size(); i++) {
            Object object = value.get(i);
            Set<ConstraintViolation<Object>> violationSet = ValidatorUtil.validator.validate(object,grouping);
            // 一旦发现异常,存储第一个异常信息,然后快速失败
            if (violationSet.size() > 0){
                errors.put(i,violationSet);
                if (quickFail){
                    throw new ValidListException(errors);
                }
            }
        }
        
        // 有异常信息,抛出
        if (errors.size() > 0){
            throw new ValidListException(errors);
        }
        return true;
    }
}
```
> 修改控制层
```java
@RestController
@RequestMapping("/job")
@Validated
public class JobController {

  @PostMapping("/list")
  public ResultDTO addEmpByBatch(@RequestBody @ValidList(grouping = {Employee.Add.class, Default.class},quickFail = true){
        //
        return ResultDTO.success();
    }
}    
```
## 八、bean参数间的逻辑校验
### 8.1 场景
员工类里要求: 根据年龄来校验title的参数值
  年龄在20~25之间的,title开头为"初级",例如"初级xxxx"
  年龄在2~30之间的,title开头为"中级",例如"中级xxxx"
  否则,不校验
### 编程
> employee实体类
```java
@GroupSequenceProvider(EmployeeSequenceProvider.class)
public class Employee {
    
    public interface Add extends Default {}
    public interface Update{}
    
    /**
     * 分组:
     *  新增时:id为null,empName不为空
     *  修改时:id不为null,
     */
    @Null(groups = Add.class) // 新增时生效
    @NotNull(groups = Update.class) // 修改时生效
    private Long id;
    
    @NotEmpty
    private String empName;
    
    @Valid
    private Department dept;
    
    /**
     * 根据年龄,校验title
     * age:20~25,title的开头"初级"
     * age:25~30,title的开头"中级"
     * 否则,其他
     */
    @NotNull
    private Integer age;
    
    public interface JuniorTitle{} // 初级\u521d\u7ea7
    public interface MiddleTitle{} // 中级\u4e2d\u7ea7
    
    // 正则一定要写uniocode编码???
    @NotNull
    @Pattern(regexp = "^\u521d\u7ea7.*",groups = JuniorTitle.class)
    @Pattern(regexp = "^\u4e2d\u7ea7.*",groups = MiddleTitle.class)
    private String title;
}
```
> 使用@GroupSequenceProvider,来动态定义默认分组
> 需要实现接口DefaultGroupSequenceProvider\<?\>
```java
public class EmployeeSequenceProvider implements DefaultGroupSequenceProvider<Employee> {
    
    @Override
    public List<Class<?>> getValidationGroups(Employee employee) {
        // 添加默认分组
        List<Class<?>> defaultGroupSequence = new ArrayList<>();
        defaultGroupSequence.add(Employee.class);
    
        /**
         * age:20~25,初级组
         * age:25~30,中级组
         */
        if (employee != null) {
            if (20 <= employee.getAge() && employee.getAge() <= 25) {
                defaultGroupSequence.add(Employee.JuniorTitle.class);
            } else if (25 < employee.getAge() && employee.getAge() <= 30) {
                defaultGroupSequence.add(Employee.MiddleTitle.class);
            }
        }
        // 最后就是{default.class,?} ?=初级组或者中级组或者没有
        return defaultGroupSequence;
    }
}
```
> controller测试
> 如果输入{"empName":"张三","age":22,"title":"程序员"}会报错说,没有匹配正则/*初级*/
```java
 @PostMapping("/updateTitle")
    public ResultDTO updateEmpWithTitle(@RequestBody  @Valid Employee employee){
        return ResultDTO.success();
    }
```