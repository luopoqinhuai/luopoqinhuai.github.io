---
layout: post
comments: true
categories: java
---

# 类加载器 ClassLoader

Java 中的类加载器大致可以分成两类，一类是系统提供的，另外一类则是由 Java 应用开发人员编写的。 

## 引导类加载器（bootstrap class loader）：

它用来加载 Java 的核心库(jre/lib/rt.jar)，是用原生C++代码来实现的，并不继承自java.lang.ClassLoader。

加载扩展类和应用程序类加载器，并指定他们的父类加载器，在java中获取不到。 

## 扩展类加载器（extensions class loader）：

它用来加载 Java 的扩展库(jre/ext/*.jar)。Java 虚拟机的实现会提供一个扩展库目录。该类加载器在此目录里面查找并加载 Java 类。 

## 系统类加载器（system class loader）：

它根据 Java 应用的类路径（CLASSPATH）来加载 Java 类。一般来说，Java 应用的类都是由它来完成加载的。可以通过 ClassLoader.getSystemClassLoader()来获取它。

## 自定义类加载器（custom class loader）：

除了系统提供的类加载器以外，开发人员可以通过继承 java.lang.ClassLoader类的方式实现自己的类加载器，以满足一些特殊的需求。

---
# Class.fromName的用法：
Class.fromName("com.xxx.xxx") 调用后返回一个类，其作用就是让JVM查找并加载这个类；在实际使用中一般是为了使得该类中的**静态代码段**被执行。

这个知识点是在使用jdbc初始化时遇到的点。
	
	try {  
           Class.forName(name);//指定连接类型  
           conn = DriverManager.getConnection(url, user, password);//获取连接  
           pst = conn.prepareStatement(sql);//准备执行语句  
        }catch (Exception e) {  
            e.printStackTrace();  
        }  

