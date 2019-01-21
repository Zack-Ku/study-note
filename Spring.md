# IOC（Inverse of Control）
作用：解耦。把依赖关系交给Ioc容器，避免在代码组件中直接管理/生成对象。

概念：Ioc容器表示BeanFactory、ApplicationContext。bean表示IoC容器管理的对象。

BeanFactroy：IoC容器，负责bean的管理，创建、获取等。IoC容器所设定最基本的功能规范。包含getBean()，创建/销毁bean等bean的基本操作。如XMLBeanFactory，负责管理通过XML创建的Bean，获取bean等。

BeanDefinition：bean的实例载体，有具体的属性、数据，包含初始化函数、是否延迟加载等属性，像图纸一样。BeanDefinitionReader决定如何实现容器的IoC内容（bean的内容），bean的实际如何创建。IoC功能都是围绕对这个BeanDefinition的处理来完成。被。如XmlBeanDefinitionReader，说明具体如何从XML中读取，创建bean。

BeanDefinitionReader：负责解析Resource信息，Resource不由BeanFactory直接使用

ApplicationContext：面向架构使用的整个框架运行的上下文，最常用的IoC容器。包含各种BeanFactory、EnvironmentCapable、ResourcePatternResolver（资源访问）、ApplicationEventPublisher（事件）等。在BeanFactory简单的IoC容器的基础上添加许多高级容器的特性支持。不同的Context负责不同的资源。

ResourceLoader：负责加载具体Resource资源

DefaultListableBeanFactory：一个默认功能完整的IoC容器。其他功能完整的IoC容器都是基于这个实现的，如XMLBeanFactory（参考细节）。

IoC容器初始化过程（BeanDefinition的初始化）：
0. AbstractApplicationContext中的refresh()触发整个IoC容器的初始化。触发的是IoC容器是DefaultListableBeanFactory。
1. 然后用该IoC容器加载BeanDefinitions，通过loadBeanDefinitions()。
2. 在loadBeanDefinitions()中，首先通过ResourceLoader加载好Resource资源，然后通过初始化一个BeanDefinitionReader去解析Resource。
3. 在BeanDefinitionReader解析Resource的流数据解析成w3c的Document，然后用这些内容注册到DefaultListableBeanFactory中的beanDefinitionMap（ConcurrentHashMap）。此时具体Bean实例还没初始化，仅仅把BeanDefinition初始化注册好。

IoC过程（Bean依赖注入过程）：
1. 在对IoC容器getBean的时候触发依赖注入，把bean实例创建出来（实例化），也可以通过设置lazy-init让IoC容器预实例化。
2. 主要getBean()逻辑在AbstractBeanFactory中的doGetBean()中，其中会去找是否已存在实例化bean，有则从中取出，否则则创建。单例的bean会在DefaultSingletonBeanRegistry中管理实例。
3. 创建bean会用到反射相关的技术。如果再创建过程中，遇到其他bean会递归创建bean

bean的scope作用域：
1. singleton。只会创建一次，每次返回同一个实例
2. prototype。IOC容器可以创建多个Bean实例，每次返回的都是一个新的实例。
3. request。每次HTTP请求都会创建一个新的Bean，适用于WebApplicationContext环境。
4. session。同一个Session共享一个Bean实例。不同Session使用不同的实例。
5. global-session。该属性仅用于HTTP Session，同session作用域不同的是，所有的Session共享一个Bean实例。

父BeanFactory作用：每个BeanFactory有自己的BeanDefinition范围，如果自己找不到，就往父级BeanFactory找。

# AOP

# MVC

# Spring用到的设计模式