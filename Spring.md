# IOC（Inverse of Control）
**IOC作用**：解耦。把依赖关系交给Ioc容器，避免在代码组件中直接管理/生成对象。

**概念**：Ioc容器表示BeanFactory、ApplicationContext。bean表示IoC容器管理的对象。

**BeanFactroy**：IoC容器，负责bean的管理，创建、获取等。IoC容器所设定最基本的功能规范。包含getBean()，创建/销毁bean等bean的基本操作。如XMLBeanFactory，负责管理通过XML创建的Bean，获取bean等。

**BeanDefinition**：bean的定义信息，有具体的属性、数据，包含初始化函数、是否延迟加载等属性，像图纸一样。BeanDefinitionReader决定如何实现容器的IoC内容（bean的内容），bean的实际如何创建。IoC功能都是围绕对这个BeanDefinition的处理来完成。被。如XmlBeanDefinitionReader，说明具体如何从XML中读取，创建bean。

**BeanDefinitionReader**：负责解析Resource信息，Resource不由BeanFactory直接使用

**ApplicationContext**：面向架构使用的整个框架运行的上下文，最常用的IoC容器。包含各种BeanFactory、EnvironmentCapable、ResourcePatternResolver（资源访问）、ApplicationEventPublisher（事件）等。在BeanFactory简单的IoC容器的基础上添加许多高级容器的特性支持。不同的Context负责不同的资源。

**ResourceLoader**：负责加载具体Resource资源

**DefaultListableBeanFactory**：一个默认功能完整的IoC容器。其他功能完整的IoC容器都是基于这个实现的，如XMLBeanFactory（参考细节）。

**FactoryBean**：封装了一些创建过程复杂的Bean，一般是系统的工具Bean，例如AmqpProxyFactoryBean、ForkJoinPoolFactoryBean。其中getObject()方法返回该过程创建的实例。

---------

**IoC容器初始化过程**（BeanDefinition的初始化）：

0. AbstractApplicationContext中的refresh()触发整个IoC容器的初始化。触发的是IoC容器是DefaultListableBeanFactory。

1. 然后用该IoC容器加载BeanDefinitions，通过loadBeanDefinitions()。

2. 在loadBeanDefinitions()中，首先通过ResourceLoader加载好Resource资源，然后通过初始化一个BeanDefinitionReader去解析Resource。

3. 在BeanDefinitionReader解析Resource的流数据解析成w3c的Document，然后用这些内容注册到DefaultListableBeanFactory中的beanDefinitionMap（ConcurrentHashMap）。此时具体Bean实例还没初始化，仅仅把BeanDefinition初始化注册好。

   

**IoC过程**（Bean依赖注入过程）：

1. 在对IoC容器getBean的时候触发依赖注入，把bean实例创建出来（实例化），也可以通过设置lazy-init让IoC容器预实例化。
2. 主要getBean()逻辑在AbstractBeanFactory中的doGetBean()中，其中会去找缓存中是否已存在实例化bean，有则从中取出，否则则创建。如果bean是FactoryBean，则由该FactoryBean返回实例。如果想取FactoryBean本身，则需要加&。
3. 创建bean会用到反射相关的技术。如果再创建过程中，遇到其他bean会递归创建bean

-------------------------

**bean的scope作用域**：

1. singleton。只会创建一次，每次返回同一个实例
2. prototype。IOC容器可以创建多个Bean实例，每次返回的都是一个新的实例。常用于AOP代理
3. request。每次HTTP请求都会创建一个新的Bean，适用于WebApplicationContext环境。
4. session。同一个Session共享一个Bean实例。不同Session使用不同的实例。
5. global-session。该属性仅用于HTTP Session，同session作用域不同的是，所有的Session共享一个Bean实例。

**父BeanFactory作用**：每个BeanFactory有自己的BeanDefinition范围，如果自己找不到，就往父级BeanFactory找。

**lazy-init属性**：bean的属性，如果为false，则在容器初始化的时候创建。否则默认在getBean时初始化。

**aware接口**：使bean可以获取IoC容器的信息。例如Bean实现ApplicationContextAware接口，可以让bean在实例化后获取ApplicationContext属性。具体实现在ApplicationContextAwareProcessor（实现BeanPostProcessor），在bean后置处理实例化前set好内容。

**循环依赖问题**：如果是构造函数循环依赖解决不了，可以改成field属性setter注入。原因是，当Spring实例化了StudentA、StudentB、StudentC后，紧接着会去设置对象的属性，此时StudentA依赖StudentB，就会去Map中取出存在里面的单例StudentB对象，以此类推，不会出来循环的问题喽。



# AOP（Aspect-OrientedProgramming）

概念：

- **Advice（通知）**：定义在连接点做什么。

- **Pointcut（切点）**：定义Advice（通知）作用于哪个连接点。

- **Advisor（通知器）**：定义在哪个pointcut实现advice，相当于advice+pointcut的执行器。编写代码基本单位。

https://blog.csdn.net/fighterandknight/article/details/51209822

## 创建AOP代理：

1. Spring启动时准备，ProxyFactoryBean初始化好拦截器。
2. 每个bean的实例化之后，获取bean前，都会先经过AbstractAutoProxyCreator类的postProcessAfterInitialization（）这个方法，然后接下来是调用wrapIfNecessary方法。
3. wrapIfNecessary中getAdvicesAndAdvisorsForBean，会提取当前bean 的所有增强方法，然后获取到适合的当前bean 的增强方法，然后对增强方法进行排序，最后返回。
4. 然后根据bean的情况，在proxyFactory中利用Proxy或者CGLIG正常代理对象（有接口则用Proxy）。



# MVC

# Spring用到的设计模式