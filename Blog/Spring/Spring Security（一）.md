# Spring Security（一）

### 一、Spring Security作用

Spring Security从两个角度来解决安全性问题：一是使用Servlet规范中的Filter保护Web请求并限制URL级别的访问；二是使用Spring AOP保护方法调用，即借助于对象代理和使用通知来确保只有具备适当权限的用户才能访问安全保护的方法。



### 二、Spring Security入门

#### 2.1 注册springSecurityFilterChain

Spring Security在保护Web应用时是基于Filter进行工作的，因此要使用它需要在web.xml中引入一个特殊的Filter，如下：

```xml
<filter>
	<filter-name>springSecurityFilterChain</filter-name>
    <filter-class>
    	org.springframework.web.filter.DelegatingFilterProxy
    </filter-class>
</filter>
```

通过`DelegatingFilterProxy`把`springSecurityFilterChain`这个bean注册入Spring容器中，该bean将把工作委托给具体的Spring容器中的Filter实现类，由具体的Filter实现类来完成工作。

如果不想使用web.xml来注册`springSecurityFilterChain`也可以使用Java代码的方式进行配置：

```java
import org.springframework.security.web.context.*;

public class SecurityWebApplicationInitializer
    extends AbstractSecurityWebApplicationInitializer {
    
}
```

`AbstractSecurityWebApplicationInitializer`实现了`WebApplicationInitializer`,因此Spring会发现继承了`AbstractSecurityWebApplicationInitializer`的类，并用它在web容器中注册`springSecurityFilterChain`这个bean。



#### 2.2 编写Spring Security配置

在将springSecurityFilterChain注册完毕后，就可以着手编写Spring Security的配置类。配置类如下：

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
}
```

@EnableWebSecurity可以启用任意Web应用的安全性功能，但Spring Security的配置类必须是实现了`WebSecurityConfigurer`的bean，为了方便起见我们可以选择继承`WebSecurityConfigurerAdapter` 。在完成Spring Security的配置类后，我们可以在`AbstractAnnotationConfigDispatcherServletInitializer`的`getRootConfigClasses（）`方法中将其引入应用上下文中，也可以在RootConfig中使用@Import注解将其导入。

```java
public class MvcWebApplicationInitializer extends
        AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[] { SecurityConfig.class };
    }

    // ... other overrides ...
}
```

具体的配置方法是在配置类内部覆盖configure()方法，configure()有三个重载版本：



|                  方法                   | 描述                          |
| :-------------------------------------: | ----------------------------- |
|         configure(WebSecurity)          | 配置Spring Security的Filter链 |
|         configure(HttpSecurity)         | 配置如何通过拦截器保护请求    |
| configure(AuthenticationManagerBuilder) | 配置用户存储服务              |

如果不进行覆盖的话将会有如下的默认配置：

```java
protected void configure(HttpSecurity http) throws Exception {
    http
        .authorizeRequests()          //对请求认证进行配置
            .anyRequest().authenticated()  //设置任何HTTP请求都必须经过认证
            .and()
        .formLogin()                   //提供一个默认的登陆页面
            .and()
        .httpBasic();					//提供HTTP Basic认证方式
}
```

