# Spring In Action 5th Edition 读书笔记（二）



## Chapter 2 Developing Web Applications



### 1.摘录与解读

```html
	Generally, I prefer to only use @RequestMapping at the class level to specify the basepath. I use the more specific @GetMapping, @PostMapping, and so on, on each of the handler methods.
	解读：对于@RequestMapping和@GetMapping等注解比较好的使用方式是：用@RequestMapping来标注类从而确定该类所要映射的URL的路径前缀；而@GetMapping等注解则直接标注在方法上。
```

```html
	View libraries such as Thymeleaf are designed to be decoupled from any particular web  framework.  As  such,  they’re  unaware  of  Spring’s  model  abstraction  and  are unable  to  work  with  the  data  that  the  controller  places  in  Model.  But  they  can  work with  servlet  request  attributes.  Therefore,  before  Spring  hands  the  request  over  to  a view, it copies the model data into request attributes that Thymeleaf and other view-templating options have ready access to.
	解读：Spring是与具体的视图解耦的，这样便于我们灵活地选择视图工具。此外，模板不能直接使用我们在控制器中放入到Model实例中的数据，但是模板能够使用servlet request中的属性，因此spring在将request交还给视图前，将会被Model实例中的数据复制到request中，从而能够让视图获取到这些数据。
```

```ht
	 But  when  a  controller  is simple enough that it doesn’t populate a model or process input—as is the case with your HomeController—there’s another way that you can define the controller.
	 ...
	 WebMvcConfigurer defines several methods for configuring Spring MVC. Even though it’s an interface, it provides default implementations of all the  methods,  so  you  only  need  to  override  the  methods  you  need.  
	 解读：当一个控制器负责提供视图并且不操作任何Model对象时。我们无需为该控制器编写一个类，可以采取更简单的方式，即实现WebMvcConfiguration接口并覆盖addViewControllers()。WebMvcConfiguration提供了一些方法来配置Spring MVC，并且它为所有方法都提供了默认的实现(Java 8提供的语法)，因此我们可以选择我们所需要的方法进行实现。
```

```html
 	You’ll  notice  in  table  2.2  that  JSP  doesn’t  require  any  special  dependency  in  thebuild.  That’s  because  the  servlet  container  itself  (Tomcat  by  default)  implementsthe JSP specification, thus requiring no further dependencies. But there’s a gotcha if you choose to use JSP. As it turns out, Java servlet contain-ers including  embedded  Tomcat  and  Jetty  containers usually  look  for  JSPs  some-where under /WEB-INF. 
	But if you’re building your application as an executable JARfile,  there’s  no  way  to  satisfy  that  requirement.  Therefore,  JSP  is  only  an  option  if you’re building your application as a WAR file and deploying it in a traditional servlet container.  If  you’re  building  an  executable  JAR  file,  you  must  choose  Thymeleaf,FreeMarker, or one of the other options in table 2.2.
	解读：JSP和其他模板引擎不同，它无需我们引入starter依赖，它的渲染由servlet容器来完成。而Java的servlet容器往往会去WEB-INF目录下去寻找JSP文件，而如果我们以可执行JAR的形式去创建应用程序将不存在WEB-INF目录，因此只有当我们打算把应用程序以WAR的形式构建时，JSP才是可选的方案，倘若以JAR的形式构建程序则只能选择Thymeleaf，FreeMarker等模板引擎。
```

```html
	And with Spring Boot, you don’t need to do anything special to add validation libraries to yourproject, because the Validation API and the Hibernate implementation of the Valida-tion  API  are  automatically  added  to  the  project as transient dependencies of Spring Boot’s web starter. To apply validation in Spring MVC, you need to
	Declare  validation  rules  on  the  class  that  is  to  be  validated:  specifically,  the Taco class.
	Specify  that  validation  should  be  performed  in  the  controller  methods  that require  validation:  specifically,  the  DesignTacoController’s processDesign() method and OrderController’s processOrder() method.
	Modify the form views to display validation errors.
	解读：Spring Boot的web-starter自动帮我们引入了JSR-303的Hibernate实现，我们在Spring MVC中对数据进行验证所要做的主要是三步：
	1.在领域(domain)对象中对需要验证的类添加注解来规定验证的规则。
	2.在控制器的方法中对所要验证的参数用注解(如：@Valid)进行标注。
	3.在视图中编写如何显示验证未通过时所返回错误信息。
```



### 2.出现的重要注解

本章中出现了部分LomBok注解，在此将LomBok重要注解略作总结：

**@Data:**

​	标注在类上，会为类的所有属性自动生成setter/getter、equals、canEqual、hashCode、toString方法，如为final属性，则不会为该属性生成setter方法。

**@Getter/@Setter/@Tostring：**

​	标注在类上，只为类自动创建所有字段的getter/setter方法和Tostring方法。

**@NoArgsConstructor/@AllArgsConstructor：**

​	标注在类上，帮类自动生成构造器。

**@Log4j：**

​	标注在类上，会自动为类生成一个名为log的Logger对象。

**@NonNull：**

​	标注在方法的形参上，若该形参的实参为null则抛出空指针异常。

**@Cleanup：**

​	标注在流对象上，该对象会在适当时机被自动关闭。

**@Builder：**

​	标注在类上，为该类提供构建者模式的创建对象方式。



本章还用到了JSR-303的Hibernate实现，在此也将相关重要注解略作总结：

**@NotNull/@Null：**

​	标注在类的字段上，检查该字段不能为null或只能为null。

**@NotBlank：**		

​	标注在类的字符串类型字段上，检查该字段不能为null且不能为空白字符串。

**@Length(min=,max=)：**

​	标注在类的字符串类型字段上，检查该字符串的长度。

**@Pattern(regex=, flag=)：**

​	标注在String类型的字段上，该字段的值必须匹配给定的正则表达式。

**@Min/@Max：**

​	标注在类的数字类型以及String类型字段上，检查该字段不能小于或大于某个值。

**@Range(min=, max=)：**

​	标注在类的数字类型以及String类型字段上，检查该字段的值必须在给定范围内。

**@Future：**

​	标注在类的Date或Calendar类型字段上，检查给定的世界要比当前时间晚。

**@Digits(integer=, fraction=)：**

​	标注在类的数字类型以及String类型字段上，检查此值是否是一个数字,并且这个数字的整数部分不超过integer定义的位数, 和小数部分不超过fraction 定义的位数.

**@Size(min=, max=)：**

​	标注在类的集合类型以及String类型字段上，检查该值的size是否在给定区间内。

以上注解均有一个message属性，表示验证未通过时需要返回的字符串信息。

**@Valid：**

​	标注在方法的形参上，表示需要对该参数进行JSR-303验证。



