# Spring In Action 5th Edition 读书笔记（一）



## Chapter 1 Get Started With Spring



### 1.摘录与解读

这一章通过一个最简单的Spring boot程序讲解了Spring boot程序的基本骨架，以及在结尾部分介绍了Spring家族中目前的主要成员。

个人认为比较有意义的桥段摘录如下：


 	Traditional Java web applications are packaged as WAR files, leaving JAR filesthe packaging of choice for libraries and the occasional desktop UI application.The choice of JAR packaging is a cloud-minded choice. Whereas WAR files are per-fectly suitable for deploying to a traditional Java application server, they’re not a natural  fit  for  most  cloud  platforms.
	解读：传统的Java web应用往往会被打包成War包的形式，而它所依赖的库以及UI相关应用则会被打包成Jar包的形式。而基于云的应用往往会选择打包成Jar包的形式。打包成War包更适合传统Java Web应用进行部署，但它们并非天然适合绝大多数云平台。


	 Next,  take  note  of  the  <parent>  element  and,  more  specifically,  its  <version>child. This specifies that your project has spring-boot-starter-parent as its parentPOM.  Among  other  things,  this  parent  POM  provides  dependency  management  forseveral libraries commonly used in Spring projects. For those libraries covered by the parent POM, you won’t have to specify a version, as it’s inherited from the parent.
	解读：Spring Boot的Pom.xml中<parent>标签中引入的spring-boot-starter-parent使得我们无需再管理应用中引入的依赖的版本。(这些依赖必须是以spring-boot-strater-xxx的形式引入的)



	Spring Boot starter dependencies are special in that they typically don’t have any  library  code  themselves,  but  instead  transitively  pull  in  other  libraries.  Theses tarter dependencies offer three primary benefits:
	Your  build  file  will  be  significantly  smaller  and  easier  to  manage  because  you won’t need to declare a dependency on every library you might need.
	You’re  able  to  think  of  your  dependencies  in  terms  of  what  capabilities  they provide, rather than in terms of library names. If you’re developing a web application, you’ll add the web starter dependency rather than a laundry list of individual libraries that enable you to write a web application.
	You’re freed from the burden of worry about library versions. You can trust that for a given version of Spring Boot, the versions of the libraries brought in tran-sitively  will  be  compatible.  You  only  need  to  worry  about  which  version  of Spring Boot you’re using.
	解读：spring boot提供的starter本身不提供任何库的代码，但它们能够透明地引入其他库，由starter依赖的形式来引入第三方库主要有三个优点：
	1.我们的构建文件(如pom.xml)中的内容将会大大减少从而更容易管理，因为一个starter依赖包含了许多库的依赖。
	2.我们在引入依赖时只要根据所要实现的功能去选择依赖即可，而不用为了实现一个功能去记忆并引入大量的库。
	3.我们不必再自己去管理引入的库的版本，spring boot将负责管理这一内容。这对于开发者来说是极大的利好，因为我们自己引入库时经常会因为版本选择不当而引发问题。


Finally, the build specification ends with the Spring Boot plugin. This plugin performs a few important functions:
	It provides a Maven goal that enables you to run the application using Maven.
	It ensures that all dependency libraries are included within the executable JAR file and available on the runtime classpath.
	It  produces  a  manifest  file  in  the  JAR  file  that  denotes  the  bootstrap  class as the main class for the executable JAR.
	解读：Spring Boot的pom.xml中还引入了spring boot的maven插件，该插件提供了以下几个重要功能：
	1.它提供给我们一些Maven goal使得我们可以用maven来运行应用程序。
	2.它能确保所有库的依赖被可执行的Jar文件所包括并且在运行时处在可用的类路径下。
	3.它在Jar文件中生成了一个文件来作为可执行Jar的主类。
	(换言之，如果我们没有引入这个插件，就无法生成可执行的JAR包)



DevTools provides Spring developers with some handy develop-ment-time tools. Among those are
	Automatic application restart when code changes
	Automatic browser refresh when browser-destined resources (such as templates,JavaScript, stylesheets, and so on) change
	Automatic disable of template caches
	Built in H2 Console if the H2 database is in use
	解读：我们如果引入spring-boot-devtools将会带来以下一些好处：当代码更改时应用程序会自动重启；当静态文件更改后浏览器会自动刷新；自动禁用模板的缓存；如果使用了H2将会提供一个H2的控制台；


In addition to starter dependencies and autoconfiguration,Spring Boot also offers a handful of other useful features:
	The  Actuator  provides  runtime  insight  into  the  inner  workings  of  an  application, including metrics, thread dump information, application health, and envi-ronment properties available to the application.
	Flexible specification of environment properties.
	Additional  testing  support  on  top  of  the  testing  assistance  found  in  the  core framework.
	解读：对Spring Boot的主要功能略作归纳：1.Spring Boot提供了starter依赖 2.Spring Boot提供了自动配置  3.Spring Boot提供了Actuator来监控程序运行时的情况 4.灵活的读取环境属性的方式 5.Spring Boot提供了额外的测试支持


### 2.出现的重要注解

罗列并解释本章中出现的一些重要注解：

**@SpringBootApplication：**
​	组合注解,标注在一个类上，包含了@SpringBootConfiguration，@EnableAutoConfiguration，@ComponentScan。
**@SpringBootConfiguration：**
​	标注在一个类上，表示该类是一个配置类。该注解是@Configuration的特殊形式，与@Configuration有相同的功能。
**@EnableAutoConfiguration：**
​	标注在类上，表示在当前程序中开启自动配置。

**@ComponentScan：**

​	标注在类上，表示在当前程序中开启自动地组建扫描。

**@RunWith：**

​	Junit提供的注解，标注在类上，为该注解提供一个SpringRunner.class从而能够在测试时创建Spring context，在传统的spring测试类中，SpringRunner.class的作用由SpringJUnit4ClassRunner.class代替，我们可以看到，SpringRunner更短，而且它与具体的Junit版本无关。

**@SpringBootTest：**

​	标注在类上，使得Junit拥有启动Spring Boot程序的能力，它起到的作用就好像Spring Boot所提供的引导类中main()中的SpringApplication.run()所起到的作用.

**@Component:**

​	标注在类上，使得该类能够被自动装配的扫描所发现。语义上表示该类是一般的无特殊含义的组件。与其有相同功能的还有**@Controller,@Service,@Repository**，不过这些注解在语义上说明被标注的类是特定的组件。

**@RequestMapping:**

​	标注在类或方法上，使得方法能够处理响应映射的HTTP请求，而**@GetMapping，@PostMapping，@DeleteMapping，@PutMapping，@PatchMapping**则是在@RequestMapping的基础上，限定了请求的类型分别是get，post，delete，put和patch。

**@RestController：**

​	标注在类上，语义上说明该类是一个提供REST服务的控制器。其实是一个组合注解，即@Controller和@ResponseBody组合而成，表示该控制器中的所有方法的返回值都将以JSON的形式交还。

**@WebMvcTest：**

​	标注在类上，被标注的类将被提供Spring MVC的测试支持。从而能够注入一个MockMvc对象来进行测试。

**@AutoWired：**

​	标注在方法或变量上，表示从IOC容器自动取出bean注入给变量或方法所需的参数。
