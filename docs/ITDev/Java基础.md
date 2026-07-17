---
coding: UTF-8
title: Java基础
date: 2026-03-22T23:03:00
updated: 2026-07-17T02:20:43
tags: []
share: true
---

# Java基础

## 1.编译

在`JAVA_HOME`的`bin`目录下找到很多可执行文件：

- java：这个可执行程序其实就是JVM，运行Java程序，就是启动JVM，然后让JVM执行指定的编译后的代码；
- javac：这是Java的编译器，它用于把Java源码文件（以`.java`后缀结尾）编译为Java字节码文件（以`.class`后缀结尾）；
- jar：用于把一组`.class`文件打包成一个`.jar`文件，便于发布；
- javadoc：用于从Java源码中自动提取注释并生成文档；
- jdb：Java调试器，用于开发阶段的运行调试。

---

## 2.OOP

- 重载(Overload):方法名相同，参数不同，返回值类型相同。
- 重写(Override):用于多态，子类重写父类的方法。
- 抽象类(abstract):必须有方法是抽象方法，不用写内容，重在规范约束，要求子类必须重写。
- 接口(interface):抽象类中没有字段，全部都是抽象方法，则可以改为接口。接口定义的所有方法默认都是public abstract的。一个子类可以继承多个接口。

|       |  abstract class  |        interface        |
| :---: | :--------------: | :---------------------: |
|   继承  | 只能extends一个class | 可以implements多个interface |
|   字段  |     可以定义实例字段     |         不能定义实例字段        |
|  抽象方法 |     可以定义抽象方法     |         可以定义抽象方法        |
| 非抽象方法 |     可以定义非抽象方法    |      可以定义default方法      |

- 静态方法经常用于工具类

---

## 3.命名空间

### 3.1 包

1. package说明

- 一个类总是属于某个包（包名.类名）
- 在Java虚拟机执行的时候，JVM只看完整类名，因此，只要包名不同，类就不同。
- 包可以是多层结构，用`.`隔开。例如：`java.util`。
- 位于同一个包的类，可以访问包作用域的字段和方法。
- 不用`public`、`protected`、`private`修饰的字段和方法就是包作用域。

2. Java编译器最终编译出的`.class`文件只使用_完整类名_，因此，在代码中，当编译器遇到一个`class`名称时：

- 如果是完整类名，就直接根据完整类名查找这个`class`；

- 如果是简单类名，按下面的顺序依次查找：
	- 查找当前`package`是否存在这个`class`；
	- 查找`import`的包是否包含这个`class`；
	- 查找`java.lang`包是否包含这个`class`。

- 如果按照上面的规则还无法确定类名，则编译报错。

3. 编写class的时候，编译器会自动帮我们做两个import动作：

- 默认自动`import`当前`package`的其他`class`；
- 默认自动`import java.lang.*`。

> [!NOTE] 包名必须完全一致，包没有父子关系，`com.apache`和`com.apache.abc`是不同的包。

> [!INFO] 用`final`修饰`class`可以阻止被继承。用`final`修饰方法可以防止被重写。用`final`修饰字段可以防止被修改值。

4. Java核心类库

- java.lang包 – 该包是Java语言的核心包，并且该包中的所有内容由Java虚拟机自动导入。如：System类、String类、…
- java.util包 – 该包是Java语言的工具包，里面提供了大量工具类以及集合类等。如：Scanner类、Random类、List集合、…
- java.io包 – 该包是Java语言中的输入输出包，里面提供了大量读写文件相关的类等。如：FileInputStream类、FileOutputStream类、…
- java.net包 – 该包是Java语言中的网络包，里面提供了大量网络编程相关的类等。如：ServerSocket类、Socket类、…
- java.sql 包 – 该包是Java语言中的数据包，里面提供了大量操作数据库的类和接口等。如：DriverManager类、Connection接口、

### 3.2 classpath

- `classpath`是JVM用到的一个环境变量，它用来指示JVM如何搜索`class`。所以，`classpath`就是一组目录的集合，它设置的搜索路径与操作系统相关。
- 在IDE中运行Java程序，IDE自动传入的`-cp`参数是当前工程的`bin`目录和引入的jar包。而在命令行中应当明确指出，例如`  java -cp .;C:\work\project1\bin;C:\shared abc.xyz.Hello `。（`-cp`也可以写全就是`-classpath`）

### 3.3 jar

- 如果有很多`.class`文件，散落在各层目录中，肯定不便于管理。如果能把目录打一个包，变成一个文件，就方便多了。
- jar包就是用来干这个事的，它可以把`package`组织的目录层级，以及各个目录下的所有文件（包括`.class`文件和其他文件）都打成一个jar文件，这样一来，无论是备份，还是发给客户，就简单多了。`java -cp ./hello.jar abc.xyz.Hello`

> [!NOTE] jar只是用于存放class的容器，它并不关心class之间的依赖。

### 3.4 class版本

- Java 8，Java 11，Java 17，是指JDK的版本，也就是JVM的版本，更确切地说，就是`java.exe`这个程序的版本。
- Java 11对应的class文件版本是55，而Java 17对应的class文件版本是61。

### 3.5 模块（Module）

- 一个大型Java程序会生成自己的jar文件，同时引用依赖的第三方jar文件，而JVM自带的Java标准库，实际上也是以jar文件形式存放的，这个文件叫`rt.jar`，一共有60多M。所以要运行main函数就得设置`classpath`搜索好多路径的jar包。

- 如果`a.jar`必须依赖另一个`b.jar`才能运行，那我们应该给`a.jar`加点说明`module-info.class`啥的，让程序在编译和运行的时候能自动定位到`b.jar`，这种自带“依赖关系”的class容器就是模块。

> [!NOTE] 把一堆class封装为jar仅仅是一个打包的过程，而把一堆class封装为模块则不但需要打包，还需要写入依赖关系。此外，模块支持多版本，即在同一个模块中可以为不同的JVM提供不同的版本。

---

## 4. Boxing

实际上，因为包装类型非常有用，Java核心库为每种基本类型都提供了对应的包装类型：

|   基本类型  |       对应的引用类型       |
| :-----: | :-----------------: |
| boolean |  java.lang.Boolean  |
|   byte  |    java.lang.Byte   |
|  short  |   java.lang.Short   |
|   int   |  java.lang.Integer  |
|   long  |    java.lang.Long   |
|  float  |   java.lang.Float   |
|  double |   java.lang.Double  |
|   char  | java.lang.Character |

> [!NOTE] 所有的包装类型都是不变类。`BigInteger`和`Integer`、`Long`一样，也是不可变类，总是使用compareTo()比较两个BigDecimal的值，不要使用equals()！

## 5. Enum

`enum`定义的类型就是`class`，只不过它有以下几个特点：

- 定义的`enum`类型总是继承自`java.lang.Enum`，且无法被继承；
- 只能定义出`enum`的实例，而无法通过`new`操作符创建`enum`的实例；
- 定义的每个实例都是引用类型的唯一实例；
- 可以将`enum`类型用于`switch`语句。

---

## 6. Exception

1. Java标准库定义的常用异常包括：

```
Exception
├─ RuntimeException
│  ├─ NullPointerException(NPE)
│  ├─ IndexOutOfBoundsException
│  ├─ SecurityException
│  └─ IllegalArgumentException
│     └─ NumberFormatException
├─ IOException
│  ├─ UnsupportedCharsetException
│  ├─ FileNotFoundException
│  └─ SocketException
├─ ParseException
├─ GeneralSecurityException
├─ SQLException
└─ TimeoutException
```

2. Java中Logging模块定义了7个等级，默认是INFO

```
 SEVERE
 WARNING
 INFO
 CONFIG
 FINE
 FINER
 FINES
```

3. Commons Logging定义了6个日志级别，默认级别是INFO。

```
FATAL
ERROR
WARNING
INFO
DEBUG
TRACE
```

4. 对比Commons Logging和SLF4J的接口：

| Commons Logging                       | SLF4J                   |
| ------------------------------------- | ----------------------- |
| org.apache.commons.logging.Log        | org.slf4j.Logger        |
| org.apache.commons.logging.LogFactory | org.slf4j.LoggerFactory |

## 7. Reflection

1. 基础定义

- 反射就是Reflection，Java的反射是指程序在运行期可以拿到一个对象的所有信息。

- 反射是为了解决在运行期，对某个实例一无所知的情况下，如何调用其方法。

- 除了int等基本类型外，Java的其他类型全部都是class（包括interface）。

- class（包括interface）的本质是数据类型（Type）。无继承关系的数据类型无法赋值

```
Number n = new Double(123.456); // OK\
String s = new Double(123.456); // compile error!\
```

- 由于JVM为每个加载的class创建了对应的Class实例，并在实例中保存了该class的所有信息，包括类名、包名、父类、实现的接口、所有方法、字段等，因此，如果获取了某个Class实例，我们就可以通过这个Class实例获取到该实例对应的class的所有信息。
- 这种通过Class实例获取class信息的方法称为反射（Reflection）。

> [!Note] 每个类（class）或者基本数据类型（e.g.,int）都是一种数据类型（Type）。

> [!Note] 其中Java为每个类（class）都创建了一个实例（Class），这个实例与其他实例不同，它的实例类型是Class类型，这个实例记录了class的完整信息。

## 8. Maven

### 8.1 基础配置

- 本地仓库地址路径的配置
- 下载包与插件时的镜像源的配置
- 指定javajdk版本的配置

### 8.2 Maven指令

```
mvn compile
mvn clean
mvn test
mvn package
mvn install

mvn aechetype:generate
	-DgroupId={project-packaging}
	-DartifactId={project-name}
	-DarchetypeArtifactId=maven-archetype-quickstart
	-DubteractiveMode=false

//创建Java工程
mvn archetype:generate -DgroupId=com.demo -DartifactId=java-project -DarchetypeArtifactId=maven-archetype-quickstart -Dversion=0.0.1-snapshot -DinteractiveMode=false
//创建Web工程
mvn archetype:generate -DgroupId=com.demo -DartifactId=web-project -DarchetypeArtifactId=maven-archetype-webapp -Dversion=0.0.1-snapshot -DinteractiveMode=false

```

### 8.3 Maven工程结构

![](../assets/Java%E5%9F%BA%E7%A1%80.png)
![](../assets/Java%E5%9F%BA%E7%A1%80-1.png)

### 8.4 Maven依赖范围

![](../assets/Java%E5%9F%BA%E7%A1%80-2.png)
![](../assets/Java%E5%9F%BA%E7%A1%80-3.png)

### 8.5 Maven主要功能

1. 提供了一套标准化的项目结构
2. 提供了一套标准化的构建流程（编译，测试，打包，发布）
3. 提供了一套依赖管理机制

### 8.6 Maven模型

![](../assets/Java%E5%9F%BA%E7%A1%80-4.png)

## 9. Spring

### 9.1 Spring体系结构

- Spring是分层的JavaSE/EE应用full-stack轻量级开源框架，以IoC(Inverse Of Control:反转控制)和AOP(Aspect Oriented Programming:面向切面编程)为内核。
- ![](../assets/Java%E5%9F%BA%E7%A1%80-5.png)

### 9.2 Spring的开发示例

1. 开发原理
		![](../assets/Java%E5%9F%BA%E7%A1%80-6.png)
2. 开发流程
		![](../assets/Java%E5%9F%BA%E7%A1%80-7.png)

> [!note] 第4步，Spring配置文件通常取名为“applicationContext.xml”。

> [!note] 配置Bean标签，参数有id，class，scope，init-method，destroy-method.

3. scope配置的意义
		![](../assets/Java%E5%9F%BA%E7%A1%80-8.png)

4. Bean实例化的方式
		- 无参构造方法
		- 工厂静态方法
		- 工厂实例方法

### 9.3 依赖注入

1. Bean的依赖注入（Dependency Injection，它是Spring框架核心IOC的具体实现）
2. 注入方法

- set方法注入

- 构造方法注入

3. 注入类型

- 普通数据类型
- 引用注入类型
- 集合数据类型

4. 配置文件

```
<bean> 标签
	id 属性 ： 在容器中Bean实例的唯一标识，不允许重复
	class属性：要实例化的Bean的全限定名（也叫全包名）
	scope属性：Bean的作用范围，常用是singleton（默认）和prototype
	<property>标签：属性注入
		name属性：属性名称
		value属性：注入的普通属性值
		ref属性：注入的对象引用值
		<list>标签
		<map>标签
		<properties>标签
	<constructor-arg>标签
<import>标签：导入其他的Spring的分文件
```

### 9.4 Application Context

![](../assets/Java%E5%9F%BA%E7%A1%80-10.png)

### 9.5 注解开发

1. 原始注解
		![](../assets/Java%E5%9F%BA%E7%A1%80-11.png)
		![](../assets/Java%E5%9F%BA%E7%A1%80-12.png)
		Autowired 和 Qualifier进行set注入的时候可以不需要写set方法；使用xml配置时需要写set方法。
2. 新注解
		![](../assets/Java%E5%9F%BA%E7%A1%80-13.png)

### 9.6 SpringJunit

- Spring每次测试都需要先创建容器，然后再拿到bean进行测试，否则会出现空指针。
- 因此
	- 让SpringJunit负责创建Spring容器
	- 将需要测试的Bean直接在测试类中进行注入
-

## 10 JavaWeb

### 10.1 简介

1. 关系
		![](../assets/Java%E5%9F%BA%E7%A1%80-14.png)
2. 内容
		1. 数据库：MySQL，JDBC，MyBatis
		2. 前端：HTML+CSS，JS，Ajax+Vue+ElementUI
		3. Web核心：Tomcat+HTTP+Servlet，Request+Response，JSP，Cookie+Session，Filter+Listener
3. 数据库查询
		![](../assets/Java%E5%9F%BA%E7%A1%80-15.png)

### 10.2 JDBC

1. JDBC就是使用JAVA语言操作关系型数据库的一套API
		![](../assets/Java%E5%9F%BA%E7%A1%80-16.png)
2. 逻辑
		![](../assets/Java%E5%9F%BA%E7%A1%80-17.png)
3. 预防SQL注入
		![](../assets/Java%E5%9F%BA%E7%A1%80-18.png)
4. 抽取`jdbc`配置文件
		![](../assets/Java%E5%9F%BA%E7%A1%80-19.png)
5. 缺点
		![](../assets/Java%E5%9F%BA%E7%A1%80-20.png)

### 10.3 数据源（连接池）

- 常见的数据源：DBCP、C3P0、BoneCP、Druid

- 数据库连接池是个容器，负责分配、管理数据库连接（Connection）

- 数据库连接池允许应用程序重复使用一个现有的数据库连接，而不是再重新建立一个

- 优势
	- 资源重用
	- 提升系统相应速度
	- 避免数据库连接遗漏

- 数据源开发步骤：

	1. 导入数据源的坐标和驱动坐标
	2. 创建数据源的对象
	3. 设置数据源的基本连接数据（账号、密码）
	4. 获取连接资源和归还连接资源

- 图示
	![](../assets/Java%E5%9F%BA%E7%A1%80-21.png)

- 数据库连接池的标准接口实现：DataSource

- 增删改查数据
	![](../assets/Java%E5%9F%BA%E7%A1%80-22.png)

## 11 MyBatis

### 11.1 MyBatis是什么

- MyBatis是一款持久层框架，用于简化JDBC开发
- 持久层是负责将数据保存到数据库的那一层代码

> [!Note] JaveEE的三层架构：表现层、业务层、持久层

### 11.2 MaBatis简化

6. 处理JDBC连接过程的硬编码问题：配置文件
7. 处理JDBC连接过程的操作繁琐问题：自动完成

> [!Summary] MyBatis免除了几乎所有的JDBC代码以及设置参数和获取结果集的工作

### 11.3 Mapper代理开发

- 目的
	1. 解决原生方式中的硬编码
	2. 简化后期执行SQL
- 图示
	![](../assets/Java%E5%9F%BA%E7%A1%80-23.png)
- 步骤要求
	![](../assets/Java%E5%9F%BA%E7%A1%80-24.png)

### 11.4 MyBatis核心配置文件

![](../assets/Java%E5%9F%BA%E7%A1%80-25.png)
