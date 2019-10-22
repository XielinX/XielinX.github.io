# Java随笔
## 一、 面向对象(oop)
### 1.0 理解
### 1.1 特征
#### 继承
#### 多态
#### 封装
## 二、Annotation
jdk5增加元数据(MetaData))的支持,也就是Annotation(注解),代码里的特殊标记
这些标记可以在编译,类加载,运行时被读取

可以修饰于包,类,构造器,方法,接口,成员变量,参数,局部变量的声明
### 2.1 基本Annotation
#### @Override
+ 表示重写父类方法
#### @Depreciated
+ 表示某个程序元素(类,方法等)已过时
#### @SuppressWarnings
+ 可以取消编译器警告
```java
@SuppressWarnings
public class SuppressWarningsTest{
  public static void main(String[] args){
    List list = new ArrayList();
  } 
}
```
#### @SafeVarargs
+ 可以抑制堆污染警告(heap pollution)
+ 把一个不带泛型的对象赋值给一个带泛型的变量时,就会发生堆物染
```java
List list = new ArrayList<Integer>();
List<String> ls = list;// 堆污染
```
#### @FunctionalInterface
+ 函数式接口
  + 只有一个抽象方法
  + 但也可以包含多个默认方法和static方法
  + 只能修饰接口
  + 此接口为Lambda表达式准备 
```java
@FunctionalInterface
public interface Funinterface{
  static void foo(){
    System.out.println("foo类方法");
}

  default void bar(){
    System.out.println("bar默认方法");
}
  /**
   * 只定义一个抽象方法
   */
void test();
} 
```
### 2.2 元Annotation
#### @Retention
+ 只能修饰一个Annotation
+ 用于指定被修饰的Annotation可以保留多长时间
+ @Retention包含一个RetentionPolicy类型的value成员变量,使用时必须指定value值
+ value值
  + RetentionPolicy.CLASS
    + 编译器将Annotation记录在class中
    + 当运行Java程序时,JVM不可获取Annotation信息(默认)
  + RetentionPolicy.RUNTIME
    + 译器将Annotation记录在class中
    + 当运行Java程序时,JVM可获取Annotation信息,程序通过反射获取  
  + RetentionPolicy.SOURCE
    + Annotation只保存在源代码中
    + 编译器直接丢弃Annotation
```java
@Retention(value=RetentionPolicy.CLASS)
public @interface testable{}

@Retention(RetentionPolicy.RUNTIME)
public @interface testable{}
```
#### @Target
+ 只能修饰一个Annotation
+ 用于指定被修饰的Annotation可以用于哪些程序单元
+ value值
  + ElementType.ANNOTATION_TYPE
    + 只能修饰的Annotation
  + ElementType.CONSTRUCROR
    + 只能修饰构造器
  + ElementType.FIELD
    + 只能修饰成员变量
  + ElementType.LOCAL_VARIABLE
    + 只能修饰 局部变量
  + ElementType.METHOD
    + 只能修饰方法
  + ElementType.PACKAGE
    + 只能修饰包
  + ElementType.PARAMETER
    + 只能修饰参数
  + Element.TYPE
    + 修饰类,接口,枚举
```java
// 只能修饰方法
@Target(ElementType.METHOD)
public @interface testable{}

// 只能修饰成员变量
@Target(ElementType.FIELD)
public @interface testable{}
```
#### @Documented
+ 使用该注解的类将会被javadoc工具提取成文档
```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@Documented
public @interface testable{}

public class MyTest{
  // 使用该注解修饰,方法会被提取为文档
  @testable
  public void info(){
    // 方法体
  }
}
```
#### @Inherited
+ 使用该注解类修饰的类,这个类的子类也会被注解类修饰
```java
// 注解类
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@Inherited
public @interface testable{}

// 修饰类
@testable
public class Base{}

// 继承至Base类,但也具有@testable功能
public class MyTest extends Base{}
```
### 2.3 自定义Annotation
+ 使用`@interface`定义
+ `default`指定成员变量默认值

```java
/**
 * 注解类,修饰成员变量
 */
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface testable{
  // 成员变量
  String name() default "hellokit";
  int age() default 30;
}

/**
 * pojo
 */
public class User{
  @testable(name="姓名")
  private String name;
  
  @testable(name="年龄",age="20")
  private Integer age;
}
```
### 2.4 提取Annotation信息

## 三、JVM
### 指令集
### 寄存器
### 类文件的格式
### 栈
### 垃圾回收堆
### 存储区