# Struts2执行原理

> struts的核心原理就是通过拦截器来处理客户端的请求，经过拦截器一系列的处理后，再交给Action的指定方法处理。

struts官方的工作原理图：

![Struts2原理](http://wx1.sinaimg.cn/mw690/9e6aadb3gy1ffn657ssfkj20dm0czgmi.jpg)

首先客户端发来HttpServletRequest请求，传递给FilerDispatcher（ActionMapper是访问静态资源（struts的jar文件等）时用的，平时很少用），然后FilerDispatcher创建一个ActionProxy对象，ActionProxy对象通过ConfigurationManager对象获得struts.xml文件中的配置，ActionProxy拥有一个ActionInvocation对象的实例，通过调用ActionInvocation的invoke()方法，来按顺序交由Interceptor处理，最后交由Action，接着返回Result，再逆序经过Interceptor，最后得到HttpServletResponse返回给客户端。

![Struts2原理时序图](http://wx1.sinaimg.cn/mw690/9e6aadb3gy1ffn69ozarwj20u50fqq3t.jpg)

1. 模拟ActionInvocation：

```java
    package com.tgb.struts;  
    import java.util.ArrayList;  
    import java.util.List;  
    // 这个自定义的ActionInvocation是将Interceptor写在里面的，真实的ActionInvocation是通过反射加载的
    public class ActionInvocation {  
        List<Intercepto> interceptors = new ArrayList<Interceptor>();  
        int index = -1;  
        Action a = new Action();  

        public ActionInvocation() {  
            this.interceptors.add(new FirstInterceptor());  
            this.interceptors.add(new SecondInterceptor());       
        }  

        public void invoke() {  
            index ++;  
            if(index >= this.interceptors.size()) {  
                a.execute();  
            }else {  
                this.interceptors.get(index).intercept(this);  
            }  
        }  
    } 
```

2. 模拟 Interceptor接口以及两个简单实现类：

```java
    package com.tgb.struts;  

    public interface Interceptor {  
        public void intercept(ActionInvocation invocation) ;  
    }  

    package com.tgb.struts;  

    public class FirstInterceptor implements Interceptor {  

        public void intercept(ActionInvocation invocation) {  
            System.out.println("FirstInterceptor Begin...");  
            invocation.invoke();  
            System.out.println("FirstInterceptor End...");  
        }  

    }  

    package com.tgb.struts;  

    public class SecondInterceptor implements Interceptor {  

        public void intercept(ActionInvocation invocation) {  
            System.out.println("SecondInterceptor Begin...");  
            invocation.invoke();  
            System.out.println("SecondInterceptor End...");  
        }  

    } 
```

3. Action：

```java
    package com.tgb.struts;  

    public class Action {  
        public void execute() {  
            System.out.println("Action Run...");  
        }  
    }
```

4. 测试：

```java
   package com.tgb.struts;  

    public class Client {  
        public static void main(String[] args) {  
            new ActionInvocation().invoke();  
        }  
    } 
```

 执行结果：

```java
    FirstInterceptor Begin...  
    SecondInterceptor Begin...  
    Action Run...  
    SecondInterceptor End...  
    FirstInterceptor End...  
```

通过上面的执行结果，可以看出客户端请求来的时候会按照顺序被所有配置的拦截器拦截一遍，然后返回的时候会按照逆序再被拦截器拦截一遍，这跟栈结构比较相似（先进先出）。