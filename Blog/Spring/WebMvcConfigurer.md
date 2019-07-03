# WebMvcConfigurer

## 一、前言

用JavaConfig的形式配置Spring MVC，我们往往会选择继承WebMvcConfigurerAdapter，而在Spring 5和Spring Boot 2.0中，WebMvcConfigurerAdapter已经被废弃，Spring更加推荐我们直接实现WebMvcConfigurer接口来配置Spring MVC。



### 二、WebMvcConfigurer配置Spring MVC详解

#### 2.1 配置视图解析器

```java
//通过调用registry的方法来完成视图解析器的配置
public void configureViewResolvers(ViewResolverRegistry registry);
```

ViewResolverRegistry对象的主要方法如下：



| Modifier and Type | Method and Description                                       |
| :---------------- | :----------------------------------------------------------- |
| `void`            | `beanName()`Register a bean name view resolver that interprets view names as the names of [`View`](https://docs.spring.io/spring/docs/5.1.8.RELEASE/javadoc-api/org/springframework/web/servlet/View.html) beans. |

注册一个BeanNameViewResolver视图解析器，功能为：按照视图名去找同名的bean来返回，该bean必须是实现了接口View。

| Modifier and Type                  | Method and Description                                       |
| :--------------------------------- | :----------------------------------------------------------- |
| `UrlBasedViewResolverRegistration` | `jsp()`Register JSP view resolver using a default view name prefix of "/WEB-INF/" and a default suffix of ".jsp". |
| `UrlBasedViewResolverRegistration` | `jsp(String prefix, String suffix)`Register JSP view resolver with the specified prefix and suffix. |

注册JSP的视图解析器。

| Modifier and Type | Method and Description                                       |
| :---------------- | :----------------------------------------------------------- |
| `void`            | `viewResolver(ViewResolver viewResolver)`Register a [`ViewResolver`](https://docs.spring.io/spring/docs/5.1.8.RELEASE/javadoc-api/org/springframework/web/servlet/ViewResolver.html) bean instance. |

接收一个视图解析器的实例并将其注册。

| Modifier and Type | Method and Description                                       |
| :---------------- | :----------------------------------------------------------- |
| `void`            | `enableContentNegotiation(boolean useNotAcceptableStatus, View... defaultViews)`Enable use of a [`ContentNegotiatingViewResolver`](https://docs.spring.io/spring/docs/5.1.8.RELEASE/javadoc-api/org/springframework/web/servlet/view/ContentNegotiatingViewResolver.html) to front all other configured view resolvers and select among all selected Views based on media types requested by the client (e.g. |
| `void`            | `enableContentNegotiation(View... defaultViews)`Enable use of a [`ContentNegotiatingViewResolver`](https://docs.spring.io/spring/docs/5.1.8.RELEASE/javadoc-api/org/springframework/web/servlet/view/ContentNegotiatingViewResolver.html) to front all other configured view resolvers and select among all selected Views based on media types requested by the client (e.g. |

启用内容裁决视图解析器，该解析器不进行具体视图的解析，而是管理你注册的所有视图解析器，所有的视图会先经过它进行解析，然后由它来决定具体使用哪个解析器进行解析。



#### 2.2 配置请求拦截器

```java
public void addInterceptors(InterceptorRegistry registry);
```

拦截器只能拦截映射到控制器的请求，而无法拦截直接请求资源的请求。

#### 2.3 配置视图控制器

```java
public void addViewControllers(ViewControllerRegistry registry);
```

视图控制器能将直接请求页面的请求映射到某个视图，从而避免了专门新建控制器来跳转页面。

#### 2.4 配置资源处理器

```java
public void addResourceHandlers(ResourceHandlerRegistry registry);
```

资源处理器负责定义静态资源映射目录。