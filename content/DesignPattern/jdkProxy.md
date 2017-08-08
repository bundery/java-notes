# JDK代理

JDK动态代理通过反射类Proxy和InvocationHandler回调接口实现，与静态代理相比较，最大的好处是接口声明的所有方法都被移到了invoke方法里，再也不怕接口方法多，或者接口方法变更了。

特点：
- 代理对象的生成要求委托类必须要**实现接口**。
- 需要传入**委托对象**，因为JDK代理动态生成的代理类中没有委托类的原始方法，所以要调用原方法必须要有委托对象。
- 代理类是`java.lang.reflect.Proxy`对象的**子类**且实现了委托类所有的接口，JDK代理只代理了以下方法：
    + Proxy类的toString()
    + Proxy类的hashCode()
    + Proxy类的equals()
    + 接口中的所有方法
- 内部调用的方法不会被代理，比如add()方法中调用了delete()方法，则调用的delete()方法还是委托对象的delete()方法，因为JDK代理调用代理对象的add()-->调用invocationHandler.invoke(proxy,method,args)-->调用委托对象.add()，所以delete()方法还是由委托对象来调用。这点与**CGLIB**代理不同。
- 不能代理**private**、**final**、**static**方法。
    + private：假如可以代理private方法，私有方法不能被外界访问，就算内部调用，调用的也不是代理后的private方法，所以毫无意义。
    + final：子类不能出现父类同名的final方法，无法被代理，所以只代理了Proxy对象的toString()、hashCode()及equals()方法（继承自Object类）。
    + static：假如可以代理static方法，代理类的static方法不用创建代理类就能直接访问，那么`invocationHandler.invoke(proxy,method,args)`怎么传递proxy对象呢。

```java
//创建InvocationHandler
public class MyInvocationHandler implements InvocationHandler {

	private Object target; //委托类可以是任何类型

	public MyInvocationHandler(Object target) {
		this.target=target; //invocationHandler对象要包含委托对象，因为要调用委托对象的方法
	}

    //proxy:代理对象，method：委托类的方法，args：方法的参数
	@Override
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		System.out.println("开始执行时间："+System.currentTimeMillis());
		Object result = method.invoke(target, args); //执行委托类的方法
		System.out.println("结束执行时间："+System.currentTimeMillis());
		return result;
	}

}

//生成代理对象
public class Client {

	@Test
	public void testAddUser() {
		UserService userService=new UserServiceImpl();
		InvocationHandler h=new MyInvocationHandler(userService);
		UserService proxy = (UserService)(Proxy.newProxyInstance(userService.getClass().getClassLoader(), userService.getClass().getInterfaces(), h));
		proxy.add(); //转到invocationHandler的invoke(proxy,Method(add),null)方法
	}

}

```

`Object proxy=Proxy.newInstance(classLoader,interfaces,invocationHandler)`做了以下几件事：

1. 根据参数classLoader和interfaces创建$Proxy0代理类，实现了interfaces接口，并继承Proxy类。
2. 创建出一个$Proxy0对象并通过构造方法传入invocationHandler。

```java

//Proxy类
class Proxy{  
    InvocationHandler h=null;  
    protected Proxy(InvocationHandler h) {  
        this.h = h;  
    }  
    ...  
}

//运行期间动态生成的$Proxy0类
public final class $Proxy0 extends Proxy implements Subject {  
    private static Method m0;  
    private static Method m1;  
    private static Method m2;  
    private static Method m3;  
    private static Method m4;  
  
    static {  
        try {  
            //object.hashCode()方法
            m0 = Class.forName("java.lang.Object").getMethod("hashCode" , new Class[0]);
            //object.equals()方法
            m1 = Class.forName("java.lang.Object").getMethod("equals" , new Class[] { Class.forName("java.lang.Object") });
            //object.toString()方法
            m2 = Class.forName("java.lang.Object").getMethod("toString", new Class[0]);
            //userService.add()方法
            m3 = Class.forName("*.UserService").getMethod("add", new Class[0]);
            //userService.delete()方法
            m4 = Class.forName("*.UserService").getMethod("delete", new Class[0]);
        } catch (NoSuchMethodException nosuchmethodexception) {  
            throw new NoSuchMethodError(nosuchmethodexception.getMessage());  
        } catch (ClassNotFoundException classnotfoundexception) {  
            throw new NoClassDefFoundError(classnotfoundexception.getMessage());  
        }  
    }
  
    public $Proxy0(InvocationHandler invocationHandler) {  
        super(invocationHandler);  //传入invocationHandler对象
    }  
  
    @Override  
    public final int hashCode() {  
        try {  
            return ((Integer) super.h.invoke(this, m0, null)).intValue();  //调用InvaocationHandler对象的invoke()方法
        } catch (Throwable throwable) {  
            throw new UndeclaredThrowableException(throwable);  
        }  
    }  

    @Override  
    public final boolean equals(Object obj) {  
        try {  
            return ((Boolean) super.h.invoke(this, m1, new Object[] { obj })) .booleanValue(); 
        } catch (Throwable throwable) {  
            throw new UndeclaredThrowableException(throwable);  
        }  
    }  

    @Override  
    public final String toString() {  
        try {  
            return (String) super.h.invoke(this, m2, null); 
        } catch (Throwable throwable) {  
            throw new UndeclaredThrowableException(throwable);  
        }  
    }  
   //每次调用proxy.add()/delete()/equals()/hashCode()/toString()方法时，都会转到invocationHandler的invoke()方法上。
    public final void add() {  
        try {  
            super.h.invoke(this, m3, null);  
            return;  
        } catch (Error e) {  
        } catch (Throwable throwable) {  
            throw new UndeclaredThrowableException(throwable);  
        }  
    }  

    public final void delete() {  
        try {  
            super.h.invoke(this, m4, null);  
            return;  
        } catch (Error e) {  
        } catch (Throwable throwable) {  
            throw new UndeclaredThrowableException(throwable);  
        }  
    }  
  
} 

```
