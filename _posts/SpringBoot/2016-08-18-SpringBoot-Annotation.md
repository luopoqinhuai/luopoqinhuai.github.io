---
layout: post
comments: true
categories: SpringBoot
---



### 在Spring Boot中几乎可以完全弃用xml配置文件，在这里总结下常用的注解。

> Spring最开始是为了解决EJB等大型企业框架对应用程序的侵入性  
> 因此大量依靠配置文件来“非侵入式”得给POJO增加功能  
> 从Spring 3.x开始，Spring被外界最为诟病的一点就是配置繁多，号称“配置地狱”
 
从Spring 4.x开始，Spring.io提供了三种方式编织Bean：

* 利用注解：隐式配置，例如：@Autowired、@Bean、@Component等，通过注解来简化xml文件。
* 利用Java文件：显示配置，比xml配置的优势是具备类型安全
* 利用传统的xml配置文件


# 注解(annotations)列表

### @ResponseBody 
> 用该注解修饰的函数，会将结果直接填充到HTTP的响应体中，一般用于构建RESTful的api；

### @Controller 
> 用于定义控制器类，在spring 项目中由控制器负责将用户发来的URL请求转发到对应的服务接口（service层） 

一般情况下@Controller与@RequestMapping一起使用，用于指定URL对应的处理方法或者处理类

	@Controller
	public class Index {
    	@RequestMapping(value ="/", method = RequestMethod.GET)
    	@ResponseBody
    	public String hello(){
    	    return "hello world";
    	}
	}

### @RestController 
> 与Controller类似，使用这个注解表明是REST接口  



### @ResponseBody  
> 

### @RequestMapping 
> 提供路由信息，负责URL到Controller中的具体函数的映射。

### @EnableAutoConfiguration 
> Spring Boot自动配置（auto-configuration）：尝试根据你添加的jar依赖自动配置你的Spring应用。例如，如果你的classpath下存在HSQLDB，并且你没有手动配置任何数据库连接beans，那么我们将自动配置一个内存型（in-memory）数据库”。你可以将@EnableAutoConfiguration或者@SpringBootApplication注解添加到一个@Configuration类上来选择自动配置。如果发现应用了你不想要的特定自动配置类，你可以使用@EnableAutoConfiguration注解的排除属性来禁用它们。


### @ComponentScan 
> 表示将该类自动发现（扫描）并注册为Bean，可以自动收集所有的Spring组件，包括@Configuration类。我们经常使用@ComponentScan注解搜索beans，并结合@Autowired注解导入。


### @Configuration 
> 相当于传统的xml配置文件，如果有些第三方库需要用到xml文件，建议仍然通过@Configuration类作为项目的配置主类——可以使用@ImportResource注解加载xml配置文件。


### @SpringBootApplication 
> 相当于@EnableAutoConfiguration、@ComponentScan和@Configuration的合集。


### @Import 
> 用来导入其他配置类。


### @ImportResource 
> 用来加载xml配置文件。


### @Autowired 
> 自动导入依赖的bean


### @Service 
> 一般用于修饰service层的组件


### @Repository 
> 使用@Repository注解可以确保DAO或者repositories提供异常转译，这个注解修饰的DAO或者repositories类会被ComponetScan发现并配置，同时也不需要为它们提供XML配置项。