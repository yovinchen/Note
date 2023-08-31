# 第二章：Spring中的Bean

## 1、Bean的配置

如果把Spring看做一个大型工厂，则Spring容器中的Bean就是该工厂的产品。要想使用这个工厂生产和管理Bean，就需要在配置文件中告诉它需要哪些Bean，以及需要使用何种方式将这些Bean装配到一起。

**test：Bean的本质就是Java中的类，而Spring中的Bean其实就是对实体类的引用，来生产Java类对象，从而实现生产和管理Bean .**

XML配置文件的根元素是<beans>，<beans>中包含了多个<bean>子元素，每个<bean>子元素定义了一个Bean，并描述了该Bean如何被装配到Spring容器中

关于<beans>元素的常用属性如下表所示：

| 属性或元素名称 |                             描述                             |
| :------------: | :----------------------------------------------------------: |
|       id       | 是一个Bean的唯一标识符,Spring容器对Bean的配置、管理都通过该属性来完成。 |
|      name      | Spring 容器同样可以通过此属性对容器中的 Bean 进行配置和管理，name 属性中可以为 Bean指定多个名称，每个名称之间用逗号或分号隔开。 |
|     class      | 该属性指定了 Bean 的具体实现类，它必须是一个完整的类名，使用类的全限定名。 |
|     scopes     | 用来设定 Bean 实例的作用域，其属性值有：singleton（单例）、prototype（原型）、request、session、global Session、application和 websocket。其默认值为 singleton。 |

#### 作用域的种类

|    作用域名称     |                             说明                             |
| :---------------: | :----------------------------------------------------------: |
| singleton（单例） | 使用singleton定义的Bean在Spring容器中将只有一个穿例，也就是说，无论有多少个Bean 引用它，始终将指向同一个对象。这也是Spring 容器默认的作用域。 |
| prototype（原型） | 每次通过Spring容器获取的prototype定义的Bean时，容器都将创建一个新的 Bean 实例。 |

**test：如果在Bean中未指定id和name，则Spring会将class值当作id使用。**

## 2、构造器实例化

首先创建一个Maven项目然后导包

```java
<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>4.3.6.RELEASE</version>
</dependency>
```

然后在 main 文件下面创建Java源码包，并在包下创建名为 com.ssm.instance.constructor 包 并在其下创建 Bean1 类

然后在 main 文件创建名为 resources 的 Resources Root （源根）并配置 Bean1 的 id 和 class 

```java
<bean id="bean1" class="com.ssm.instance.constructor.Bean1"/>
```

然后在 com.ssm.instance.constructor 包下创建 InstanceTest1 类

```java
public static void main(String[] args) {
        //定义配置文件路径
        String xmlPath = "beans1.xml";
        //ApplicationContext在加载配置文件时，对Bean进行实例化
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);
        Bean1 bean = (Bean1) applicationContext.getBean("bean1");
        System.out.println(bean);
    }
```

程序执行截图：

![image-20220309123304518](https://lsky.hhdxw.top/imghub/img/image-20220309123304518.png)

## 3、静态工厂实例化

首先在 Java 包下创建一个名为 com.ssm.instance.static_factory 包，然后在其包下创建一个 名为 Bean2 的类以及一个叫做 MyBean2Factory 的类 并在这个类中创建一个静态方法来实现返回 Bean2 实例 

```java
 //使用自己的工厂创建Bean实例
     public static Bean2 createBean(){
        return new Bean2();
    }
```

然后在  resources 下创建 beans2.xml 并输入以下代码：

```java
<bean id ="bean2"
          class="com.ssm.instance.static_factory.MyBean2Factory"
          factory-method="createBean"/>
```

然后在 static_factory 下创建一个名为 InstanceTest2 的测试类，

```java
public static void main(String[] args) {

        String xmlPath = "beans2.xml";

        ApplicationContext applicationContext =new ClassPathXmlApplicationContext(xmlPath);
        System.out.println(applicationContext.getBean("bean2"));
    }
```

程序执行截图：

![image-20220309142136727](https://lsky.hhdxw.top/imghub/img/image-20220309142136727.png)

## 4、实例工厂实例化

首先在 Java 文件下面创建一个名为 com.ssm.instance.factory 的包，并在其下面创建一个名为 Bean3 的类和一个名为 MyBean3Factory 的类，创建 Bean3 的实例方法，输入以下代码：

```java
 public MyBean3Factory (){
        System.out.println("Bean3 工厂实例化中");
    }

    //创建 Bean3 实例的方法
    public Bean3 createBean(){
        return new Bean3();
    }
```

在 resources  下创建 beans3.xml 并输入以下代码：

```java
<!--    配置工厂    -->
    <bean id="myBean3Factory"
          class="com.ssm.instance.factory.MyBean3Factory"/>

<!--        使用 factory-bean 属性指向配置的实例工厂，
            使用 factory-method 的属性确定使用工厂中的哪个方法   -->
    <bean id="bean3"
          factory-bean="myBean3Factory"
          factory-method="createBean" />
```

然后在 factory 下创建一个名为 InstanceTest3 的测试类，并输入以下代码：

```java
public static void main(String[] args) {
    //指定配置文件路径
    String xmlPath = "beans3.xml";
    //ApplicationContext在加载配置文件时，对Bean进行实例化
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);
    System.out.println(applicationContext.getBean("bean3"));
}
```

程序执行截图：

![image-20220309144326438](https://lsky.hhdxw.top/imghub/img/image-20220309144326438.png)