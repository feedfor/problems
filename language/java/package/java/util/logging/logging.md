版本：
<br>
JDK：1.7.0_80 <br>
tomcat：7.0.72 <br>
Hibernate：3.3.0.GA <br>
slf4j-api：1.5.2 <br>
slf4j-jdk14：1.5.2
<br><br>
问题：
<br>
```
Logger logger = LogManager.getLogManager().getLogger(Logger.GLOBAL_LOGGER_NAME);
```
在 IDE 内运行工程时，全局 Logger 可以获取到（不为 null）；将工程发布到 tomcat 下运行时，这个全局 Logger 就获取不到了，如果调用了全局 Logger 的其他成员，则会抛出运行时异常 NullPointerException，导致客户端调用失败。
<br><br>
Apache Tomcat 对 java.util.logging API 的几个关键元素进行了自己的实现，此实现被称为“JULI”。特别是其中的关键组件 LogManager。这个应该是影响到了 JDK 类库中的类 java.util.logging.LogManager。从以上代码可以看到，对全局 Logger 的存在造成了影响。
<br><br>
Tomcat 官方文档原文部分摘要：
```
Apache Tomcat has its own implementation of several key elements of java.util.logging API. This implementation is called "JULI". The key component there is a custom LogManager implementation, that is aware of different web applications running on Tomcat (and their different class loaders). It supports private per-application logging configurations. It is also notified by Tomcat when a web application is unloaded from memory, so that the references to its classes can be cleared, preventing memory leaks. 

This java.util.logging implementation is enabled by providing certain system properties when starting Java. The Apache Tomcat startup scripts do this for you, but if you are using different tools to run Tomcat (such as jsvc, or running Tomcat from within an IDE), you should take care of them by yourself. 
```
<br>
链接：https://tomcat.apache.org/tomcat-7.0-doc/logging.html