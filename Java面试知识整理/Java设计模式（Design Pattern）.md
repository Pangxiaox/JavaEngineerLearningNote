# Java设计模式（Design Pattern）

### ⭐1. GoF设计模式

##### 1.1 创建型设计模式

抽象工厂模式，建造者模式，工厂方法模式，原型模式，单例模式

##### 1.2 结构性设计模式

适配器模式，桥接模式，组合模式，装饰器模式，外观模式，享元模式，代理模式

##### 1.3 行为型设计模式

责任链模式，命令模式，解释器模式，迭代器模式，中介者模式，备忘录模式，观察者模式，状态模式，策略模式，模板方法模式，访问者模式



### 2. 几个设计模式介绍

##### ⭐2.1 单例模式

一个应用程序里，某个类的实例对象只有一个，没有办法去new，因为构造器是被private修饰，一般通过getInstance()方法获取它们的实例。

getInstance()方法返回值是一个对象的引用，并不是一个新的实例。

**懒汉式（线程不安全）**

```java
public class Singleton{
	private static Singleton singleton;

	private Singleton(){
	}
	
	public static Singleton getInstance(){
		if(singleton ==  null){
			singleton = new Singleton();
		}
		return singleton;
	}
}
```

**懒汉式（线程安全）**

```java
public class Singleton{
	private static Singleton singleton;

	private Singleton(){
	}
	
	public static synchronized Singleton getInstance(){
		if(singleton ==  null){
			singleton = new Singleton();
		}
		return singleton;
	}
}
```

**饿汉式**

```java
public class Singleton{
	private static Singleton singleton = new Singleton();
	private Singleton(){
	}
	public static Singleton getInstance(){
		return singleton;
	}
}
```

**静态内部类**

```java
public class Singleton{
	private static class SingletonHolder{
		private static final Singleton INSTANCE = new Singleton();
	}
	private Singleton(){}
	public static final Singleton getInstance(){
		return SingletonHolder.INSTANCE;
	}
}
```

**枚举**

```java
public enum Singleton{
	INSTANCE;
	public void whateverMethod(){
	}
}
```

避免多线程同步问题，能防止反序列化重新创建新的对象

**双重校验锁**

```java
public class Singleton{
	private volatile static Singleton singleton;
	private Singleton(){}
	public static Singleton getSingleton(){
		if(singleton == null){
			synchronized(Singleton.class){
				if(singleton == null){
					singleton = new Singleton();
				}
			}
		}
		return singleton;
	}
}
```



##### 2.2 观察者模式

又叫做“发布——订阅”模式，定义了一种一对多的依赖关系，让多个观察者对象同时监听某一个主题对象，这个主题对象在状态变化时，会通知所有的观察者对象，是他们可以自动更新自己。

放图



##### 2.3 装饰器模式

对已有的业务逻辑进一步封装，使其增加额外功能，如Java中的IO流使用了装饰器模式。



##### 2.4 适配器模式

作为两个不兼容的接口之间的桥梁，将一个类的接口转换成客户希望的另外一个接口。



##### 2.5 工厂模式

**工厂模式**：定义一个创建对象的接口，让其子类自己决定实例化哪一个工厂类，工厂模式使其创建过程延迟到子类进行。

**抽象工厂模式**：是围绕一个超级工厂创建其他工厂。提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。



##### 2.6 代理模式

分为静态代理和动态代理。为其他对象提供一种代理以控制对这个对象的访问。



### 3. 简单工厂和抽象工厂区别

产品族：位于不同产品等级结构中，功能相关联的产品组成的家族。

例如，BMWCar和BenzCar就是两个产品树（产品层次结构），BenzSportsCar和BMWSportsCar就是一个产品族，同理BMWBusinessCar和BenzBusinessCar也是一个产品族。

**简单工厂和抽象工厂区别在于需要创建对象的复杂程度上。抽象工厂模式是最为抽象、最具一般性的。抽象工厂模式用意为：给客户端提供一个接口，可以创建多个产品族中的产品对象。**

使用抽象工厂模式还要满足以下条件：

- 系统中有多个产品族，而系统一次只可能消费其中一族产品
- 同属于同一个产品族的产品一起使用

