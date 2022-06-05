# Spring.io_learning
跟着spring.io学spring

## 第一天

Building a RESTful Web Service 
构建一个Restful风格的web服务
This guide walks you through the process of creating a “Hello, World” RESTful web service with Spring.
这个教程会引导你通过spring来创建一个“Hello， World”的RestFul风格的web服务

What You Will Build
你将会构建什么呢
You will build a service that will accept HTTP GET requests at http://localhost:8080/greeting.
你将会构建一个能够接受HTTP的get请求的服务 http://localhost:8080/greeting

It will respond with a JSON representation of a greeting, as the following listing shows:
服务将会返回一个问候的json表达式，就像下面这样
{"id":1,"content":"Hello, World!"}

You can customize the greeting with an optional name parameter in the query string, as the following listing shows:
你讲在请求里面可以加上一个可选择的问候语，就像下面这样
http://localhost:8080/greeting?name=UserCOPY

The name parameter value overrides the default value of World and is reflected in the response, as the following listing shows:
这个“name”参数会覆盖default参数”World“，并且在响应中一起返回，就像下面这样
{"id":1,"content":"Hello, User!"}COPY

What You Need
你需要什么

About 15 minutes
大概15分钟

A favorite text editor or IDE
一个喜欢的编辑器

JDK 1.8 or later
jdk1.8或者更高

Gradle 4+ or Maven 3.2+
Gradle或者Maven项目管理工具

You can also import the code straight into your IDE:
你也可以直接将代码导入到你的编辑器里

Spring Tool Suite (STS)
就是一个基于Eclipse的开发环境， 用于开发Spring应用程序。

IntelliJ IDEA

How to complete this guide
你怎么完成这个引导

Like most Spring Getting Started guides, you can start from scratch and complete each step or you can bypass basic setup steps that are already familiar to you. Either way, you end up with working code.
就像大部分spring入门指南一样，你能够一步一步跟着走或者绕过基本步骤

To start from scratch, move on to Starting with Spring Initializr.
从头开始，先构建一个spring初始化器

To skip the basics, do the following:
你可以像下面这样跳过这些步骤

Download and unzip the source repository for this guide, or clone it using Git: git clone https://github.com/spring-guides/gs-rest-service.git
去 GitHub 上下载压缩包导入或者直接 git clone

cd into gs-rest-service/initial
进入文件夹

Jump ahead to Create a Resource Representation Class.
继续创建资源表示类

When you finish, you can check your results against the code in gs-rest-service/complete.‘
完成之后，可以检查你的代码是否在这个文件夹里

Starting with Spring Initializr
You can use this pre-initialized project and click Generate to download a ZIP file. This project is configured to fit the examples in this tutorial.
解压文件

To manually initialize the project:
手动创建文件夹

Navigate to https://start.spring.io. This service pulls in all the dependencies you need for an application and does most of the setup for you.
去spring官网创建spring Initializr

Choose either Gradle or Maven and the language you want to use. This guide assumes that you chose Java.
选择你想要的语言和 Gradle 或者 Maven ,本指南假定你选择了Java

Click Dependencies and select Spring Web.
点击依赖并且选择Spring web

Click Generate.
点击生成

Download the resulting ZIP file, which is an archive of a web application that is configured with your choices.
下载压缩包，这是基于你选择的配置生成的web存档

If your IDE has the Spring Initializr integration, you can complete this process from your IDE.
如果你的ide集成了 Spring Initializr，你也可以用你的ide来实现这些步骤

You can also fork the project from Github and open it in your IDE or other editor.
你也可以用你的ide直接从GitHub上 fork 下来

Create a Resource Representation Class
创建资源表示类

Now that you have set up the project and build system, you can create your web service.
现在您已经设置了项目和构建系统，您可以创建您的 Web 服务。

Begin the process by thinking about service interactions.
先思考这个服务交互的过程

The service will handle GET requests for /greeting, optionally with a name parameter in the query string. The GET request should return a 200 OK response with JSON in the body that represents a greeting. It should resemble the following output:
该服务将处理访问的 GET 请求，可以选择在路径中中使用名称参数。 GET 请求应返回 200 OK 响应，其中包含表示问候的正文中的 JSON。它应该类似于以下输出
{
"id": 1,
"content": "Hello, World!"
}

The id field is a unique identifier for the greeting, and content is the textual representation of the greeting.
这个“id” 是一个唯一标识符， content是表示问候的文本

To model the greeting representation, create a resource representation class. To do so, provide a plain old Java object with fields, constructors, and accessors for the id and content data, as the following listing (from src/main/java/com/example/restservice/Greeting.java) shows:
先创建一个Greeting的实体类

package com.example.restservice;

public class Greeting {

	private final long id;
	private final String content;

	public Greeting(long id, String content) {
		this.id = id;
		this.content = content;
	}

	public long getId() {
		return id;
	}

	public String getContent() {
		return content;
	}
}

This application uses the Jackson JSON library to automatically marshal instances of type Greeting into JSON. Jackson is included by default by the web starter.
Jackson JSON 自动将实例转换为JSON格式，Jackson依赖在web-stater中默认包含

Create a Resource Controller
创建一个Controller

In Spring’s approach to building RESTful web services, HTTP requests are handled by a controller. These components are identified by the @RestController annotation, and the GreetingController shown in the following listing (from src/main/java/com/example/restservice/GreetingController.java) handles GET requests for /greeting by returning a new instance of the Greeting class:
使用RestFul风格的注解，在controller上注解@RestController

package com.example.restservice;

import java.util.concurrent.atomic.AtomicLong;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController {

	private static final String template = "Hello, %s!";
	private final AtomicLong counter = new AtomicLong();

	@GetMapping("/greeting")
	public Greeting greeting(@RequestParam(value = "name", defaultValue = "World") String name) {
		return new Greeting(counter.incrementAndGet(), String.format(template, name));
	}
}

This controller is concise and simple, but there is plenty going on under the hood. We break it down step by step.
这个controller很简洁，但是在底层发生了很多事，我们一步一步剖析

The @GetMapping annotation ensures that HTTP GET requests to /greeting are mapped to the greeting() method.
这个@GetMapping注解确保将HTTP的GET请求映射到greeting()方法上

There are companion annotations for other HTTP verbs (e.g. @PostMapping for POST). There is also a @RequestMapping annotation that they all derive from, and can serve as a synonym (e.g. @RequestMapping(method=GET)).
同时还有个和@GetMapping伴生的注解@PostMapping，它也是从@RequestMapping引申出来的注解

@RequestParam binds the value of the query string parameter name into the name parameter of the greeting() method. If the name parameter is absent in the request, the defaultValue of World is used.
@RequestParam 将 HTTP 请求中传来的参数与greeting()方法中的参数绑定， 如果 没有传过来这个参数， 就使用默认参数 "World"

The implementation of the method body creates and returns a new Greeting object with id and content attributes based on the next value from the counter and formats the given name by using the greeting template.
方法体执行的时候创建一个带有 “id” 和 “name” 的Greeting对象，name属性通过请求的参数来format 一下

A key difference between a traditional MVC controller and the RESTful web service controller shown earlier is the way that the HTTP response body is created. Rather than relying on a view technology to perform server-side rendering of the greeting data to HTML, this RESTful web service controller populates and returns a Greeting object. The object data will be written directly to the HTTP response as JSON.
传统mvc的controller和RESTFUl的controller不同的地方是响应主体的创立，这个 RESTful Web 服务控制器不依赖于视图技术将问候数据在服务器端呈现为 HTML，而是填充并返回一个 Greeting 对象。对象数据将作为 JSON 直接写入 HTTP 响应体

This code uses Spring @RestController annotation, which marks the class as a controller where every method returns a domain object instead of a view. It is shorthand for including both @Controller and @ResponseBody.
代码中使用到的@RestController注解，包含@Controller and @ResponseBody，并且响应是 实体类而不是视图

The Greeting object must be converted to JSON. Thanks to Spring’s HTTP message converter support, you need not do this conversion manually. Because Jackson 2 is on the classpath, Spring’s MappingJackson2HttpMessageConverter is automatically chosen to convert the Greeting instance to JSON.
这个响应的实体必须被转化为JSON格式，得益于spring的http消息支持自动转化，不需要你手动去转化

@SpringBootApplication is a convenience annotation that adds all of the following:
@SpringBootApplication 包含了一下的几个注解，很方便

@Configuration: Tags the class as a source of bean definitions for the application context.
将类标记为一个应用上下文档bean定义源(不知道怎么说合适)

@EnableAutoConfiguration: Tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings. For example, if spring-webmvc is on the classpath, this annotation flags the application as a web application and activates key behaviors, such as setting up a DispatcherServlet.
@EnableAutoConfiguration：告诉 Spring Boot 根据类路径设置、其他 bean 和各种属性设置开始添加 bean。例如，如果 spring-webmvc 在类路径上，则此注释将应用程序标记为 Web 应用程序并激活关键行为，例如设置 DispatcherServlet

@ComponentScan: Tells Spring to look for other components, configurations, and services in the com/example package, letting it find the controllers.
告诉 Spring 在 com.example 包中查找其他组件、配置和服务，让它找到控制器。

The main() method uses Spring Boot’s SpringApplication.run() method to launch an application. Did you notice that there was not a single line of XML? There is no web.xml file, either. This web application is 100% pure Java and you did not have to deal with configuring any plumbing or infrastructure.
main() 方法使用 Spring Boot 的 SpringApplication.run() 方法来启动应用程序。您是否注意到没有一行 XML？也没有 web.xml 文件。这个 Web 应用程序是 100% 纯 Java，您不必处理任何管道或基础设施的配置。


Build an executable JAR
You can run the application from the command line with Gradle or Maven. You can also build a single executable JAR file that contains all the necessary dependencies, classes, and resources and run that. Building an executable jar makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.
您可以使用 Gradle 或 Maven 从命令行运行应用程序。您还可以构建一个包含所有必要依赖项、类和资源的单个可执行 JAR 文件并运行它。构建可执行 jar 可以在整个开发生命周期、跨不同环境等中轻松地作为应用程序交付、版本化和部署服务

If you use Gradle, you can run the application by using ./gradlew bootRun. Alternatively, you can build the JAR file by using ./gradlew build and then run the JAR file, as follows:
java -jar build/libs/gs-rest-service-0.1.0.jar
gradle使用上面这个命令

If you use Maven, you can run the application by using ./mvnw spring-boot:run. Alternatively, you can build the JAR file with ./mvnw clean package and then run the JAR file, as follows:
java -jar target/gs-rest-service-0.1.0.jar
maven 使用上面这个命令

The steps described here create a runnable JAR. You can also build a classic WAR file.
Logging output is displayed. The service should be up and running within a few seconds.

下面就是一些执行测试
Test the Service
Now that the service is up, visit http://localhost:8080/greeting, where you should see:

{"id":1,"content":"Hello, World!"}
Provide a name query string parameter by visiting http://localhost:8080/greeting?name=User. Notice how the value of the content attribute changes from Hello, World! to Hello, User!, as the following listing shows:

{"id":2,"content":"Hello, User!"}COPY
This change demonstrates that the @RequestParam arrangement in GreetingController is working as expected. The name parameter has been given a default value of World but can be explicitly overridden through the query string.

Notice also how the id attribute has changed from 1 to 2. This proves that you are working against the same GreetingController instance across multiple requests and that its counter field is being incremented on each call as expected.

Summary
Congratulations! You have just developed a RESTful web service with Spring.