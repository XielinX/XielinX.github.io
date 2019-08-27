# Java8新特性
## 一、Lambda表达式
+ 特征
  + 可选类型声明:不需要声明参数类型
  + 可选的参数圆括号:无参,必须又圆括号,一个参数无需定于圆括号,多个参数需要定义圆括号
  + 可选的花括号:如果主体包含一个语句,可以不需要花括号
    + 一个语句: 单行语句吧,如a+b; System.out.println("hello kittly");  
  + 可选的返回关键字:如果主体只有一个表达式  
+ 格式
  + (parameters) -> expression
  + (parameters) -> {expression}
## 二、Lambda表达式应用
```java
// 1. 不需要参数,返回值为 5  
() -> 5  
  
// 2. 接收一个参数(数字类型),返回其2倍的值  
x -> 2 * x  
  
// 3. 接受2个参数(数字),并返回他们的差值  
(x, y) -> x + y  
  
// 4. 接收2个int型整数,返回他们的和  
(int x, int y) -> x + y  
  
// 5. 接受一个 string 对象,并在控制台打印,不返回任何值(看起来像是返回void)  
(String s) -> System.out.print(s)

// 6. 匿名函数
List<String> names1 = new ArrayList<String>();
	names1.add("Google ");
	names1.add("Runoob ");
	names1.add("Taobao ");
	names1.add("Baidu ");
	names1.add("Sina ");
//排序	
public void sort(List<String> list){
	//普通写法
	Collections.sort(list, new Comparator<String>() {
		@Override
		public int compare(String o1, String o2) {
			return o1.compareTo(o2);
		}
	});
//String s1="";
//匿名函数Lambda写法
Collections.sort(list,(s1,s2) -> s1.compareTo(s2));//不能有同名的局部变量s1
}

// 7.多线程
new Thread(new Runnable() {
	@Override
	public void run() {
	System.out.println("当前线程:" + Thread.currentThread().getName());
		}
	}).start();
}
//多线程lambda写法
new Thread(() -> System.out.println("当前线程:" + Thread.currentThread().getName())).start();
```

