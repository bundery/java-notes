# Spring Aop

## AOP简介
AOP(Aspect Oriented Programming)是对OOP的补充，可以通过预编译的方式和运行期动态代理实现在不修改源代码的情况下给程序动态添加功能的一种技术，实现了调用者和被调用者之间的解耦。一些如日志、事务往往横切于业务代码中，但这些代码往往是重复的，AOP就实现了把这些业务需求和系统需求分开来做。

### Aspect
Aspect(切面)：一个抽象的概念，表示整个切面逻辑，由Advice和Pointcut组成。

### Pointcut
是一个基于正则的Aspect表达式，是程序执行点(JoinPoint)的集合。
execution是最常用的切点函数，切入点是方法，`execution(<修饰符模式>? <返回类型模式> <方法名模式>(<参数模式>) <异常模式>?)`，  除了返回类型模式、方法名模式和参数模式外，其它项都是可选的。`*`：表示任意类型。`..`：用在包上，表示当前包及其子包；用在参数上，表示任意参数。`()`匹配无参方法，`(..)` 匹配任意方法，`(*)`匹配一个参数的方法。

1. `execution(* io.bundery..*.*(..))`：匹配io.bundery包及其子包下任意类的任意方法。
2. `execution(public * io.bundery..*.*(..))`：匹配io.bundery包及其子包下任意类的任意public方法。
3. `execution(* io.bundery..UserService.*(*))`：匹配io.bundery包及其子包下UserService接口的任意只有一个参数的方法。
4. `execution(* io.bundery..UserService+.*())`：匹配io.bundery包及其子包下UserService接口的及其子类的任意无参方法。
 
### JoinPoint
某个切点，Pointcut集合中具体的某个执行点。

### Advice
在某个Pointcut要执行的操作，有5种类型，每种类型都可以传入JoinPoint参数来获取target对象的相关信息，但是只有`@Around`才能传入`ProceedingJoinPoint`参数。

1. `@Around`：环绕通知，类似Filter的doFilter()和Interceptor的intercept()方法。第一个参数ProceedingJoinPoint(JoinPoint的子类，增加了proceed()等方法)。
2. `@Before`：target方法执行前要执行的操作。
3. `@AfterReturn`：target方法执行前的操作，不包括抛出异常的情况。
4. `@AfterThrowing`：target方法抛出异常时执行的操作。
5. `@After`：target方法退出的时候执行的操作（无论正常还是抛出异常的方法）。

### Target
被AspectJ横切的对象。

```java
@Aspect
@ioponent
public class ExceptionAop implements Ordered{

	@Pointcut("execution(* io.bundery..*.*(..))")
	public void aspectJMethod() {};

	@Before("aspectJMethod()")
	public void printStartLog(JoinPoint joinPoint) {
		System.out.println(joinPoint.toLongString()); //通过JoinPoint对象获取target对象要执行的方法。
		System.out.println("--ExceptionAop..printStartLog.."+System.currentTimeMillis());
	}
	
	@AfterReturning("aspectJMethod()")
	public void printEndLog() {
		System.out.println("--ExceptionAop..printEndLog.."+System.currentTimeMillis());
	}
	
	@Around("aspectJMethod()")
	public void printLog(ProceedingJoinPoint joinPoint) throws Throwable {
		System.out.println("LogAop..printArroundStartLog.."+System.currentTimeMillis());
		joinPoint.proceed(); //执行target方法。
		System.out.println("LogAop..printArroundEndLog.."+System.currentTimeMillis());
	}

	@Override
	public int getOrder() {
		return 4; //多个aop对同一个target执行的顺序，order的值越小，说明越早执行
	}
}
```