---
categories:
  - spring源码解析
---
#### 新建一个gradle模块

在构建的spring framework项目中新建一个gradle模块。

```groovy
plugins {
    id 'java'
}

group 'org.springframework'
version '5.1.11.BUILD-SNAPSHOT'

sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
   
  //  compile group: 'org.apache.tomcat.embed', name: 'tomcat-embed-core', version: '9.0.27' 
    compile group: 'org.apache.tomcat.embed', name: 'tomcat-embed-core', version: '8.5.47'
    compile project(":spring-webmvc")
    compile project(":spring-context")
    testCompile group: 'junit', name: 'junit', version: '4.12'
}

```

#### 实现WebApplicationInitializer

servlet 3.1规范:

`ServletContainerInitializer`类通过`jar services API`查找。对于每一个应用，应用启动时，由容器创建一个`ServletContainerInitializer`实例。框架提供的`ServletContainerInitializer`实现必须绑定在jar包的META-INF/services目录中的一个叫做`javax.servlet.ServletContainerInitializer`的文件(在spring framework中的spring web模块中可以找到改文件)，根据jar services API，指定ServletContainerInitializer的实现。

**在任何Servlet Listener的事件被触发之前，当应用正在启动时，ServletContainerInitializer的onStartup方法将被调用。**

**ServletContainerInitializer’s的onStartup得到一个类的set，其或者继承/实现initializer表示感兴趣的类，或者它是使用指定在@HandlesTypes注解中的任意类注解的。**（`@HandlesTypes`注解的处理，被它标明的类需要作为参数值传入到onStartup方法）

```java
oid onStartup(Set<Class<?>> c, ServletContext ctx) throws ServletException;
```

```java
@HandlesTypes(WebApplicationInitializer.class)
public class SpringServletContainerInitializer implements ServletContainerInitializer {

	/**
	 * Delegate the {@code ServletContext} to any {@link WebApplicationInitializer}
	 * implementations present on the application classpath.
	 * <p>Because this class declares @{@code HandlesTypes(WebApplicationInitializer.class)},
	 * Servlet 3.0+ containers will automatically scan the classpath for implementations
	 * of Spring's {@code WebApplicationInitializer} interface and provide the set of all
	 * such types to the {@code webAppInitializerClasses} parameter of this method.
	 * <p>If no {@code WebApplicationInitializer} implementations are found on the classpath,
	 * this method is effectively a no-op. An INFO-level log message will be issued notifying
	 * the user that the {@code ServletContainerInitializer} has indeed been invoked but that
	 * no {@code WebApplicationInitializer} implementations were found.
	 * <p>Assuming that one or more {@code WebApplicationInitializer} types are detected,
	 * they will be instantiated (and <em>sorted</em> if the @{@link
	 * org.springframework.core.annotation.Order @Order} annotation is present or
	 * the {@link org.springframework.core.Ordered Ordered} interface has been
	 * implemented). Then the {@link WebApplicationInitializer#onStartup(ServletContext)}
	 * method will be invoked on each instance, delegating the {@code ServletContext} such
	 * that each instance may register and configure servlets such as Spring's
	 * {@code DispatcherServlet}, listeners such as Spring's {@code ContextLoaderListener},
	 * or any other Servlet API componentry such as filters.
	 * @param webAppInitializerClasses all implementations of
	 * {@link WebApplicationInitializer} found on the application classpath
	 * @param servletContext the servlet context to be initialized
	 * @see WebApplicationInitializer#onStartup(ServletContext)
	 * @see AnnotationAwareOrderComparator
	 */
	@Override
	public void onStartup(@Nullable Set<Class<?>> webAppInitializerClasses, ServletContext servletContext)
			throws ServletException {

		List<WebApplicationInitializer> initializers = new LinkedList<>();

		if (webAppInitializerClasses != null) {
			for (Class<?> waiClass : webAppInitializerClasses) {
				// Be defensive: Some servlet containers provide us with invalid classes,
				// no matter what @HandlesTypes says...
				if (!waiClass.isInterface() && !Modifier.isAbstract(waiClass.getModifiers()) &&
						WebApplicationInitializer.class.isAssignableFrom(waiClass)) {
					try {
						initializers.add((WebApplicationInitializer)
								ReflectionUtils.accessibleConstructor(waiClass).newInstance());
					}
					catch (Throwable ex) {
						throw new ServletException("Failed to instantiate WebApplicationInitializer class", ex);
					}
				}
			}
		}

		if (initializers.isEmpty()) {
			servletContext.log("No Spring WebApplicationInitializer types detected on classpath");
			return;
		}

		servletContext.log(initializers.size() + " Spring WebApplicationInitializers detected on classpath");
		AnnotationAwareOrderComparator.sort(initializers);
		for (WebApplicationInitializer initializer : initializers) {
            // 这里执行满足@HandlesTypes(WebApplicationInitializer.class)传过来的类
			initializer.onStartup(servletContext);
		}
	}

}
```

实现WebApplicationInitializer由上可知会被调用

```java
// 不会自己调用，给web服务器调用
public class MyWebApplicationInitializer implements WebApplicationInitializer {


	/**
	 * Configure the given {@link ServletContext} with any servlets, filters, listeners
	 * context-params and attributes necessary for initializing this web application. See
	 * examples {@linkplain WebApplicationInitializer above}.
	 *
	 * @param servletContext the {@code ServletContext} to initialize
	 * @throws ServletException if any call against the given {@code ServletContext}
	 *                          throws a {@code ServletException}
	 */
	@Override
	public void onStartup(ServletContext servletContext) throws ServletException {
		System.out.println("xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx");
	}
}
```

#### 容器运行

```java
public class TomcatContainer {
	public static void run () throws ServletException, LifecycleException{
		Tomcat tomcat = new Tomcat();
		tomcat.setPort(9090);
		/**
		 *  * @param contextPath The context mapping to use, "" for root context.
		 *  * @param docBase Base directory for the context, for static files.
		 *  *  Must exist, relative to the server home
		 *  * @return the deployed context
		 *   contextPath 是tomcat的访问路径
		 *	tomcat.addWebapp说明是web项目，将查找使用的services API（绑定在jar包中的META-INF/services目录中的一个叫做javax.servlet.ServletContainerInitializer的文件，最终会调用到 WebApplicationInitializer实现类。
		 */
		tomcat.addWebapp("/",new File(System.getProperty("java.io.tmpdir")).getAbsolutePath());
		Context rootContext = tomcat.addContext("/", "");
		tomcat.start();
		tomcat.getServer().await();

	}
}
```

> 如果使用addWebapp()执行完onStartup()后会报错。（addwebapp表示普通web项目,tomcat会找web.xml和解析jps,spring boot又不支持）。没有web.xml找不到servlet.
>
> 严重: Servlet [jsp] in web application [] threw load() exception
> java.lang.ClassNotFoundException: org.apache.jasper.servlet.JspServlet
>
> 使用addContext就不会查找WebApplicationInitializer，也不会报错。spring boot的中tomcat的依赖没依赖jsp