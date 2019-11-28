#### AOP——面向切面编程

传统开发基于继承，至上而下的。会产生一些跟业务逻辑无关的横切性问题、日记记录、错误处理等。 抽取汇总成切面。不用关系主业务，可以只关心切面的编程。什么时候切入、切入到哪里、之前还是之后。更加关注执行的时间和顺序。

* join point 在代码中就是method
* PointCut 某些join Point的集合
* target Object还没有被增强的目标对象
* **Weaving**织入，增强的过程
* Advice:增强的逻辑

>@AspectJ指的是将方面声明为用注释注释的常规Java类的样式。@AspectJ样式是由AspectJ项目作为AspectJ 5发行版的一部分引入的。Spring使用AspectJ提供的用于切入点解析和匹配的库来解释与AspectJ 5相同的注释。但是AOP运行时仍然是纯Spring AOP，并且不依赖于AspectJ编译器或编织器。

#### AOP的实现框架

Aop是一种面向切面编程的概念。springAop、AspectJ都是Aop的实现，SpringAop有自己的语法，但是语法复杂，所以SpringAop借助了AspectJ的注解，但是底层实现还是自己的（我们可以知道**spring**底层使用的是JDK或者CGLIB来完成的代理,并且在官网上**spring**给出了aspectj的文档,和springAOP是不同的）

#### spring 支持@Aspect风格

1. EnableAspectJAutoProxy

```java
@Configuration
@EnableAspectJAutoProxy
// @EnableAspectJAutoProxy(proxyTargetClass = true)使用cglib
public class AppConfig {
}
```

```xml
<aop:aspectj-autoproxy/>
```

2. 使用注解@Apsect 声明一个切面
3. 使用@Pointcut声明切点
4. 声声明adive

#### @Pointcut

1. excution

```java
example:
@Pointcut("execution(* com.chenss.dao.*.*(..))")//匹配com.chenss.dao包下的任意接口和类的任意方法
@Pointcut("execution(public * com.chenss.dao.*.*(..))")//匹配com.chenss.dao包下的任意接口和类的public方法
@Pointcut("execution(public * com.chenss.dao.*.*())")//匹配com.chenss.dao包下的任意接口和类的public 无方法参数的方法
@Pointcut("execution(* com.chenss.dao.*.*(java.lang.String, ..))")//匹配com.chenss.dao包下的任意接口和类的第一个参数为String类型的方法
@Pointcut("execution(* com.chenss.dao.*.*(java.lang.String))")//匹配com.chenss.dao包下的任意接口和类的只有一个参数，且参数为String类型的方法
@Pointcut("execution(* com.chenss.dao.*.*(java.lang.String))")//匹配com.chenss.dao包下的任意接口和类的只有一个参数，且参数为String类型的方法
@Pointcut("execution(public * *(..))")//匹配任意的public方法
@Pointcut("execution(* te*(..))")//匹配任意的以te开头的方法
@Pointcut("execution(* com.chenss.dao.IndexDao.*(..))")//匹配com.chenss.dao.IndexDao接口中任意的方法
@Pointcut("execution(* com.chenss.dao..*.*(..))")//匹配com.chenss.dao包及其子包中任意的方法

```

2. with

   ```java
   // within与execution相比，粒度更大，仅能实现到包和接口、类级别。而execution可以精确到方法的返回值，参数个数、修饰符、参数类型等
   @Pointcut("within(com.chenss.dao.*)")//匹配com.chenss.dao包中的任意方法
   @Pointcut("within(com.chenss.dao..*)")//匹配com.chenss.dao包及其子包中的任意方法
   ```

3.  args

   args表达式的作用是匹配指定参数类型和指定参数数量的方法,与包名和类名无关

4. @annotation,匹配使用了PoinCutAnnotation的方法,加@表示有没有加这个注解

   ```java
   @Around("@annotation(com.mxdc.aspect.PointCutAnnotation)")
   ```

5. this和target

   ```java
   @Pointcut("target(com.chenss.dao.IndexDaoImpl)")//目标对象，也就是被代理的对象。限制目标对象为com.chenss.dao.IndexDaoImpl类
   @Pointcut("this(com.chenss.dao.IndexDaoImpl)")//当前对象，也就是代理对象，代理对象时通过代理目标对象的方式获取新的对象，与原值并非一个
   ```

   

#### **advice**通知类型:

* **Before** 连接点执行之前，但是无法阻止连接点的正常执行，除非该段执行抛出异常

*  **AfterReturning**  连接点正常执行之后，执行过程中正常执行返回退出，非异常退出

* **After** **throwing**  执行抛出异常的时候

* **After** (**finally**)  无论连接点是正常退出还是异常退出，都会执行

* **Around** **advice**: 围绕连接点执行，例如方法调用。这是最有用的切面方式。**around**通知可以在方法调用之前和之后执行自定义行为。它还负责选择是继续加入点还是通过返回自己的返回值或抛出异常来快速建议的方法执行



#### **Proceedingjoinpoint** 和**JoinPoint**的区别:

**Proceedingjoinpoint** 继承了**JoinPoint**,**proceed**()这个是**aop**代理链执行的方法。并扩充实现了**proceed**()方法，用于继续执行连接点。**JoinPoint**仅能获取相关参数，无法执行连接点。

**JoinPoint**的方法

1.java.lang.Object[] **getArgs**()：获取连接点方法运行时的入参列表； 

2.Signature **getSignature**() ：获取连接点的方法签名对象； 

3.java.lang.Object **getTarget**() ：获取连接点所在的目标对象； 

4.java.lang.Object **getThis**() ：获取代理对象本身；

**proceed**()有重载,有个带参数的方法,可以修改目标方法的的参数的值

