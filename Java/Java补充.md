# Java随笔
## 一、 面向对象(oop)
### 1.0 理解
+ SOLID原则
### 1.1 特征
+ 继承
+ 多态
+ 封装
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

+ retention : 保留

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
+ 注明保留时间 @Retention
+ 作用目标 @Target
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
## 三、Volatile
单词: **volatile**  英[ˈvɒlətaɪl]  adj 不稳定,异变
Volatile 是一个类型修饰符,
### 3.1 概念补充
Java内存模型的: 可见性,原子性,有序性

+ 可见性
指线程之间的可见性,一个线程修改的结果,另一个线程马上就能看到

+ 原子性
原子是化学反应中最小微粒,不可再分割.就是指一个操作不可再分割,例如,int a = 0;是一个原子操作,又如 a++ ,实际是a= a + 1;可分割不具有原子性,非原子性操作可能存在线程不安全问题,需要同步技术变成原子性操作.  java.util.concurrent包,提供一些原子类,AtomicInteger,AtomicReference

+ 有序性
禁止指令重排序/优化,而省略某些步骤,即预计想有序执行4步指令,由于编译器优化,可直接一步到位,不达预期,可使用Volatile解决

### 3.2 volatile的特性
+ 可见性
线程之间对变量操作的可见性,不允许线程内部缓存和重排序，即直接修改内存

+ 有序性
禁止指令重排

+ 原子性
只能保证对单次读/写的原子性。i++ 这种操作不能保证原子性。

## 四、ThreadLocal
多个线程对一个变量进行写入的时候，为了保证线程安全,一般需要使用同步措施
ThreadLocal是除了加锁这种同步方式之外的一种保证一种规避多线程访问出现线程不安全的方法,
创建一个变量时,当每一个线程都去访问时,访问的是自己的线程变量(本地副本)
线程本地变量

ThreadLocal类,它提供线程本地变量，如果创建一乐ThreadLocal变量，那么访问这个变量的每个线程都会有这个变量的一个副本
### 1.1 使用
```java
public class ThreadLocalDemo {
   
public static void main(String[] args) {
     ThreadLocal<String> threadLocal = new ThreadLocal<>();
     // public void set(T value);
     threadLocal.set("localVar1")
     // public T get();
     String str = threadLocal.get();
     // public void remove();
     threadLocal.remove();
     
  }

}
```
## 五、多线程
### 相关概念
+ 进程
操作系统中,一个应用程序就代表一个进程,进程之间是独立的资源与空间

+ 线程
线程是进程的组成部分,一个进程可以有多个线程,一个线程必须有一个父进程,线程可以独立运行,可以共享内存
### 生命周期
+ 创建状态(new)
+ 就绪状态(runnable)
+ 运行状态(running)
+ 阻塞状态(blocked)
+ 死亡状态(dead)
### 多线程创建
#### 3种创建方式
> 1. 继承Thread类
```java
public class MyThread extends Thread {
    
    
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println("线程:" +  getName() + ":" + i);
        }
    }
    
    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();
    
        t1.start();
        t2.start();
  }
}
```
> 2. 实现Runnable接口
```java
public class MyRunnable implements Runnable {
       
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println("线程:" + Thread.currentThread().getName() + "---->" + 1);
        }
    }
    
    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();
        
        t1.start();
        t2.start();
        
    }
}
```

> 3. 实现Callable\<E>接口,再使用FutureTask\<E>类包装Callable对象
```java
public class ThirdThread implements Callable<Integer> {
    
    @Override
    public Integer call() throws Exception {
        int i = 0;
        for (;  i < 100; i++) {
            System.out.println(Thread.currentThread().getName()+ " 的i值:" + i);
        }
        return i;
    }
    
    public static void main(String[] args) {
        ThirdThread t3 = new ThirdThread();
        // FutureTask<E>类包装Callable对象
        FutureTask<Integer> futureTask = new FutureTask<>(t3);
        for (int i = 0; i < 100; i++) {
            System.out.println(Thread.currentThread().getName()+ " 的i值:" + i);
            if (i == 20){
                new Thread(futureTask,"有返回值得线程").start();
            }
        }
        
     try {
            // 获取返回值
            System.out.println("子线程得返回值:" + futureTask.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }   
```
#### 3种创建方式比较
+ 使用start()方法启动线程,这样run()方法就是一个线程执行体,不能直接调用run()方法,否则就相当于调用一个函数而已
+ 继承Thread类,参考类的单继承,接口多继承
+ Runnable是接口,run()方法是public void 得
+ Callable\<E>是接口,call()方法是有返回值,会抛出异常


## JVM
### 指令集
### 寄存器
### 类文件的格式
### 栈
### 垃圾回收堆
### 存储区