##1单例模式##
定义：确保一个类只有一个实例，并提供一个全局访问点；
使用场景：有一些对象我们只需要一个，例如：线程池，缓存，对话框，注册表的对象，日志对象等，假如出现多个反而会出现问题；
与全局变量相比：首先你要明白第一点，单例模式既然能出现这么多年就说明它肯定有它的优势才对；另一方面：使用单例模式相比如全局变量，它既有全局变量的优点，提供一个全局访问点，同时没有全局变量所带来一开始就要求创建好对象的缺点。

###思维过程###
1.  我要创建一个对象:new MyClass();
2.  只能创建一个：将构造方法的access modifier设置为private；
3.  只能在类的内部创建，使用方法返回给调用类（client），使用MyClass.createNewInstance();因此得到方法
```
    public static MyClass getNewInstance()
    {
        return obj;
    }
```
4.  obj来自于Singleton自身定义的实例，并进行new MyClass();由于对client透明，getNewInstance()能够调用，确定修饰符为private static.

###code###
```
package com.singleton;

public class SingletonTest
{
    public static void main(String[] args)
	{
		Singleton singleton = Singleton.getNewInstance();
		System.out.println(singleton.hashCode());
		Singleton singleton1 = Singleton.getNewInstance();
		System.out.println(singleton1.hashCode());
		System.out.println(singleton == singleton1);
	}
}
class Singleton
{
	private static Singleton singleton = new Singleton();
	private Singleton()
	{
		
	}
	public static Singleton getNewInstance()
	{
		return singleton;
	}
}

```
###说明###
单例模式还有一种，可以将创建对象放到方法中：这样在多线程的环境下可能不止产生一个对象。
```
public static Singleton getNewInstance()
    {
		if (singleton == null)
		{
			singleton = new Singleton();
		}
		return singleton;
	}
```

##策略模式##
定义：定义了算法族，分别封装起来，让他们之间可以相互替换，此模式在于让算法的变化独立于使用算法的客户；
设计原则：找出应用中可能的变化之处，把它们独立起来，不要和那些不需要变化的代码混在一起；针对接口编程，不要针对实现编程；多用组合，少用继承。
使用场景：模拟鸭子--给鸭子添加fly(),重写quack()等。

###思考过程###
1.  我要使对于对象的操作根据不同的类型进而得到不同的结果；
2.  定义一个接口，接口中存在一个方法；
3.  写不同的子类去实现接口，重写里面的方法；
4.  编写Environment，在其中存在接口的一个引用，并写上了get与set方法；
5.  在client端，创建子类的对象，创建Environment的对象，调用env.perform()方法；

###StrategyI.java###
```
package com.strategy;

public interface StrategyI
{
    public int calc(int a, int b);
}
```
###AddStrategy.java###
```
package com.strategy;

public class AddStrategy implements StrategyI
{

    @Override
	public int calc(int a, int b)
	{
		return a + b;
	}
}

```
###SubtractStrategy.java, MultiplyStrategy.java, DivideStrategy.java类似###
###Environment.java###
```
package com.strategy;

public class Environment
{
    public StrategyI strategy;
	public Environment(StrategyI s)
	{
		strategy = s;
	}
	public StrategyI getStrategy()
	{
		return strategy;
	}
	public void setStrategy(StrategyI s)
	{
		strategy = s;
	}
	public void perform()
	{
		System.out.println(strategy.calc(3, 4));
	}
}

```
###Client.java###
```
package com.strategy;

public class Client
{
    public static void main(String[] args)
	{
		StrategyI s1 = new AddStrategy();
		Environment env1 = new Environment(s1);
		env1.perform();
		
		StrategyI s2 = new SubtractStrategy();
		Environment env2 = new Environment(s2);
		env2.perform();
		
		env2.setStrategy(s1);
		env2.perform();
	}
}

```
###not over###