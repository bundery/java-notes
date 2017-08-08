# JDBC相关技术

## DataSource
是一个提供标准化的取得数据库连接方式的接口，该接口有一个getConnection()方法，用以取得数据库连接。实现一个数据库连接池统一实现此接口。

## Connection Pool
由于数据库连接的创建和销毁非常消耗系统资源，因此可以提前创建多个Connection放在一个容器里，使用时直接从容器中取，使用完再放回池里供下个请求使用。从连接池中获取到的连接，通常已经被扩展了，当调用connection的close方法时，实际上并不会关闭连接，而是通知连接池，这个连接已经又可以使用了。

一个标准的数据库连接池至少需要：
* 实现dataSource接口
* 有一个容器可以管理多个连接
* 必须拥有回收连接的能力
* 数据库用户名及连接密码
* ConnectionURL（如："jdbc:mysql://localhost:3306/helloWorld"）
* 相应的数据库驱动（如：com.mysql.jdbc.Drive）
* MaxPoolSize 连接池中最大连接数
* MinPoolSize 连接池中最小连接数
* ...

## Hibernate Session
Hibernate的session(不是web层的`HttpSession`)相当于封装了一个JDBC-Connection的对象，是应用程序和数据库之间的一个会话。

* Session对象是有生命周期的，以Transction对象的事务开始和结束边界，生命周期绑定在了`Transcation`上。
* session对象不是线程安全的，应该避免多个线程共享同一个session实例。
* session对象内部有一个缓存，被称为Hibernate的第一缓存，每个session实例都有自己的缓存。

    - 缓存的作用：减少数据库的频繁访问，保证缓存中的对象与数据库同步，缓存中的对象称为持久化对象。
    - session加载对象后会为对象值类型的属性复制一份快照，当session清理缓存时，比较当前对象和它的快照就可以知道哪些属性发生了变化，从而会进行数据库的同步。
    - 当调用commit()，flush()（session.close()时会调用flush()）时会清理缓存，保证查询结果能反映对象的最新状态。

* 通过`session=sessionFactory.getSession()`或者`session=sessionFactory.getCurrenSession()`获得。

    - getCurrentSession()创建的session会绑定到上下文中，在commit前，同一个上下文中再次调用`getCurrentSession()`得到的还是同一个session，如果在一个`userService`方法中调用多个`xxDao`的方法，这时候`xxDao`中只能使用`getCurrentSession()`，不然每个xxDao得到的都是不同的session，就没办法在`userSerice`中进行事务管理了。getCurrentSession()会在commit或者rollback操作后自动close()，不能手动close()。

        + 上下文可以设定Thread（事务在单个数据库）或者JTA（分布式，事务横跨多个数据库），需要在`SessionFactory`中配置`<property name="hibernate.current_session_context_class">thread/jta</property>`指定上下文的类型。

    - openSession()得到的都是新创建的session，需要手动close()。

* session对象使用完需要及时close()，因为session对象中不仅保持着一个connection的连接，而且还保存着一些数据的缓存，close操作用于释放内存空间，将持久太对象变为托管态，防止不经意间修改数据库，但是不一定关闭了connection的连接，如果是从连接池拿到connection，就是通知连接池回收该connection供其他请求使用。

## Hibernate Transaction
Hibernate本身是不具备Transcation处理功能的，Hibernate的Transaction实际上是底层的JDBC Transaction的封装（如果不进行配置，默认使用JDBC Transaction），或者是JTA Transaction的封装。
Hibernate相关操作对应JDBC的代码：

```java
Session session=sessionFactory.openSession(); -->Connection conn=...
Trancaction tx=session.beginTransaction();    -->conn.setAutoCommit(false);
session.save(user);
tx.commit();                                  -->conn.commit();
session.close();                              -->conn.close();
```
所以Hibernate的Transcation很简单而已，就是调用了`conn.setAutoCommit(false)`而已，如果执行了这段代码，程序必须调用手动commit或者rollback操作数据才能提交，JDBC默认AutoCommit为true，即每次更新操作都会自动提交到数据库。

## Hibernate SessionFactory
* 用来创建和管理Session对象，是产生Session对象的工厂。
* 除非需要访问多个数据库，否则每个应用程序中只需要一个SessionFactory。
* 因为产生Session对象的代码都在SessionFactory对象的方法里，所以SessionFactory是线程安全的。
* 因为每个Session对象中都有一个数据库的连接，所以产生session的工厂里边一定有很多的数据库连接，也就是SessionFctory里维护着一个数据库连接池，当产生session对象时，它会从连接池里拿出一个connection交给seesion对象，当session.close()时，就会清空Session的缓存（Hibernate一级缓存）并把connection连接返还给SessionFactory。