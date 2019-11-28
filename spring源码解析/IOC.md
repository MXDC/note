#### IOC

The `org.springframework.context.ApplicationContext` interface represents the Spring IoC container and is responsible for instantiating, configuring, and assembling the beans. 

```java
1.   AnnotationConfigApplicationContext
2.   new ClassPathXmlApplicationContext("services.xml", "daos.xml");
3.   FileSystemXmlApplicationContext
```

Within the container itself, these bean definitions are represented as `BeanDefinition` objects,



#### 组件注册

***

##### 包扫描+组件注解(@Service/@Controller/@Component/@Repository)

##### @Bean

#####  @Conditional按条件注册Bean

##### @Import

* @Import
* ImportSelector
* ImportBeanDefinitionRegistrar

FactoryBean

####  Bean生命周期

loadBeanDefinition--new bean-- lifecyclecallback- 主动注入 -初始化--- ---销毁

1. 加载配置，扫描配置文件

2. 加载bean的元素数据包装成BeanDefinition

3. 执行BeanFactoryPostProcessor

  finishBeanFactoryInitialization(),下面是实例化bean


4. 实例化（Instantiation）

   ---第一次应用后置处理器。InstantiationAwareBeanPostProcessor$postProcessBeforeInstantiation

   ---第二次应用后置处理器。InstantiationAwareBeanPostProcessor$postProcessPropertyValues

   ---利用依赖注入设置属性

   --- BeanNameAware（作用：**让Bean获取自己在BeanFactory配置中的名字（根据情况是id或者name）。**）

   ---BeanClassLoaderAware
   
   --- BeanFactoryAware（作用：**让Bean获取配置他们的BeanFactory的引用。**）
   
   ----applyBeanPostProcessorsBeforeInitialization
   
   ---
   
   ---6.初始化(**Lifecyle Callbacks**)（（@PostConstruct先）先调用afterPropertiesSet，在调用自定义初始化方法）
   
   ​	|---- Initialization Callbacks
   
   ​	|---- Destruction Callbacks
   
   ---applyBeanPostProcessorsAfterInitialization

***

1. InstantiationAwareBeanPostProcessor接口继承BeanPostProcessor接口，它内部提供了3个方法，再加上BeanPostProcessor接口内部的2个方法，所以实现这个接口需要实现5个方法。InstantiationAwareBeanPostProcessor接口的主要作用在于目标对象的实例化过程中需要处理的事情，包括实例化对象的前后过程以及实例的属性设置
2. postProcessBeforeInstantiation方法是最先执行的方法，它在目标对象实例化之前调用，该方法的返回值类型是Object，我们可以返回任何类型的值。由于这个时候目标对象还未实例化，所以这个返回值可以用来代替原本该生成的目标对象的实例(比如代理对象)。如果该方法的返回值代替原本该生成的目标对象，后续只有postProcessAfterInitialization方法会调用，其它方法不再调用；否则按照正常的流程走
3. postProcessAfterInstantiation方法在目标对象实例化之后调用，这个时候对象已经被实例化，但是该实例的属性还未被设置，都是null。如果该方法返回false，会忽略属性值的设置；如果返回true，会按照正常流程设置属性值
4. postProcessPropertyValues方法对属性值进行修改(这个时候属性值还未被设置，但是我们可以修改原本该设置进去的属性值)。如果postProcessAfterInstantiation方法返回false，该方法不会被调用。可以在该方法内对属性值进行修改

***



##### Lifecycle Callbacks

* **Initialization Callbacks**是在执行构造方法之后，**Destruction Callbacks**是销毁之前。有三种方式实现

1)指定初始化方法和销毁方法

​	指定init-mtthod和destory-method

​	@Bean(initMethod="init",destroyMethod="destroy")	

2）让Bean实现InitializingBean,DisposbleBean

2）使用JSR250（用在方法上面）

​	@PostConstruct,在bean创建完成之前并且属性赋值完成，来执行初始化方法

​	@PreDestory，在容器销毁bean之前通知我们进行清理

* ##### Startup and Shutdown Callbacks

  

***



BeanFactoryPostProcessor和BeanPostProcessor，这两个接口，都是Spring初始化bean时对外暴露的扩展点。两个接口名称看起来很相似，但作用及使用场景却不同

#### BeanFactoryPostProcessor

是一个接口，加载类后(类信息被抽象在BeanDefinition)还没有实例化。可以在spring的bean创建之前，修改bean的定义属性。



#### bean的实例化

```java
// Trigger initialization of all non-lazy singleton beans...
	for (String beanName : beanNames) {
	    .....
	}
```

1. 执行finishBeanFactoryInitialization(beanFactory);

2. beanFactory.preInstantiateSingletons(),preInstantiateSingletons()是ConfigurableListableBeanFactory一个接口方法。DefaultListableBeanFactory是实现类。

3. 执行perInstantiateSingletons()时先判断`if (!bd.isAbstract() && bd.isSingleton() && !bd.isLazyInit()) `单列、懒加载。如果是则在判断`if (isFactoryBean(beanName)){}else{getBean(beanName); }。`factoryBean是一个特殊bean。如果不factoryBean是普通bean则调用`getBen(beanName)`。

> 为啥是getBean而不是newBean,createBean?是为了判断是否已经存在bean了，避免重复实例化。

4. getBean会调用doGetBean,doGetBean()方法里面先执行transformedBeanName()规范beanName。然后第一次调用getSingleton(beanName),`private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);`就是从`singletonObjects`中获取bean,它就是个ConcurrentHashMap<>(256)如果没被实例化就会返回null;isSingletonCurrentlyInCreation`,

  
   > isSingletonCurrentlyInCreation`,// 循环依赖问题A-->B -->A，new 出对象后还没最终在容器中生成bean，还要走完生命周期。`
   
5.  (1)Check if bean definition exists in this factory. (2) Guarantee initialization of beans that the current bean depends on.(3) Create bean instance.

   ```mermaid
   graph TB
   CREATE_BEAN_INSTANCE-->Decision{mbd.isSingleton}
   Decision -->|true| getSingleton
   Decision -->|else if mbd.isPrototype| D[Result one]
   Decision-->|else| E[Result two]
   
   ```



​		第二次调用getSingleton

```java
sharedInstance = getSingleton(beanName, () -> {
						try {
							return createBean(beanName, mbd, args);//创建bean
						}
						catch (BeansException ex) {
							// Explicitly remove instance from singleton cache: It might have been put there
							// eagerly by the creation process, to allow for circular reference resolution.
							// Also remove any beans that received a temporary reference to the bean.
							destroySingleton(beanName);
							throw ex;
						}
					});
```



#### BeanPostProcessor

spring 实例化 包括初始化

初始化 new bean()之后的过程

new ----XXXXBeanPostProcessor ---A ----@PostConstruct -----BeanPostProcessor

> !!!! Instantiation的过程是包括Initialization的。BeanPostProcessor接口只能管理Initialization的阶段。但还有很多接口是继承了BeanPostProcessor接口的。像InstantiationAwareBeanPostProcessor extends BeanPostProcessor。

***

> 9个地方，5个后置处理器
>
> 1.Object bean = resolveBeforeInstantiation(beanName, mbdToUse);

BeanPostProcessor，可以在spring容器new了bean之后，在执行bean的初始化方法前后，添加一些自己的处理逻辑。如果配置了多个`BeanPostProcessor`，那么可以通过设置`'order'`属性来控制`BeanPostProcessor`的执行次序（仅当`BeanPostProcessor`实现了`Ordered`接口时，你才可以设置此属性，因此在编写自己的`BeanPostProcessor`实现时，就得考虑是否需要实现`Ordered`接口）。此时对应的bean已经实例化了，但是对应的属性注入等还没有进行，即在调用InitializingBean的afterPropertiesSet()方法或bean对应的init-method之前；而方法postProcessAfterInitialization()将在bean被完全初始化后进行回调，此时对应的依赖注入已经完成，即在调用InitializingBean的afterPropertiesSet()方法或对应init-method方法之后。两个方法的参数以及返回值对应的意义都是一样的，其中参数bean表示当前状态的bean，参数beanName表示当前bean的名称，而方法对应的返回值即表示需要放入到bean容器中的bean，所以用户如果有需要完全可以在这两个方法中对bean进行修改，即封装自己的bean进行返回。

> 实现了BeanPostProcessor的bean，不会被postProcessBeforeInitialization,和postProcessAfterInitialization当做参数调用。



```java
public interface BeanPostProcessor {
    //bean初始化方法调用前被调用,返回null不会使bean为空，返回其他对象可以代替原有对象
   default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException;
    //bean初始化方法调用后被调用
   default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException;

}
```

这里说的初始化方法，指的是下面两种：
1）bean实现了InitializingBean接口，对应的方法为afterPropertiesSet

2）在bean定义的时候，通过init-method设置的方法

```mermaid
graph TB

A[A:AnnotationConfigApplicationContext]
B((A.refresh))
1.4[postProcessBeanFactory]
1.5[invokeBeanFactoryPostProcessors]
1.6[registerBeanPostProcessors]
A --> B
subgraph 1
	B --> 1.4
	1.4 --> 1.5
	1.5 --> 1.6
	subgraph 1.6
		1.6 --> 1.6.1
	end
end
A.finishRefresh((finishRefresh))-->beanFactory.preInstantiateSingletons
beanFactory.preInstantiateSingletons -->preInstantiateSingletons
preInstantiateSingletons --> |Loop|getBean
subgraph Trigger initialization of all non-lazy singleton beans
getBean --> getSingleton
getSingleton -->singletonFactory.getObject
singletonFactory.getObject-->doGetBean
doGetBean -->createBean
createBean -->doCreateBean
doCreateBean --> initializeBean
initializeBean-->applyBeanPostProcessorsBeforeInitialization
applyBeanPostProcessorsBeforeInitialization -->processor.postProcessBeforeInitialization
end
 
```

> 有趣的添加了@configuration的bean,是被spring的cllib增强的代理类。com.mxdc.Config$$EnhancerBySpringCGLIB$$aff7e6e7



