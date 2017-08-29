# Struts2框架传输json数据

1. 导入`struts2-json-plugin`包，它是Struts2内置的Json插件，它可以把对象有getter方法的属性全部被转换为Json字符串。
2. 在struts2.xml中，package继承`json-default`，Action的返回结果集内设置成`result type="json"`。

```xml
<package name="myJson" extends="json-default">
    <action name="user_*" class="com.bundery.tax.user.action.UserAction">
    <!--因为返回的是json数据，并不是页面，所以result内不能配置返回页面-->
        <result name="success" type="json">
            <!--把userAction对象返回-->
            <param name="root">action</param>
        </result>
    </action> 
</package>
```

此插件默认解析ValueStack栈顶对象。

1. 如果UserAction没有实现ModelDriven接口，默认会把UserAction的所有getter方法返回的属性解析为字符串。
    - 如果不想让某个getter返回，就在getter方法上添加`@json(serialize=false)`。
    - 或者在配置文件中，设置包含属性。`<param name="includeProperties">products\[\d+\]\.name</param>`(设置只包含name属性)
2. 如果UserActon实现了ModelDriven接口，默认会把model对象转换为json返回，可以通过`<param name="root">action</param>`来设置让UserAction对象转换为json格式返回。
