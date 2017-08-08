#Spring 容器

springIOC容器的核心是ApplicationContext接口，实现类有ClasspathXmlApplicationContext，该容器可以读取xml文件，并初始化配置在文件中的bean，以及管理他们的整个声明周期。
## 获取容器对象

1. 手动初始化容器
ApplicationContext ac=new ClasspathXmlApplicationContext("classpath:applicationContext.xml");这种方式创建容器时会重新创建配置文件中配置的bean。

2. 通过Spring提供的工具类获取ApplicationContext对象

* ApplicationContext ac1 = WebApplicationContextUtils.getRequiredWebApplicationContext(ServletContext sc);如果applicationContext对象不存在，就抛出异常。

* ApplicationContext ac2 = WebApplicationContextUtils.getWebApplicationContext(ServletContext sc);
  如果applicationContext对象不存在，返回null。

3. 自定义类实现`ApplicationContextAware`接口
自定义类实现该接口，并配置在applicationContext.xml文件中，spring初始化该类时，会自动把applicationContext对象注入。