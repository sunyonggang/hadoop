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

##观察者模式##
观察者模式定义了一种一对多的依赖关系，让多个观察者同时监听某一个主题对象，当主题对象的状态情况发生变化时，他会通知所有的观察者对象，让他们能够改变自己。
###观察者模式的组成###
1.  抽象主题角色：我们一般认为，主体对象是包含所有观察者的一个集合；我们一般通过将抽象主题角色定义为一个接口，在接口中定义addObserver，removeObserve等方法；
2.  抽象观察者角色：我们在观察者之前定义一个抽象观察者的接口，接口中定义应该使用的方法；
3.  具体主题角色：我们在这里定义一个接口，接口内包含一个用来添加或删除观察者的集合；
4.  具体观察者：具体观察者用来观察具体主题角色。

###java与观察者模式###
jdk中使用了大量的观察者模式，它提供了Observer接口与**Observable抽象类**，我们一般诟病jdk的设计正是在于它把抽象主题角色定义为一个抽象类而不是使用接口。

###观察者模式的具体实现##
抽象主题角色
```
package com.observer;

public interface Watched
{
    public void addWatcher(Watcher watcher);
	public void removeWatcher(Watcher watcher);
	public void notifyWatcher(String s);
}

```
抽象观察者模式
```
package com.observer;

public interface Watcher
{
    public void update(String s);
}
```
具体主题角色
```
package com.observer;

import java.util.ArrayList;

public class ConcreteWatched implements Watched
{
    ArrayList<Watcher> list = new ArrayList();
	@Override
	public void addWatcher(Watcher watcher)
	{
		list.add(watcher);
	}

	@Override
	public void removeWatcher(Watcher watcher)
	{
		list.remove(watcher);
	}

	@Override
	public void notifyWatcher(String s)
	{
		for(Watcher watcher : list)
		{
			watcher.update(s);
		}
	}
}

```
具体观察者模式
```
package com.observer;

public interface Watcher
{
    public void update(String s);
}
```
使用测试
```
package com.observer;

public class Client
{
    public static void main(String[] args)
	{
		ConcreteWatched girl = new ConcreteWatched();
		ConcreteWatcher boy1 = new ConcreteWatcher();
		ConcreteWatcher boy2 = new ConcreteWatcher();
		ConcreteWatcher boy3 = new ConcreteWatcher();
		
		girl.addWatcher(boy1);
		girl.addWatcher(boy2);
		girl.addWatcher(boy3);
		girl.notifyWatcher("我难过");
		
		girl.removeWatcher(boy2);
		girl.notifyWatcher("happy");
	}
}

```
##装饰者模式##
装饰者模式动态地将责任附加到对象上，若要扩展功能功能，装饰者提供了比继承更有弹性的替代文件
###设计原则###
对扩展开放，对修改关闭

###装饰者模式的特点###
1.  装饰者以对客户端透明的方式扩展对象的功能，是继承关系的一个替代方案；
2.  装饰者模式以一种透明的方式动态地给对象添加更多的责任，换言之，客户端不会再装饰前或是装饰之后有什么变化；
3.  装饰者模式以一种不需要创造更多子类的情况下，将对象的功能加以拓展。
4.  装饰者模式把客户端的调用委派到被装饰类，装饰模式的关键在于这种拓展完全是透明的；
5.  装饰模式是不必改变原类文件和继承的情况下，动态地扩展一个对象的功能，通过创建一个装饰类对象。
6.  **装饰对象与真实对象拥有相同的接口；装饰对象包含有一个抽象接口的引用；装饰对象接收请求，然后再把请求发送给真是对象；装饰对象在转发这些请求之前或以后可以添加一些附加功能**。

###装饰模式与继承###
1.  用来扩展特定对象的功能，不需要子类，是动态的，在运行时进行分配职责，更灵活；
2.  用来扩展一类对象的功能，需要子类，静态的，在编译时分配职责，但会导致更多的子类产生，缺乏灵活性。

###组成###
*  抽象构建角色：给出一个抽象接口用来规范准备接受附加责任的对象：
```
package com.decorator;

public interface Component
{
    public void saySomething();
}

```
*  具体构建角色：定义一个将要接收附加责任的类（被装饰者）：
```
package com.decorator;

public class ConcreteCompoent implements Component
{
    @Override
	public void saySomething()
	{
		System.out.println("ConcreteCompoent is running");
	}

}

```
*   装饰角色：拥有抽象构建角色的一个引用，并且实现了与抽象接口一致的接口：
```
package com.decorator;

public class Decorator implements Component
{
    public Component component;
	@Override
	public void saySomething()
	{
		component.saySomething();
	}
	public void doThing()
	{
		System.out.println("Decorator haha");
	}
}

```
*   具体装饰角色：负责给构件对象添加上额外的责任（装饰者）：
```
package com.decorator;

public class ConcreteDecorator extends Decorator
{
    public Component component;
	public ConcreteDecorator(Component component)
	{
		this.component = component;
	}
	@Override
	public void saySomething()
	{
		component.saySomething();
		this.doThing();
	}
	public void doThing()
	{
		System.out.println("ConcreteDecorator haha");
	}
}

```
*   测试
```
package com.decorator;

public class Client
{
    public static void main(String[] args)
	{
		Component component = new ConcreteDecorator2(new ConcreteDecorator(new ConcreteCompoent()));
		component.saySomething();
	}
}

```