# cglib代理
使用cglib(Code Generation Library)实现动态代理，不要求委托类必须实现接口，底层采用asm框架生成代理类的字节码，需要导入第三方jar包。

步骤：
1. 实现MethodInterceptor，定义方法的拦截器。
2. 利用Enhancer类生成代理类对象。
    1. 生成委托类的子类的二进制字节码。
    2. 通过Class.forName加载二进制字节码到内存，生成*.Class对象。
    3. 生成并初始化代理类对象。

特点：
1. 不需要实现接口，代理对象是委托对象的子类。
2. 不需要传入委托对象。（因为子类(代理类)可以访问父类(委托类)的方法）
3. 内部调用的非私有方法也会被代理。比如add()方法中调用了delete()方法，调用代理对象的add()方法-->调用myMethodInterceptor.interceptor(...)-->调用methodProxy.invokeSuper(proxy, args);-->调用代理对象的CGLIB$add$0()方法-->调用super.add()；所以最终调用了super.add()，方法是运行期间动态绑定的，内部的delete()方法由于已被代理类重写，所以调用的
是代理类的方法。这点和`JDK代理`不一样。
4. 不能代理`private`、`final`、`static`方法。
    - private：假如代理了private方法，私有方法不能被外界访问，就算内部调用，调用的也不是代理后的private方法，因为`super`对象访问不到代理类的`private`方法，访问的还是委托类的`private`方法，所以毫无意义。
    - final: 子类不能出现父类同名的final方法，无法被代理。
    - static: 假如代理了static方法，代理类的static方法不用创建代理类就能直接访问，那么`myMethodInterceptor.interceptor(...)`怎么传递proxy对象呢。

```java
//MethodInterceptor
public class MyMethodInterceptor implements MethodInterceptor {
    //proxy: 代理对象 method:委托类中委托的方法对象 args:参数 methodProxy:代理方法的MethodProxy对象
	@Override
	public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
		System.out.println("开始运行时间："+System.currentTimeMillis());
		Object result = methodProxy.invokeSuper(proxy, args);//每个被代理的方法都对应一个MethodProxy对象，最终调用委托类的原方法
		System.out.println("结束运行时间："+System.currentTimeMillis());
		return result;
	}

}

//生成代理对象
public class Client {

	@Test
	public void testAddUser() {
		Enhancer enhancer=new Enhancer();
		enhancer.setSuperclass(UserServiceImpl.class);
		enhancer.setCallback(new MyMethodInterceptor());
		UserServiceImpl proxy=(UserServiceImpl)(enhancer.create());
		proxy.add();
	}

}
```

`enhancer.create();`此句先是生成了**委托类的子类**的二进制代码，生成的代理类为每个委托方法都生成了相应的俩个方法，以`add()`方法来讲，生成了一个重写的`add()`方法（内部调用myMethodInterceptor.intercept()），另外一个是`CGLIB$add$0()`方法(内部直接调用父类/委托类的原方法即:super.add())。


```java
//生成的代理类大致代码
public class UserServiceImpl$CGLIB extends UserServiceImpl implements Factory
{
    ...otherCode...

    //覆盖委托类中的add方法
    public final void add() {
            myMethodInterceptor.intercept(this[代理对象], Method[原委托类中的方法对象], args[方法参数], MethodProxy);
            return;
    }

    //同时生成的另外一个方法(MethodProxy.invokeSuper()就是调用的这个方法)
    final void CGLIB$add$0() {
        super.add();
    }

    ...otherCode...
}
```
