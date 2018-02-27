# Spring Core APi

[TOC]

## 1. The IoC container

It is a process whereby objects define their dependencies, that is, the other objects they work with, only through constructor arguments, arguments to a factory method, or properties that are set on the object instance after it is constructed or returned from a factory method.

他是一个确定对象的相关依赖的过程，只有通过构造方法入参，工厂方法入参，或者返回后在对象实例上设置的属性来定义它们的依赖关系。



The container then *injects* those dependencies when it creates the bean. This process is fundamentally the inverse, hence the name *Inversion of Control* (IoC)

Bean 会在容器中完成相关依赖的注入，因为整个过程基本是相反的，因此称为控制反转（IOC）



The [`BeanFactory`](https://docs.spring.io/spring-framework/docs/5.0.5.BUILD-SNAPSHOT/javadoc-api/org/springframework/beans/factory/BeanFactory.html) interface provides an advanced configuration mechanism capable of managing any type of object.

**BeanFactory** 提供了高级的配置管理机制，能够管理任何类型的对象



[`ApplicationContext`](https://docs.spring.io/spring-framework/docs/5.0.5.BUILD-SNAPSHOT/javadoc-api/org/springframework/context/ApplicationContext.html) is a sub-interface of `BeanFactory`. It adds easier integration with Spring’s AOP features; message resource handling (for use in internationalization), event publication; and application-layer specific contexts such as the `WebApplicationContext` for use in web applications.

**ApplicationContext** 是BeanFactory 的一个子接口，它在原有基础上添加了Spring Aop 机制，消息资源处理（用于国际化），事件发布以及应用程序层特定的上下文，例如WebApplicationContext 用于Web 应用



In short, the `BeanFactory` provides the configuration framework and basic functionality, and the `ApplicationContext` adds more enterprise-specific functionality. The `ApplicationContext` is a complete superset of the `BeanFactory`, and is used exclusively in this chapter in descriptions of Spring’s IoC container.

简而言之，BeanFactory 提供了配置框架以及基本功能，而ApplicationContext 则添加了更多的企业级功能。ApplicationContext 是BeanFactory 的完整超集，在本章中专门用于描述Spring的IoC容器。



A bean is an object that is instantiated, assembled, and otherwise managed by a Spring IoC container.

bean是一个实例化，组装并由Spring IoC容器管理的对象。



Beans, and the *dependencies* among them, are reflected in the *configuration metadata* used by a container.

**Beans** 与它们之间的依赖关系，会反应在容器使用的配置元数据中。



## 1.2. Container overview

The interface `org.springframework.context.ApplicationContext` represents the Spring IoC container and is responsible for instantiating, configuring, and assembling the aforementioned beans.

ApplicationContext 接口代表着Spring IOC 容器，并且它负责创建，配置以及组装Beans。



While XML has been the traditional format for defining configuration metadata you can instruct the container to use Java annotations or code as the metadata format by providing a small amount of XML configuration to declaratively enable support for these additional metadata formats.

虽然XML是用于定义配置元数据的传统格式，但您可以通过提供少量的XML配置来指示容器使用Java注释或代码作为元数据格式，以声明方式支持这些其他元数据格式。





### 1.2.1. Configuration metadata

XML-based metadata is *not* the only allowed form of configuration metadata. The Spring IoC container itself is *totally* decoupled from the format in which this configuration metadata is actually written. These days many developers choose [Java-based configuration](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-java) for their Spring applications.

基于XML的元数据不是唯一允许的配置元数据形式。Spring IoC容器本身完全与此配置元数据实际写入的格式分离。现在很多开发人员为他们的Spring应用程序选择基于Java的配置。



For information about using other forms of metadata with the Spring container, see:

- [Annotation-based configuration](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-annotation-config): Spring 2.5 introduced support for annotation-based configuration metadata.
- [Java-based configuration](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-java): Starting with Spring 3.0, many features provided by the Spring JavaConfig project became part of the core Spring Framework. Thus you can define beans external to your application classes by using Java rather than XML files. To use these new features, see the `@Configuration`, `@Bean`, `@Import` and `@DependsOn` annotations.

Spring 容器的其他配置元数据方式请参考：

- [Annotation-based configuration](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-annotation-config)(基于注解配置): Spring 2.5 支持通过注解进行配置元数据
- [Java-based configuration](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-java)（基于Java配置）: Spring 3.0 开始, Spring JavaConfig项目提供的许多功能成为核心Spring框架的一部分。因此，您可以使用Java而不是XML文件来定义应用程序类外部的Bean。使用新特性请参考： `@Configuration`, `@Bean`, `@Import` 以及 `@DependsOn` 注解。



The following example shows the basic structure of XML-based configuration metadata:

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <bean id="..." class="...">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions go here -->

</beans>

```



### 1.2.2. Instantiating a container

Instantiating a Spring IoC container is straightforward. The location path or paths supplied to an `ApplicationContext`constructor are actually resource strings that allow the container to load configuration metadata from a variety of external resources such as the local file system, from the Java `CLASSPATH`, and so on.

实例化Spring IoC容器很简单。提供给ApplicationContext构造函数的位置路径实际上是资源字符串，它允许容器从各种外部资源（例如本地文件系统，Java CLASSPATH等等）加载配置元数据。



#### Composing XML-based configuration metadata

You can use the application context constructor to load bean definitions from all these XML fragments. This constructor takes multiple `Resource` locations, as was shown in the previous section. Alternatively, use one or more occurrences of the `<import/>`element to load bean definitions from another file or files. For example:

您可以使用应用程序上下文构造函数从所有这些XML片段中加载bean定义。这个构造函数有多个资源位置，如前一节所示。或者，使用一个或多个<import />元素从另一个或多个文件加载bean定义。例如：

```
<beans>
    <import resource="services.xml"/>
    <import resource="resources/messageSource.xml"/>
    <import resource="/resources/themeSource.xml"/>

    <bean id="bean1" class="..."/>
    <bean id="bean2" class="..."/>
</beans>
```





## 1.3. Bean overview

Within the container itself, these bean definitions are represented as `BeanDefinition` objects, which contain (among other information) the following metadata:

- *A package-qualified class name:* typically the actual implementation class of the bean being defined.
- Bean behavioral configuration elements, which state how the bean should behave in the container (scope, lifecycle callbacks, and so forth).
- References to other beans that are needed for the bean to do its work; these references are also called *collaborators* or *dependencies*.
- Other configuration settings to set in the newly created object, for example, the number of connections to use in a bean that manages a connection pool, or the size limit of the pool.



在容器本身中，这些bean定义表示为BeanDefinition对象，其中包含以下元数据（以及其他信息）：

- *A package-qualified class name(合格的类名):* 通常是被定义的bean的实际实现类。
- Bean行为配置元素，它说明了bean在容器中的行为方式 (scope, lifecycle callbacks, and so forth).
- 引用其所需要的其他相关Bean，这些引用也称为协作者或依赖关系。
- 创建Bean时设置其他相关配置，例如：在管理连接池的bean中使用的连接数或池的大小限制。



Table 1. The bean definition

| Property                 | Explained in…                            |
| ------------------------ | ---------------------------------------- |
| class                    | [Instantiating beans](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-class) |
| name                     | [Naming beans](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-beanname) |
| scope                    | [Bean scopes](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-scopes) |
| constructor arguments    | [Dependency Injection](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-collaborators) |
| properties               | [Dependency Injection](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-collaborators) |
| autowiring mode          | [Autowiring collaborators](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-autowire) |
| lazy-initialization mode | [Lazy-initialized beans](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-lazy-init) |
| initialization method    | [Initialization callbacks](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-lifecycle-initializingbean) |
| destruction method       | [Destruction callbacks](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-lifecycle-disposablebean) |





In addition to bean definitions that contain information on how to create a specific bean, the `ApplicationContext`implementations also permit the registration of existing objects that are created outside the container, by users.

除了包含有关如何创建特定bean的信息的bean定义之外，ApplicationContext实现还允许用户注册在容器外部创建的现有对象。



### 1.3.1. Naming beans

Every bean has one or more identifiers. These identifiers must be unique within the container that hosts the bean. A bean usually has only one identifier, but if it requires more than one, the extra ones can be considered aliases.

每个Bean 都有一个或多个标识符，在它们的容器中，这些标识符必须是唯一的。一个bean通常只有一个标识符，但是如果它需要多个标识符，额外的标识符可以被认为是别名。



If you want to introduce other aliases to the bean, you can also specify them in the `name` attribute, separated by a comma (`,`), semicolon (`;`), or white space.

如果要将其他别名引入到bean中，还可以在name属性中指定它们，并用逗号（，），分号（;）或空格分隔。



#### Bean Naming Conventions

That is, bean names start with a lowercase letter, and are camel-cased from then on. Examples of such names would be (without quotes) `'accountManager'`, `'accountService'`, `'userDao'`, `'loginController'`, and so forth.

也就是说，bean名称以小写字母开头，并且从此开始以骆驼为基础。这样的名字的例子是（不带引号）'accountManager'，'accountService'，'userDao'，'loginController'等等。



### 1.3.2. Instantiating beans

If you use XML-based configuration metadata, you specify the type (or class) of object that is to be instantiated in the `class`attribute of the `<bean/>` element. This `class` attribute, which internally is a `Class` property on a `BeanDefinition` instance, is usually mandatory. (For exceptions, see [Instantiation using an instance factory method](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-class-instance-factory-method) and [Bean definition inheritance](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-child-bean-definitions).) You use the `Class` property in one of two ways:

- Typically, to specify the bean class to be constructed in the case where the container itself directly creates the bean by calling its constructor reflectively, somewhat equivalent to Java code using the `new` operator.
- To specify the actual class containing the `static` factory method that will be invoked to create the object, in the less common case where the container invokes a `static` *factory* method on a class to create the bean. The object type returned from the invocation of the `static` factory method may be the same class or another class entirely.

如果您使用基于XML的配置元数据，则可以指定要在<bean />元素的class属性中实例化的对象的类型（或类）。这个类属性在内部是一个BeanDefinition实例的Class属性，通常是强制性的。 （有关例外情况，请参阅使用实例工厂方法和Bean定义继承进行实例化。）可以通过以下两种方式之一使用Class属性：

- 通常，在容器本身通过反射调用其**构造函数**直接创建bean的情况下，指定要构造的bean类，这与使用new运算符的Java代码有点相同
- 要指定包含将被调用来创建对象的**静态工厂方法**的实际类，那么容器在类上调用静态工厂方法以创建该Bean的情况较少。从调用静态工厂方法返回的对象类型可以是完全相同的类或另一个类。



#### Instantiation with a constructor

When you create a bean by the constructor approach, all normal classes are usable by and compatible with Spring. That is, the class being developed does not need to implement any specific interfaces or to be coded in a specific fashion. Simply specifying the bean class should suffice. However, depending on what type of IoC you use for that specific bean, you may need a default (empty) constructor.

Spring 支持所有普通类通过构造方法进行初始化。也就是说我们不需要实现其他任何借口或者做特殊的编码。只需要指定Bean 类型即可。然而，如果需要通过这种方式进行依赖控制，需要提供一个默认构造方法。



#### Instantiation with a static factory method

When defining a bean that you create with a static factory method, you use the `class` attribute to specify the class containing the `static` factory method and an attribute named `factory-method` to specify the name of the factory method itself.

当你想要构建一个通过静态工厂方法创建的Bean 时，你需要通过Class 属性进行指定对象类型，通过factory-method 指定工厂方法。



```
<bean id="clientService"
    class="examples.ClientService"
    factory-method="createInstance"/>
```



```
public class ClientService {
    private static ClientService clientService = new ClientService();
    private ClientService() {}

    public static ClientService createInstance() {
        return clientService;
    }
}
```





#### Instantiation using an instance factory method

Similar to instantiation through a [static factory method](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-class-static-factory-method), instantiation with an instance factory method invokes a non-static method of an existing bean from the container to create a new bean. To use this mechanism, leave the `class` attribute empty, and in the `factory-bean` attribute, specify the name of a bean in the current (or parent/ancestor) container that contains the instance method that is to be invoked to create the object. Set the name of the factory method itself with the `factory-method`attribute.

与通过静态工厂方法实例化类似，使用实例工厂方法的实例化从容器调用现有bean的非静态方法以创建新的bean。要使用此机制，请将类属性保留为空，并在factory-bean属性中指定当前（或父/祖代）容器中包含要调用以创建对象的实例方法的bean的名称。使用factory-method属性设置工厂方法本身的名称。



```
<!-- the factory bean, which contains a method called createInstance() -->
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
    <!-- inject any dependencies required by this locator bean -->
</bean>

<!-- the bean to be created via the factory bean -->
<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>
```

```
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();

    public ClientService createClientServiceInstance() {
        return clientService;
    }
}
```

