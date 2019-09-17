# Java基础

### 1.JDK和JRE有什么区别

- JDK：Java Development Kit, Java开发工具包，提供了Java的开发环境和运行环境
- JRE：Java Runtime Environment，Java运行环境，为Java的运行提供了所需环境

如果需要运行java程序，只需安装JRE就可以，如果要编写java程序，需要安装JDK。



### 2.==和equals的区别

```java
public class test{
    public static void main(String[]args)
    {
        String s1 = "aaa";
        String s2 = "aaa";
        String s3 = new String("aaa");
        System.out.println(s1 == s2);//true
        System.out.println(s1 == s3);//false
        System.out.println(s1.equals(s2));//true
        System.out.println(s1.equals(s3));//true
    }
}
```

- ==：对于基本类型，比较的是值是否相同；对于引用类型，比较的是引用是否相同
- `equals()`:本质上就是 == ，只不过String、Integer等重写了 `equals()`方法，把它变成了值得比较
- 一般情况下， == 是比较引用是否相等， `equals()`方法比较值是否相等



### 3.两个对象的hashCode()相同，则equals()也一定为true吗

不一定。

```java
public class test{
    public static void main(String[]args)
    {
    	String s1 = "通话";
    	String s2 = "重地";
    	System.out.println(String.format("s1:%d|s2:%d",s1.hashCode(),s2.hashCode()));
   		System.out.println(s1.equals(s2));//false
    }
}
```

输出结果：

s1:1179395|s2:1179395
false



### 4.final在Java中有什么作用

- final修饰的类叫最终类，该类不能被继承
- final修饰的方法不能被重写
- final修饰的变量叫常量，常量必须初始化，且初始化之后的值不能被修改



### 5.Java中Math.round(-1.5)等于多少

等于 -1 ，因为在数轴上取值时中间值（0.5) 向右取整。

▲在给出数值的绝对值基础上+0.5，然后向下取整



### 6.String是属于基本数据类型吗

String不属于基本数据类型，Java基本数据类型有8种，分别为byte、boolean、char、short、int、float、long、double。



### 7.Java中操作字符串有哪些类，它们之间的区别是什么

有String、StringBuilder、StringBuffer.

区别如下：

- String和StringBuffer、StringBuilder区别在于String声明的是不可变(immutable)对象，每次操作都会生成新的String对象，而StringBuffer、StringBuilder可以在原有对象基础上进行操作。

  ▲在需要经常改变字符串内容d的情况下最好不要用String。

- StringBuilder和StringBuffer最大区别在于，StringBuffer是线程安全的，而StringBuilder是非线程安全的，但StringBuilder性能却高于StringBuffer。

  ▲在单线程环境下推荐用StringBuilder，多线程环境下推荐用StringBuffer。



### 8.String str="a" 与 String str=new String("a")一样吗

不一样，内存分配方式不一样。

- String str=“a"的方式，java虚拟机会将其分配到常量池中。
- String str=new String("a")则会被分配到堆内存中。



### 9.如何将字符串反转

使用StringBuilder或StringBuffer的 `reverse()`方法

```java
public class test{
    public static void main(String[]args)
    {
        StringBuffer s1 = new StringBuffer();
        s1.append("abcd");
        System.out.println(s1.reverse());//输出dcba
    }
}
```

上例中StringBuilder类似用法。



### 10.String（）类的常用方法有什么

- `indexOf()`：返回指定字符的索引值
- `charAt()`：返回指定索引处的字符
- `replace()`：字符串替换
- `trim()`：去除字符串两端空白
- `split()`：分割字符串，返回一个分割后的字符串数组
- `getBytes()`：返回字符串的byte类型数组
- `length()`：返回字符串长度
- `toLowerCase()`：将字符串转成小写字母
- `toUpperCase()`：将字符串转成大写字母
- `substring()`：截取字符串
- `equals()`：字符串比较

```java
public class test{
    public static void main(String[]args)
    {
        String s = "AbCdEfGh";
        System.out.println(s.indexOf('d'));//3
        System.out.println(s.charAt(5));//f
        System.out.println(s.toLowerCase());//abcdefgh
        System.out.println(s.toUpperCase());//ABCDEFGH
        System.out.println(s.length());//8
        String s2 = s.replace('E','e');
        System.out.println(s2);//AbCdefGh
        String s3 = "       abc  ";
        System.out.println(s3.trim());//abc
        String[] s4 = "aa|bb|cc|dd".split("\\|");
        for(int i=0;i<s4.length;i++)
        {
            System.out.println(s4[i]);//aa bb cc dd
        }
        System.out.println(s.substring(2));//CdEfGh
        System.out.println(s.substring(2,4));//Cd
    }
}
```



### 11.抽象类必须要有抽象方法吗

抽象类不一定非要有抽象方法，如下例：

```java
abstract class A{
    public static void DoA()
    {
        System.out.println("AA");
    }
}

public class test{
    public static void main(String[]args)
    {
        A.DoA();//AA
    }
}
```



### 12.普通类和抽象类有哪些区别

- 普通类不能包含抽象方法，抽象类可以包含抽象方法
- 抽象类不能直接实例化，普通类可以直接实例化



### 13.抽象类能使用final修饰吗

不能，定义抽象类就是让其他类继承，如果定义为final该类就不能被继承了



### 14.接口和抽象类的区别

- 实现：抽象类的子类使用extends来继承；接口必须使用implements来实现。
- 构造函数：抽象类可以有构造函数；接口没有构造函数。
- main方法：抽象类可以有main方法且我们可以运行它；接口不能有main方法。
- 实现数量：类可以实现多个接口，但只能继承一个抽象类。
- 访问修饰符：抽象类中的方法可以是任意访问修饰符；接口中方法默认使用public修饰。



### 15.Java中IO流分为几种

- 按功能分：输入流和输出流

- 按类型分：字节流和字符流

  ▲字节流按8位（1个字节）传输以字节为单位输入输出数据，字符流按16位（2个字节）传输以字符为单位输入输出数据



### 16.BIO、NIO、AIO有什么区别

- BIO：Blocking IO 同步阻塞式IO，就是平常用到的传统IO，特点是模式简单使用方便，并发处理能力低。
- NIO：New IO 同步非阻塞IO，传统IO的升级，客户端和服务器端通过Channel（通道）通讯，实现了多路复用。
- AIO：Asynchronous IO，NIO的升级，也叫NIO2，实现了异步非阻塞IO，异步IO操作基于事件和回调机制。

⭐同步与异步

- 同步：发起一个调用后，被调用者未处理完请求之前，调用不返回。
- 异步：发起一个调用后，立刻得到被调用者的回应表示已经接收到请求，但被调用者并无返回结果，此时我们可以处理其他请求，被调用者通常依靠事件，回调等机制来通知调用者其返回结果。

⭐阻塞与非阻塞

- 阻塞：发起一个请求，调用者一直等待请求结果返回，也即当前线程会被挂起，无法从事其他任务，只有当条件就绪才能继续。
- 非阻塞：发起一个请求，调用者不用一直等待结果返回，可先去干其他事情。



### 17.Files的常用方法有哪些

- `Files.exists()`：检测文件路径是否存在。
- `Files.createFile()`：创建文件。
- `Files.createDirectory()`：创建文件夹。
- `Files.delete()`：删除一个文件或目录。
- `Files.copy()`：复制文件。
- `Files.move()`：移动文件。
- `Files.size()`：查看文件个数。
- `Files.read()`：读取文件。
- `Files.write()`：写入文件。

