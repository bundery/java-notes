# spring注解

使用注解之前，要先在application.xml文件中配置`<context:acomponent-scan base-package="com.bundery">`，配置这个标签之后，Spring默认就会初始化4个相关的bean（用于处理相关注解），会在容器启动时扫描指定包下注解了@Component的类，并初始化为bean交由spring管理。

## @Component
@Component("...")/@Controller("...")/@Service("...")/@Repository("...")声明在类上，就可以把此类作为一个bean交给spring容器管理（括号内的内容为bean指定id的值）。

## @Scope
@Scope("singleton")/@Scope("prototype")，注解于类上，用于指定bean的生命周期。

## @Required
注解在bean的setter方法上，要求必须注入相关属性，类似于`@Override`的编译时检查。

## @AutoWired
可以注解在bean的setter方法上，然后注入指定的属性，默认按byType注入，如果容器中找到不止一个匹配的bean，则抛出异常。如果象按名称装配，可以配合`@Qualifier`使用，如果允许要注入的属性值为`null`，则可以使用`@AutoWired(required="false")`。

```java
@Autowired
public void setUserDao(@Qualifier("userDao") UserDao userDao){
    this.userDao=userDao;
}
```

## @Resource
可以注解在bean的setter方法上，然后注入指定的属性。

* 如果不指定name和type，即@Resource()，则按名称装配，找不到按类型装配。
* 如果只指定name，即@Resource(name="...")，则按名称装配，根据name在ioc容器中找匹配的bean，找不到抛出异常。
* 如果只指定type，即@Resource(type="...")，则按类型装配，找不到或者找出多个，则抛出异常。

## @PostConstruct
注解于方法上，相当于init-method。

## @PreDestory
注解于方法上相当于destory-method。

## @Transcational
注解于类或者方法上（标柱在类上，则对所有的public方法起作用，标注在方法上的注解会覆盖掉该类上的注解），用于声明式[事务](/content/spring/transcation.html)管理。要想使用该注解，需要在`beans.xml`文件中添加`<tx:annotation-driven transaction-manager="transactionManager" />`，该配置就是把所有已加载进Spring-ioc容器中带有`@Transcational`注解的bean交给指定的事务管理器进行事务管理。
