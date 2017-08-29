# Spring配置文件
定义各种bean，然后供ClasspathXmlApplicationContext对象去解析（初始化各种定义的bean，并交由IOC容器去管理）。

## context:component
`<context:component base-package="..." />`：Spring启动时会查找指定包及其子包下的各种带有`@Component`,`Controller`,`@Service`,`Repository`注解的类，将其初始化并加入到容器当中，该配置还会创建Spring内置的4个bean，用于处理`@Autowired`注解等，所以加上了这个配置才能使用相关的注解。

## aop
`<aop:aspectj-autoproxy />`：自动为spring容器中那些配置`@AspectJ`切面的bean创建代理对象，织入切面，启用Spring对AspectJ注解编程的支持。

## bean

```xml
<bean id="userService" class="com.bundery.userService" scope="singleton">
    <property name="userDAO" ref="userDAO">
</bean>
```

### lazy-init
默认情况下，当容器创建创建的时候(`new ClassPathXmlApplicationContext("classpath:applicationContext.xml")`)，就会初始化配置的bean，但是如果配置了`<bean id="..." class="..." lazy-init="true"/>`，就会等到第一次用到该bean的时候才初始化。

### init-method&destory-method
当配置了`<bean id="..." class="..." init-method="init" destory-method="destory"/>`，会在bean创建的时候的自动调用init方法，容器结束时调用destory方法。

* scope=`"singleton"` 每次使用bean时，只会在第一次调用init方法， 容器结束时，调用destory方法。
* scope=`"prototype"` 每次使用bean时，每次都会先调用init方法，容器结束时，不会调用destory方法。

## dataSource
Spring需要配置dataSource数据库连接池的bean，来获取数据库的连接参数以及数据库Connection。

```xml
<!-- 配置dbcp2数据库连接池 -->
	<bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource"
		destroy-method="close">
		<property name="driverClassName" value="${jdbc.driverClassName}" />
		<property name="url" value="${jdbc.url}" />
		<property name="username" value="${jdbc.username}" />
		<property name="password" value="${jdbc.password}" />
	</bean>
```

## SessionFactory
当与Hibernate框架相结合时，需要使用配置`SessionFactory`的bean，因为Hibernate的数据的持久化等都是通过`Session`来完成的，`SessionFactory`中需要注入`dataSource`获取数据库连接的相关信息。

```xml
<!-- 配置Hibernate5的SessionFactory -->
	<bean id="sessionFactory"
		class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
		<property name="dataSource" ref="dataSource" /><!-- 注入连接池 -->
		<property name="packagesToScan" value="io.bundery.model" /><!-- 
			扫描@Entity注解类 -->
		<property name="hibernateProperties">
			<props>
				<prop key="hibernate.dialect">org.hibernate.dialect.MySQL55Dialect</prop>
				<prop key="hibernate.show_sql">true</prop>
				<prop key="hibernate.format_sql">true</prop>
				<prop key="hibernate.hbm2ddl.auto">update</prop><!-- 有表自动更新表结构，没有就创建表 -->
			</props>
		</property>
	</bean>
```

## transactionManager
以下例子是配置Hibernate5提供的事务管理器，需要注入`SessionFactory`，因为事务管理器需要获取`Session`。原理本质上就是在被Spring事务管理的bean的方法前加上`Trancaction tx=session.beginTransaction();`，方法后加上`tx.commit()`，所以`DAO`层获取`session`一定要使用`sessionFactory.getCurrentSession()`（因为在当前上下文中获得的是同一个session）。

```xml
<!-- 配置Hibernate的事务管理器 -->
<bean id="transactionManager" class="org.springframework.orm.hibernate5.HibernateTransactionManager">
		<property name="sessionFactory" ref="sessionFactory" /><!-- 注入SessionFactory -->
	</bean>
```

## tx:annotation-driven
```xml
<tx:annotation-driven transaction-manager="transactionManager" />
```

该配置用于启用注解`@Trancational`来管理[事务](/content/spring/transcation.html)。

