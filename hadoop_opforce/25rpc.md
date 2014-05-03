##什么是RPC##
1.    rpc就是remote produce call（远程方法调用），是计算机的一个程序可以远程调用另一台计算机的子程序而不用关心底层的网络细节，对于我们来说是透明的，常见于分布式网络通信中；
2.    hadoop进程间的通信是通过rpc来完成的，比如nn与dn之间。

##RPC的过程##
1.  rpc采用的是c/s模式；
2.  Client端发送过来一个带有参数的请求到Server端；
3.  Server端接收请求并计算结果，然后将结果送回到Client端，client得到结果之后继续执行。

##RPC的特点##
1.  透明性：就像调用本地方法一样；
2.  高性能：一个Server可以并发处理来自多个Client的请求；
3.  可控性：jdk中提供的RPC框架RMI过于庞大且控制较少，因此hadoop实现了自己的rpc通信机制。

##hadoop的rpc##
1.  序列化层；
2.  函数调用层；
3.  网络传输层；
4.  服务端框架层。

采用的技术：**序列化，动态代理，反射，非阻塞io**
##java动态代理##
1.  动态代理可以提供对另一个对象的访问，同时隐藏实际对象的具体事实，代理对象对客户隐藏了实际对象；
2.  动态代理可以对对象做一些处理在不允许直接访问其他类的情况下；
3.  java中支持对动态代理的接口的实现。

###实际代码###
interface CalculationProtocol
```
package com.dynamicproxy;

public interface CalculationProtocol
{
    public int add(int a, int b);
	public int sub(int a, int b);
}
```
class ConcreteCalculation
```
package com.dynamicproxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class ConcreteCalculation implements CalculationProtocol
{
    @Override
	public int add(int a, int b)
	{
		return a+b;
	}
	@Override
	public int sub(int a, int b)
	{
		// TODO Auto-generated method stub
		return a-b;
	}
	public static void main(String[] args)
	{
		CalculationProtocol cp = new ConcreteCalculation();
		MyHandler mh = new MyHandler(cp);
		CalculationProtocol proto = (CalculationProtocol)Proxy.newProxyInstance(cp.getClass().getClassLoader(), cp.getClass().getInterfaces(), mh);
		
		System.out.println(proto.add(3, 4));
	}

}
class MyHandler implements InvocationHandler
{
	Object obj;
	public MyHandler(Object obj1)
	{
		this.obj = obj1;
	}
	@Override
	public Object invoke(Object proxy, Method method, Object[] args)
			throws Throwable
	{
		System.out.println("before");
		Object obje = method.invoke(obj, args);
		System.out.println("after");
		return obje;
	}
	
}
```
