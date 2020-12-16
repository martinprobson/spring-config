
# Spring Boot - Under the Covers

## Summary
This repo shows three methods of spring boot configuration: -

### 1. @Configuration Annotation 

Configure a class with the `@Configuration` annotation
and define a `@Bean` method that gets the service: -

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration public class HelloServiceConfiguration {
    @Bean
    public HelloService getHelloService() {
        return new MyHelloService();
    }
}
```
The `@Configuration` annotation indicates that the class concerned contains one or more
methods annotated with `@Bean` which generate types that will be managed by the Spring Application Context.

The `@Bean` annotation tells Spring, "Here is an instance of this class (or interface), please keep hold of it and
give it back to me when I ask.

## 2. Annotate the Class directly with a @Service Annotation

```java
import org.springframework.stereotype.Service;

@Service
class MyHelloService implements HelloService {
  //...
}
```

MyHelloService will get injected whenever a HelloService is required, for example using constructor injection: -

```java
@Bean
public CommandLineRunner run(HelloService helloService) {
        return (args -> System.out.println(helloService.sayHello()));
        }
```

## 3. Use Auto Configuration Factory

The `@SpringBootApplication` annotation marks the main application class (and package). This annotation 
includes the annotation `@EnableAutoConfiguration` which instructs Spring to guess which beans you are likely to need based on
a scan of the classpath.

Auto-configuration classes are regular Spring `@Configuration` beans. They are located using the `SpringFactoriesLoader` mechanism, which 
looks for `META-INF/spring.factories` on the classpath. This file contains key/value pairs: -

```aidl
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.example.helloservicestarter.HelloServiceAutoConfiguration
```

```java
@Configuration
public class HelloServiceAutoConfiguration {

    @Bean
    public HelloService getHelloService() {
        return new MyOtherHelloService();
    }
}
```

The 'real' `spring.factories` configuration classes normally have `@ConditionalOnClass` and/or `@ConditionalOnMissingBean`, for example
the Jackson auto-configuration will not happen if there is no `ObjectMapper` class on the classpath.

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnClass(ObjectMapper.class)
public class JacksonAutoConfiguration 
```

