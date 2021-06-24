# SpringMVC

ssm：mybatis

MVC是一种软件设计规范，（Model模型、View视图、Controller控制器的简写）

MVC不是一种设计模式，是一种架构模式

最典型的MVC就是JSP+servlet+javabean的模式

JSP:本质就是一个Servlet



![image-20210429152525981](SpringMVC.assets/image-20210429152525981.png)

**简要分析执行流程**

1. DispatcherServlet表示前置控制器，是整个SpringMVC的控制中心。用户发出请求，DispatcherServlet接收请求并拦截请求。

   我们假设请求的url为 : http://localhost:8080/SpringMVC/hello

   

   **如上url拆分成三部分：**

   http://localhost:8080服务器域名

   SpringMVC部署在服务器上的web站点

   hello表示控制器

   通过分析，如上url表示为：请求位于服务器localhost:8080上的SpringMVC站点的hello控制器。

2. HandlerMapping为处理器映射。DispatcherServlet调用HandlerMapping,HandlerMapping根据请求url查找Handler。

3. HandlerExecution表示具体的Handler,其主要作用是根据url查找控制器，如上url被查找控制器为：hello。

4. HandlerExecution将解析后的信息传递给DispatcherServlet,如解析控制器映射等。

5. HandlerAdapter表示处理器适配器，其按照特定的规则去执行Handler。

6. Handler让具体的Controller执行。

7. Controller将具体的执行信息返回给HandlerAdapter,如ModelAndView。

8. HandlerAdapter将视图逻辑名或模型传递给DispatcherServlet。

9. DispatcherServlet调用视图解析器(ViewResolver)来解析HandlerAdapter传递的逻辑视图名。

10. 视图解析器将解析的逻辑视图名传给DispatcherServlet。

11. DispatcherServlet根据视图解析器解析的视图结果，调用具体的视图。

12. 最终视图呈现给用户。

步骤：

1.在web.xml文件中配置DispatchServlet

![image-20210429155242000](SpringMVC.assets/image-20210429155242000.png)

2.在springmvc-servlet.xml里面配置内容

![image-20210429161104148](SpringMVC.assets/image-20210429161104148.png)

3.Controller(实现接口)

![image-20210429160917019](SpringMVC.assets/image-20210429160917019.png)

真实开发项目

1.在springmvc-servlet.xml

![image-20210429163313185](SpringMVC.assets/image-20210429163313185.png)

2.创建Controller

![image-20210429164939375](SpringMVC.assets/image-20210429164939375.png)

### RestFul风格

原来的方式：

![image-20210430095725856](SpringMVC.assets/image-20210430095725856.png)

1.在SpringMVC中使用@PathVariable注解，让方法参数的值绑定到一个URL模板变量上面

![image-20210430102039617](SpringMVC.assets/image-20210430102039617.png)

RestFul风格优点：高效，简洁，安全

SpringMVC的重定向和转发- **有视图解析器** 

狂神说笔记地址：https://mp.weixin.qq.com/s?__biz=Mzg2NTAzMTExNg%3D%3D&chksm=ce6104fdf9168deb32664243023d374b336f2a4260b55846b533a8be70d2f9bc97e45f4ede47&idx=1&mid=2247483998&scene=21&sn=97c417a2c1484d694c761a2ad27f217d#wechat_redirect

```java
@Controller
public class ResultSpringMVC2 {
   @RequestMapping("/rsm2/t1")
   public String test1(){
       //转发
       return "test";
  }

   @RequestMapping("/rsm2/t2")
   public String test2(){
       //重定向
       return "redirect:/index.jsp";
       //return "redirect:hello.do"; //hello.do为另一个请求/
  }
}
```

前端数据接收

1.个别参数

```
@RequestMapping("/hello")
public String hello(@RequestParam("username") String name, ModelMap model){
   //封装要显示到视图中的数据
   //相当于req.setAttribute("name",name);
   model.addAttribute("name",name);
   System.out.println(name);
   return "hello";
}
```

2.对象

```
@RequestMapping("/hello")
public String hello(User user){
   System.out.println(user);
   return "hello"；
}
```

前端访问地址url:localhost：8080/hello?name=2&age=12

### 乱码问题（过滤器解决）

@responseBody和@Controller=@RestController

web.xml

![image-20210430140223722](SpringMVC.assets/image-20210430140223722.png)

在web.xml中加入过滤器

```java
<filter>
   <filter-name>encoding</filter-name>
   <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
   <init-param>
       <param-name>encoding</param-name>
       <param-value>utf-8</param-value>
   </init-param>
</filter>
<filter-mapping>
   <filter-name>encoding</filter-name>
   <url-pattern>/*</url-pattern>
</filter-mapping>
```

## JSON

- 独立于文本
- 简介清晰的存储结构
- 对象表示键值对，数据由逗号分隔
- 花括号保存对象
- 方括号保存数组
- JavaScript都可以通过JSON来表示

![image-20210508143432418](SpringMVC.assets/image-20210508143432418.png)

JSON和JavaScript对象互转

![image-20210508142641343](SpringMVC.assets/image-20210508142641343.png)

### FastJson

![image-20210508144534506](SpringMVC.assets/image-20210508144534506.png)

结果

![image-20210508145039737](SpringMVC.assets/image-20210508145039737.png)

### Spring整个各类资源

配置数据库参数

![image-20210508155242359](SpringMVC.assets/image-20210508155242359.png)

Spring-dao.xml(Spring整个dao层)

![image-20210508164145780](SpringMVC.assets/image-20210508164145780.png)

Spring-service.xml(Spring整个service层)

![image-20210508154536885](SpringMVC.assets/image-20210508154536885.png)

Spring-mvc.xml

需要提前增加web项目，添加配置web.xml,如下

![image-20210508164736620](SpringMVC.assets/image-20210508164736620.png)

![image-20210508165123184](SpringMVC.assets/image-20210508165123184.png)

总的applicationContext.xml文件来管理spring-dao.xml,spring-mvc.xml,spring-service.xml

![image-20210508164532789](SpringMVC.assets/image-20210508164532789.png)

Controller层（调service层）

![image-20210508165916785](SpringMVC.assets/image-20210508165916785.png)

### Ajzx

异步无刷新

### 拦截器

拦截器只会拦截访问的控制器

拦截器是mvc框架自己带的,是AOP思想的实现

定义拦截器，实现HandlerInterceptor

拦截器配置-实现登录和验证功能

拦截器的配置，利用mvc标签

![image-20210509123037280](SpringMVC.assets/image-20210509123037280.png)

文件上传和下载过程













































