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





## 1.4. Dependencies

A typical enterprise application does not consist of a single object (or bean in the Spring parlance). Even the simplest application has a few objects that work together to present what the end-user sees as a coherent application. 

典型的企业应用程序不包含单个对象（或Spring中的bean）。即使最简单的应用程序也有几个对象一起工作来展示最终用户将其视为一个连贯的应用程序。



### 1.4.1. Dependency Injection

*Dependency injection* (DI) is a process whereby objects define their dependencies, that is, the other objects they work with, only through constructor arguments, arguments to a factory method, or properties that are set on the object instance after it is constructed or returned from a factory method. The container then *injects* those dependencies when it creates the bean. 

依赖注入（DI）是一个过程，通过这种过程，对象可以通过构造函数参数，工厂方法参数或者在构造或返回对象实例后设置的属性来定义它们的依赖关系，也就是说，它们使用的其他对象从工厂方法。容器在创建bean时会注入这些依赖关系。





#### Constructor-based dependency injection

*Constructor-based* DI is accomplished by the container invoking a constructor with a number of arguments, each representing a dependency.

构造方法注入是通过容器调用类的带参构造方法进行创建。基于构造器的DI通过容器调用具有多个参数的构造函数完成，每个参数表示一个依赖项

```
public class SimpleMovieLister {

    // the SimpleMovieLister has a dependency on a MovieFinder
    private MovieFinder movieFinder;

    // a constructor so that the Spring container can inject a MovieFinder
    public SimpleMovieLister(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // business logic that actually uses the injected MovieFinder is omitted...
}
```



##### Constructor argument type matching

In the preceding scenario, the container *can* use type matching with simple types if you explicitly specify the type of the constructor argument using the `type` attribute. For example:

```
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg type="int" value="7500000"/>
    <constructor-arg type="java.lang.String" value="42"/>
</bean>
```



##### Constructor argument index

Use the `index` attribute to specify explicitly the index of constructor arguments. For example:

```
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg index="0" value="7500000"/>
    <constructor-arg index="1" value="42"/>
</bean>
```



##### Constructor argument name

You can also use the constructor parameter name for value disambiguation:

```
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg name="years" value="7500000"/>
    <constructor-arg name="ultimateAnswer" value="42"/>
</bean>
```



#### Setter-based dependency injection

*Setter-based* DI is accomplished by the container calling setter methods on your beans after invoking a no-argument constructor or no-argument `static` factory method to instantiate your bean.

在通过无参构造方法或静态工厂构造方法创建实例后，通过调用对象的set 方法进行注入。

```
public class SimpleMovieLister {

    // the SimpleMovieLister has a dependency on the MovieFinder
    private MovieFinder movieFinder;

    // a setter method so that the Spring container can inject a MovieFinder
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // business logic that actually uses the injected MovieFinder is omitted...
}
```





####  Dependency resolution process

The container performs bean dependency resolution as follows:

- The `ApplicationContext` is created and initialized with configuration metadata that describes all the beans. Configuration metadata can be specified via XML, Java code, or annotations.
- For each bean, its dependencies are expressed in the form of properties, constructor arguments, or arguments to the static-factory method if you are using that instead of a normal constructor. These dependencies are provided to the bean, *when the bean is actually created*.
- Each property or constructor argument is an actual definition of the value to set, or a reference to another bean in the container.
- Each property or constructor argument which is a value is converted from its specified format to the actual type of that property or constructor argument. By default Spring can convert a value supplied in string format to all built-in types, such as `int`, `long`, `String`, `boolean`, etc.

容器执行Bean 的依赖解析按照以下流程：

- 根据Bean 的相关配置文件，创建并初始化**ApplicationContext **。 配置元数据可以根据XML，JavaCode 或者注解进行设置。
- 对于每个bean，如果使用该属性而不是普通构造函数，则它的依赖关系以属性，构造函数参数或静态工厂方法的参数的形式表示。这些依赖将会在Bean 创建的时候进行提供。
- 每一个属性或者构造方法的参数都是需要实际定义的。
- 作为值的每个属性或构造函数参数都从其指定的格式转换为该属性或构造函数参数的实际类型。默认情况下，Spring可以将以字符串格式提供的值转换为所有内置类型， such as `int`, `long`, `String`, `boolean`, etc.



The Spring container validates the configuration of each bean as the container is created. However, the bean properties themselves are not set until the bean *is actually created*. 

容器在创建的时候会检查每一个Bean 的配置，但是在创建Bean之前不会进行设置相关配置。



Beans that are singleton-scoped and set to be pre-instantiated (the default) are created when the container is created. Scopes are defined in [Bean scopes](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-scopes). Otherwise, the bean is created only when it is requested.

单例以及预设创建的Bean 会在容器创建的时候进行初始化。否则，Bean 将会在调用的时候才会被创建。





the Spring IoC container detects this circular reference at runtime, and throws a `BeanCurrentlyInCreationException`.

Spring IOC 在检测到循环依赖后，会返回 **BeanCurrentlyInCreationException **。





### 1.4.2. Dependencies and configuration in detail

As mentioned in the previous section, you can define bean properties and constructor arguments as references to other managed beans (collaborators), or as values defined inline. Spring’s XML-based configuration metadata supports sub-element types within its `<property/>` and `<constructor-arg/>` elements for this purpose.

上述章节提到，您可以将bean属性和构造函数参数定义为对其他被容器管理的Bean（协作者）的引用，或者定义为内联的值。Spring 基于XML 的配置通过`<property/>` 和`<constructor-arg/>` 提供这种操作。



#### Straight values (primitives, Strings, and so on)

The `value` attribute of the `<property/>` element specifies a property or constructor argument as a human-readable string representation.

 `<property/>` 标签中的 `value` 属性为Bean 提供了构造函数或者变量属性可读的值。（<property />元素的value属性将属性或构造函数参数指定为可读的字符串表示形式。）

```
<bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
    <!-- results in a setDriverClassName(String) call -->
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/mydb"/>
    <property name="username" value="root"/>
    <property name="password" value="masterkaoli"/>
</bean>
```



You can also configure a `java.util.Properties` instance as:

```
<bean id="mappings"
    class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">

    <!-- typed as a java.util.Properties -->
    <property name="properties">
        <value>
            jdbc.driver.className=com.mysql.jdbc.Driver
            jdbc.url=jdbc:mysql://localhost:3306/mydb
        </value>
    </property>
</bean>
```



##### The idref element

The `idref` element is simply an error-proof way to pass the *id* (string value - not a reference) of another bean in the container to a `<constructor-arg/>` or `<property/>` element.

`idref` 标签只是一种防错的方式，可以将容器中另一个bean的id（字符串值 - 不是引用）传递给<constructor-arg />或<property />元素。

```
<bean id="theTargetBean" class="..."/>

<bean id="theClientBean" class="...">
    <property name="targetName">
        <idref bean="theTargetBean"/>
    </property>
</bean>
```



Typos are only discovered (with most likely fatal results) when the `client` bean is actually instantiated. If the `client` bean is a [prototype](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-scopes) bean, this typo and the resulting exception may only be discovered long after the container is deployed.

错别字只有在Bean 在实际创建中才会被发现。如果客户端bean是原型bean，则此类型错误和生成的异常可能仅在部署容器后很长时间才能发现。



The `local` attribute on the `idref` element is no longer supported in the 4.0 beans xsd since it does not provide value over a regular `bean` reference anymore. Simply change your existing `idref local` references to `idref bean` when upgrading to the 4.0 schema.

idref 中的local 属性在4.0 beans xsd 之后不再支持，因为它不提供对Bean 的引用。我们如果需要使用idref 标签 ，只需要修改成idref bean 即可。



#### References to other beans (collaborators)

The `ref` element is the final element inside a `<constructor-arg/>` or `<property/>` definition element. Here you set the value of the specified property of a bean to be a reference to another bean (a collaborator) managed by the container. The referenced bean is a dependency of the bean whose property will be set, and it is initialized on demand as needed before the property is set. (If the collaborator is a singleton bean, it may be initialized already by the container.) All references are ultimately a reference to another object. Scoping and validation depend on whether you specify the id/name of the other object through the `bean`, `local,` or `parent` attributes.

Ref 标签是 `<constructor-arg/>` or `<property/>` 中定义的最后一个标签。你可以通过ref 标签指定当前Bean 对容器中另外的Bean进行依赖。该依赖将会在Bean创建时进行初始化和设置（如果该依赖是单例，则会在容器中初始换好）。范围和验证取决于您是通过bean，local或parent属性指定另一个对象的id / name。（4.0 beans xsd 之后不再支持local 属性）



#### Inner beans

A `<bean/>` element inside the `<property/>` or `<constructor-arg/>` elements defines a so-called *inner bean*.

 `<property/>` or `<constructor-arg/>`  中的Bean 标签被称为内部类。

```
<bean id="outer" class="...">
    <!-- instead of using a reference to a target bean, simply define the target bean inline -->
    <property name="target">
        <bean class="com.example.Person"> <!-- this is the inner bean -->
            <property name="name" value="Fiona Apple"/>
            <property name="age" value="25"/>
        </bean>
    </property>
</bean>
```

Inner beans are *always* anonymous and they are *always* created with the outer bean.

内部类通常都是匿名的，并且随着外部类一起被创建。





#### Collections

In the `<list/>`, `<set/>`, `<map/>`, and `<props/>` elements, you set the properties and arguments of the Java `Collection` types `List`, `Set`, `Map`, and `Properties`, respectively.

```
<bean id="moreComplexObject" class="example.ComplexObject">
    <!-- results in a setAdminEmails(java.util.Properties) call -->
    <property name="adminEmails">
        <props>
            <prop key="administrator">administrator@example.org</prop>
            <prop key="support">support@example.org</prop>
            <prop key="development">development@example.org</prop>
        </props>
    </property>
    <!-- results in a setSomeList(java.util.List) call -->
    <property name="someList">
        <list>
            <value>a list element followed by a reference</value>
            <ref bean="myDataSource" />
        </list>
    </property>
    <!-- results in a setSomeMap(java.util.Map) call -->
    <property name="someMap">
        <map>
            <entry key="an entry" value="just some string"/>
            <entry key ="a ref" value-ref="myDataSource"/>
        </map>
    </property>
    <!-- results in a setSomeSet(java.util.Set) call -->
    <property name="someSet">
        <set>
            <value>just some string</value>
            <ref bean="myDataSource" />
        </set>
    </property>
</bean>
```



##### Collection merging

That is, the child collection’s values are the result of merging the elements of the parent and child collections, with the child’s collection elements overriding values specified in the parent collection.

也就是说，子集合的值是合并父集合和子集合元素的结果，子集合元素覆盖父集合中指定的值。

```
<beans>
    <bean id="parent" abstract="true" class="example.ComplexObject">
        <property name="adminEmails">
            <props>
                <prop key="administrator">administrator@example.com</prop>
                <prop key="support">support@example.com</prop>
            </props>
        </property>
    </bean>
    <bean id="child" parent="parent">
        <property name="adminEmails">
            <!-- the merge is specified on the child collection definition -->
            <props merge="true">
                <prop key="sales">sales@example.com</prop>
                <prop key="support">support@example.co.uk</prop>
            </props>
        </property>
    </bean>
<beans>
```



##### Strongly-typed collection

If you are using Spring to dependency-inject a strongly-typed `Collection` into a bean, you can take advantage of Spring’s type-conversion support such that the elements of your strongly-typed `Collection` instances are converted to the appropriate type prior to being added to the `Collection`.

如果你使用Spring 进行强数据类型Collection 的注入，你可以利用Spring 中的类型转换，将数据转换成对应Collection 的强数据类型。

```
public class Foo {

    private Map<String, Float> accounts;

    public void setAccounts(Map<String, Float> accounts) {
        this.accounts = accounts;
    }
}
```

```
<beans>
    <bean id="foo" class="x.y.Foo">
        <property name="accounts">
            <map>
                <entry key="one" value="9.99"/>
                <entry key="two" value="2.75"/>
                <entry key="six" value="3.99"/>
            </map>
        </property>
    </bean>
</beans>
```



#### XML shortcut with the p-namespace

The p-namespace enables you to use the `bean` element’s attributes, instead of nested `<property/>` elements, to describe your property values and/or collaborating beans.

在 xml 中使用 p-namespace 可以代替在 </bean> 标签中使用<property/> 标签

```
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean name="classic" class="com.example.ExampleBean">
        <property name="email" value="foo@bar.com"/>
    </bean>

    <bean name="p-namespace" class="com.example.ExampleBean"
        p:email="foo@bar.com"/>
</beans>
```

The following example shows two XML snippets that resolve to the same result: The first uses standard XML format and the second uses the p-namespace.

上述的示例代码中，两个XML 配置起到相同的作用。第一个采用XML 格式，第二个则使用p-namespace 方式



The p-namespace is not as flexible as the standard XML format. For example, the format for declaring property references clashes with properties that end in `Ref`, whereas the standard XML format does not. 

p-namespace 并没有XML 形式那么灵活，例如，p-namespace 的ref 引用 并没有校验类型，就会容易出现类型异常。



#### XML shortcut with the c-namespace

Similar to the [XML shortcut with the p-namespace](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-p-namespace), the *c-namespace*, newly introduced in Spring 3.1, allows usage of inlined attributes for configuring the constructor arguments rather then nested `constructor-arg` elements.

与p-namespace 类似，c-namespace 命名方式从Spring 3.1 开始创建，允许使用相应的命名方式来设置构造函数的参数。

```
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:c="http://www.springframework.org/schema/c"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="bar" class="x.y.Bar"/>
    <bean id="baz" class="x.y.Baz"/>

    <!-- traditional declaration -->
    <bean id="foo" class="x.y.Foo">
        <constructor-arg ref="bar"/>
        <constructor-arg ref="baz"/>
        <constructor-arg value="foo@bar.com"/>
    </bean>

    <!-- c-namespace declaration -->
    <bean id="foo" class="x.y.Foo" c:bar-ref="bar" c:baz-ref="baz" c:email="foo@bar.com"/>

</beans>
```



For the rare cases where the constructor argument names are not available (usually if the bytecode was compiled without debugging information), one can use fallback to the argument indexes:

对于变量名称不可用的特殊情况，我们可以采用变量下标来设置：

```
<!-- c-namespace index declaration -->
<bean id="foo" class="x.y.Foo" c:_0-ref="bar" c:_1-ref="baz"/>
```



In practice, the constructor resolution [mechanism](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-ctor-arguments-resolution) is quite efficient in matching arguments so unless one really needs to, we recommend using the name notation through-out your configuration.

实际上，构造函数解析机制在匹配参数方面非常有效，因此除非真的需要，否则我们建议在整个配置中使用名称符号。





### 1.4.3. Using depends-on

If a bean is a dependency of another that usually means that one bean is set as a property of another. Typically you accomplish this with the [`ref` element](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-ref-element) in XML-based configuration metadata. However, sometimes dependencies between beans are less direct; for example, a static initializer in a class needs to be triggered, such as database driver registration. The `depends-on`attribute can explicitly force one or more beans to be initialized before the bean using this element is initialized. The following example uses the `depends-on` attribute to express a dependency on a single bean:

如果一个Bean A是另一个Bean B 的依赖，那么Bean A 就是Bean B 中的某个属性。通常我们可以使用 ref 标签来完成这个元数据的设置。但是，有时候Bean 之间的依赖性并没有那么直接，例如：类中的静态初始化器需要被触发，例如数据库驱动程序注册。depends-on属性可以在使用此元素的Bean 初始化之前，明确强制一个或多个bea n 被初始化。

```
<bean id="beanOne" class="ExampleBean" depends-on="manager"/>
<bean id="manager" class="ManagerBean" />
```





### 1.4.4. Lazy-initialized beans

By default, `ApplicationContext` implementations eagerly create and configure all [singleton](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-scopes-singleton) beans as part of the initialization process. Generally, this pre-instantiation is desirable, because errors in the configuration or surrounding environment are discovered immediately, as opposed to hours or even days later. When this behavior is *not* desirable, you can prevent pre-instantiation of a singleton bean by marking the bean definition as lazy-initialized. A lazy-initialized bean tells the IoC container to create a bean instance when it is first requested, rather than at startup.

通常情况下，ApplicationContext 在初始化的过程中会默认将所有单例的Bean 进行初始化配置。通常，这种行为是可取的，因为这种操作可以提早发现配置中是否存在异常，而不是到几个小时或几天后才发现。当我们不需要程序进行默认初始化，可以通过设置 lazy-initialized 来懒加载Bean。

```
<beans default-lazy-init="true">
    <!-- no beans will be pre-instantiated... -->
</beans>
```







### 1.4.5. Autowiring collaborators

The Spring container can *autowire* relationships between collaborating beans. You can allow Spring to resolve collaborators (other beans) automatically for your bean by inspecting the contents of the `ApplicationContext`. Autowiring has the following advantages:

Spring 可以自动关联Bean 之间的依赖关系。你可以通过Spring 检查ApplicationContext 的内容来自动为你的Bean 处理相关依赖。自动装配具有以下优点：

- Autowiring can significantly reduce the need to specify properties or constructor arguments. (Other mechanisms such as a bean template [discussed elsewhere in this chapter](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-child-bean-definitions) are also valuable in this regard.)
- Autowiring can update a configuration as your objects evolve. For example, if you need to add a dependency to a class, that dependency can be satisfied automatically without you needing to modify the configuration. Thus autowiring can be especially useful during development, without negating the option of switching to explicit wiring when the code base becomes more stable.
- 自动装配可以显着减少指定属性或构造函数参数的需要。(其他机制，例如本章别处讨论的bean模板，在这方面也很有价值)
- 随着对象的发展，自动装配可以更新配置。例如，如果需要向类中添加依赖项，则可以自动满足该依赖项，而无需修改配置。 因此，自动装配在开发过程中可能特别有用，而且不会影响代码库变得更加稳定时切换到显式布线的选项。



### 1.4.6. Method injection

In most application scenarios, most beans in the container are [singletons](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-scopes-singleton). When a singleton bean needs to collaborate with another singleton bean, or a non-singleton bean needs to collaborate with another non-singleton bean, you typically handle the dependency by defining one bean as a property of the other. A problem arises when the bean lifecycles are different. Suppose singleton bean A needs to use non-singleton (prototype) bean B, perhaps on each method invocation on A. The container only creates the singleton bean A once, and thus only gets one opportunity to set the properties. The container cannot provide bean A with a new instance of bean B every time one is needed.

方法注入主要是用于解决单例Bean 引用非单例Bean 所出现的问题。



## 1.5. Bean scopes

You can control not only the various dependencies and configuration values that are to be plugged into an object that is created from a particular bean definition, but also the *scope* of the objects created from a particular bean definition. 

你不仅可以控制创建对象的依赖和相关配置，还可以控制从特定的bean定义创建的对象的范围。

| Scope                                    | Description                              |
| ---------------------------------------- | ---------------------------------------- |
| [singleton](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-scopes-singleton) | (Default) Scopes a single bean definition to a single object instance per Spring IoC container. |
| [prototype](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-scopes-prototype) | Scopes a single bean definition to any number of object instances. |
| [request](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-scopes-request) | Scopes a single bean definition to the lifecycle of a single HTTP request; that is, each HTTP request has its own instance of a bean created off the back of a single bean definition. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [session](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-scopes-session) | Scopes a single bean definition to the lifecycle of an HTTP `Session`. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [application](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-scopes-application) | Scopes a single bean definition to the lifecycle of a `ServletContext`. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [websocket](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/web.html#websocket-stomp-websocket-scope) | Scopes a single bean definition to the lifecycle of a `WebSocket`. Only valid in the context of a web-aware Spring `ApplicationContext`. |

 

### 1.5.1. The singleton scope

Only one *shared* instance of a singleton bean is managed, and all requests for beans with an id or ids matching that bean definition result in that one specific bean instance being returned by the Spring container.

容器中只管理该对象的一个实例，在请求该Bean的时候，只会返回同一个Bean。（只管理单个bean的一个共享实例，并且具有与该bean定义匹配的id或id的bean的所有请求都会导致Spring容器返回一个特定的bean实例。）



*The singleton scope is the default scope in Spring*. To define a bean as a singleton in XML, you would write, for example:

Bean 单例作用范围是默认形式，在XML 定义中：

```
<bean id="accountService" class="com.foo.DefaultAccountService"/>

<!-- the following is equivalent, though redundant (singleton scope is the default) -->
<bean id="accountService" class="com.foo.DefaultAccountService" scope="singleton"/>
```

![https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/images/singleton.png](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/images/singleton.png)

### 1.5.2. The prototype scope

The non-singleton, prototype scope of bean deployment results in the *creation of a new bean instance* every time a request for that specific bean is made. 

Prototype 作用范围即，每次在创建一个新的Bean 的时候，都会为其创建一个新的依赖。

![https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/images/prototype.png](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/images/prototype.png)

In contrast to the other scopes, Spring does not manage the complete lifecycle of a prototype bean: the container instantiates, configures, and otherwise assembles a prototype object, and hands it to the client, with no further record of that prototype instance. 

与其他作用范围区别，Spring 并不管理prototype 类型的生命周期：容器实例化，配置和以其他方式组装原型对象，并将其交给客户端，而后不再记录该原型实例。



Thus, although *initialization* lifecycle callback methods are called on all objects regardless of scope, in the case of prototypes, configured *destruction* lifecycle callbacks are *not* called.

因此，尽管所有对象在生命周期上都会调用初始化生命周期回调方法，但是在prototype 类型中，并不调用配置的销毁生命周期回调。



### 1.5.3. Singleton beans with prototype-bean dependencies

You cannot dependency-inject a prototype-scoped bean into your singleton bean, because that injection occurs only*once*, when the Spring container is instantiating the singleton bean and resolving and injecting its dependencies. If you need a new instance of a prototype bean at runtime more than once, see [Method injection](https://docs.spring.io/spring/docs/5.0.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-method-injection)

如果你想要在单例作用范围的Bean 中引入 原型作用范围的Bean，你需要借助 Method Injection 的相关知识。



### 1.5.4. Request, session, application, and WebSocket scopes

上述作用域只适用于 Web-Aware 类型的ApplicationContext（例如 XmlWebApplicationContext）



#### Initial web configuration

To support the scoping of beans at the `request`, `session`, `application`, and `websocket` levels (web-scoped beans), some minor initial configuration is required before you define your beans. (This initial setup is *not* required for the standard scopes, `singleton` and `prototype`.)

为了提供Request，session，application以及websocket 的作用范围，你需要完善一些Web 的相关配置。

`DispatcherServlet`, `RequestContextListener`, and `RequestContextFilter` all do exactly the same thing, namely bind the HTTP request object to the `Thread` that is servicing that request. This makes beans that are request- and session-scoped available further down the call chain.

`DispatcherServlet`, `RequestContextListener`, and `RequestContextFilter` 做的是同样的事情，主要就是用于拦截Http 请求，以至于能够启动Request 以及 session 作用范围。



#### Scoped beans as dependencies

The Spring IoC container manages not only the instantiation of your objects (beans), but also the wiring up of collaborators (or dependencies). If you want to inject (for example) an HTTP request scoped bean into another bean of a longer-lived scope, you may choose to inject an AOP proxy in place of the scoped bean. That is, you need to inject a proxy object that exposes the same public interface as the scoped object but that can also retrieve the real target object from the relevant scope (such as an HTTP request) and delegate method calls onto the real object.

Spring IoC容器不仅管理对象（bean）的实例化，还管理协作者（或依赖项）的连接。 如果您想要将HTTP请求范围的bean注入（例如）一个更长寿命范围的另一个bean中，您可以选择注入一个AOP代理来代替范围bean。 也就是说，您需要注入一个代理对象，该对象公开与作用域对象相同的公共接口，但也可以从相关作用域（例如HTTP请求）中检索真实目标对象，并将方法调用委托给实际对象。