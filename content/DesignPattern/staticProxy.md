# 静态代理

实现步骤：
1. 和委托类实现同一个接口（要求委托类必须要实现接口），并覆盖接口中的方法。
2. 传入委托对象。
3. 在覆盖的方法中，写入逻辑，并调用委托对象的方法。

特点：
1. 由程序员创建代理类的源码，静态就是指的程序运行前就已经存在了代理类的字节码文件，代理类和委托类的关系在运行前就确定了。
2. 代理类只能代理**接口中定义的方法**。
3. 获得代理对象需要先提供委托对象。

优点：
1. 业务类只需关注自身逻辑，这是代理的共同优点。

缺点：
1. 代理类只能代理一种类型的接口，如果要代理其他接口，就需要再新建代理类。
2. 如果接口中新增一个方法，除了所有实现类要实现这个方法外，所有代理类也需要实现这个方法，增加了维护的难度。

```java
//接口：
public interface UserService{
    public void add();
    public void delete();
}

//委托类
public class UserServiceImpl implements UserService{

    @Override
     public void add(){
        System.out.println("add..Success");
     }

    @Override
     public void delete(){
        System.out.println("delete..Success");
     }

}

//代理类
public class ProxyUserService implements UserService{

    private UserService target;

    public ProxyUserService(UserService target){
        this.target=target;
    }

    //将请求分派给委托类执行
    @Override
     public void add(){
        System.out.println("开始运行时间："+System.currentTimeMillis());
        target.add(); //调用委托类的方法
        System.out.println("开始运行时间："+System.currentTimeMillis());
     }

    @Override
     public void delete(){
        System.out.println("开始运行时间："+System.currentTimeMillis());
        target.delete();
        System.out.println("开始运行时间："+System.currentTimeMillis());
     }

}

//产生UserService的工厂，使用者使用此工厂获得UserService对象，使用者并不知道也不在意拿到的是委托类还是代理类对象，只要有UserService的相关功能就行了
public class UserServiceFactory{
    public static UserService getInstance(){
        return new ProxyUserService(new UserServiceImpl());
    }
}

//产生代理对象
public class UserAction(){
    public void save(){
        UserService proxy=UserServiceFactory.getInstance();
        proxy.add();
    }
}
```