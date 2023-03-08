---
title: "Using Spring With Lombok"
date: 2023-02-16T11:06:59+05:30
author: ["Siddhant Shaw"]
summary: Lombok can be easily integrated with Spring Framework to make your code more concise and readable. By using Lombok with Spring, developers can reduce the amount of code they need to write and focus on the core functionality of their application.
ShowShareButtons: true
ShowToc: true
editPost:
   URL: "https://github.com/Mianto/Mianto.github.io/tree/editpost/content"
   Text: "Suggest Changes" # edit text
   appendFilePath: true # to append file path to Edit link
comments: true
draft: false
---
## Inversion of Control & Dependency Injection

In software engineering, Inversion of Control is a principle that involves transferring the control of objects or parts of a program to a container or framework. This approach offers several benefits, such as decoupling the execution of a task from its implementation, simplifying the process of switching between implementations, increasing modularity, and facilitating testing through dependency mocking.

For example, let's say you have a car application. With Inversion of Control, you can separate the implementation of the engine from the main car application. This can be achieved using Dependency Injection, where the engine code is injected into the car application at runtime.

```Java
// TraditionalCar.java
import com.example.demo.Engine;
import com.example.demo.V6Engine;

public class TraditionalCar {
    private Engine engine;

    public TraditionalCar(Engine engine) {
        this.engine = new V6Engine();
    }
}
```

In this scenario, the car application is no longer responsible for the engine, and the engine code can be swapped out easily without affecting the rest of the application. This approach also makes it easier to test the car application by mocking the engine code during testing. Overall, Inversion of Control can greatly improve the flexibility, modularity, and testability of software systems.
```Java
// DICar.java
import com.example.demo.Engine;

public class DICar {
    private Engine engine;

    public DICar(Engine engine) {
        this.engine = engine;
    }
}
```

## Spring

Spring is a Java framework that utilizes Inversion of Control (IoC) and Dependency Injection (DI) to provide a flexible and modular way of building enterprise-grade applications. With IoC, Spring's container manages the creation and configuration of objects, reducing coupling between different components of the application. DI simplifies the configuration and management of an application by injecting dependencies into beans at runtime. These features make it easier to develop and maintain complex software systems, while also increasing code flexibility and testability. Overall, Spring with IoC and DI provide a powerful and widely used platform for building robust and scalable applications.

### Types of Dependency Injection

1. **Constructor Injection**:
In Constructor Injection, dependencies are provided to a class through its constructor. This is considered to be the most preferred type of DI, as it ensures that the dependencies are available when the object is created and makes them explicit. According to Spring Framework's documentation, "constructor injection is widely regarded as the most robust form of Dependency Injection". The benefit of Constructor Injection is that it makes the dependencies explicit, so you know exactly what is required to create an instance of a class.

```Java
//SpringCar.java
import com.example.demo.Engine;

public class SpringCar {
    private Engine engine;

    public SpringCar(Engine engine) {
        this.engine = engine;
    }
}
```
```Java
// AppConfig.java
import com.example.demo.springConstructor.SpringCar;
import com.example.demo.springConstructor.SpringV6Engine;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public SpringCar springCar() {
        return new SpringCar(engine());
    }

    @Bean Engine engine() {
        return new SpringV6Engine();
    }
}
```

2. **Setter Injection**:
In Setter Injection, dependencies are provided to a class through its setter methods. This type of DI is considered to be less preferred than Constructor Injection, as it allows the object to be created with incomplete dependencies.
```java
// SpringSetterCar.java
import com.example.demo.Engine;

public class SpringSetterCar {
    private Engine engine;

    public Engine getEngine() {
        return engine;
    }

    public void setEngine(Engine engine) {
        this.engine = engine;
    }
}
```
```java
// AppConfig.java
import com.example.demo.springSetter.SpringSetterCar;
import com.example.demo.springSetter.SpringSetterV8Engine;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public Engine springSetterEngine() {
        return new SpringSetterV8Engine();
    }

    @Bean
    public SpringSetterCar springSetterCar() {
        SpringSetterCar setterCar = new SpringSetterCar();
        setterCar.setEngine(springSetterEngine());
        return setterCar;
    }
}
```
3. **Field Level Injection**:
In Field Level Injection, dependencies are provided to a class directly through its fields. This type of DI is considered to be the least preferred type of DI, as it can lead to hard-to-debug issues and makes the dependencies less explicit.
```java
// SpringFieldCar.java
import com.example.demo.Engine;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class SpringFieldCar {
    @Autowired
    private Engine springFieldEngine;

    public Engine getSpringFieldEngine() {
        return springFieldEngine;
    }

    public void setSpringFieldEngine(Engine springFieldEngine) {
        this.springFieldEngine = springFieldEngine;
    }
}
```
Although Field Level Injection is the least preferred type of Dependency Injection, as it also relies on reflection to inject the dependencies into the fields, which can impact performance and introduce additional complexity, it is widely used due to being simpler implementation.

## Lombok

Lombok is a popular Java library that offers a set of annotations to reduce boilerplate code in your Java classes. By adding these annotations to your classes, you can automate many repetitive tasks and make your code more concise and readable.

1. **Getter and Setter Methods:**
Instead of manually writing getter and setter methods for your class fields, you can use the **@Getter** and **@Setter** annotations provided by Lombok.
```java
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class Person {
    private String firstName;
    private String lastName;
}
```
2. **Constructors**
You can use Lombok's **@NoArgsConstructor** and **@AllArgsConstructor** annotations to generate constructors for your classes.
```java
import lombok.AllArgsConstructor;
import lombok.NoArgsConstructor;

@NoArgsConstructor
@AllArgsConstructor
public class Person {
    private String firstName;
    private String lastName;
}
```
3. **toString & Equals and Hashcode Methods**
Instead of manually writing a toString() method for your class, you can use the **@ToString** and, **@EqualsAndHashCode** annotation provided by Lombok to generate *equals()* and *hashCode()* methods for your class.

```java
import lombok.EqualsAndHashCode;
import lombok.ToString;

@EqualsAndHashCode
@ToString
public class Person {
    private String firstName;
    private String lastName;
}
```
4. **Builder**
Another useful feature provided by Lombok is the ability to generate a builder class for your class using the **@Builder** annotation. This can simplify the process of creating complex objects with many optional parameters.
```java
import lombok.Builder;

@Builder
public class Person {
    private String firstName;
    private String lastName;
}
```

## Lombok with Spring
Lombok can be easily integrated with Spring Framework to make your code more concise and readable. Use Lombok annotations in your Spring components. For example, you can use the **@AllArgsConstructor** or **@RequiredArgsConstructor** annotation on a Spring bean class to generate a constructor with all arguments.

```java
// LombokSpringCar.java
import com.example.demo.Engine;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Component;


@Component
@RequiredArgsConstructor
public class LombokSpringCar {
    private final Engine engine;
}
```
### Advantages:
- *Use Constructor Injection concisely*: Using @RequiredArgsConstructor on a spring bean class, we can directly inject beans similar to field-level injection.
- *Reduces boilerplate code*: Lombok annotations such as @Data, @Getter, and @Setter help to reduce boilerplate code, which can make the codebase more concise and easier to read and maintain.
- *Improves developer productivity*: By generating common code, Lombok helps to reduce the amount of time developers need to spend writing repetitive code.
- *Enhances code readability*: Lombok can help to simplify code and make it easier to read and understand.

### Disadvantages:

- *Learning curve*: Developers need to learn the Lombok annotations and how to use them effectively, which can add some initial overhead to the development process.
- *Hidden complexity*: Lombok can add complexity to the codebase by generating code that is not immediately visible in the source code. This can make it harder to debug and troubleshoot issues that arise.
- *Limited IDE support*: Some IDEs may not fully support Lombok, which can make it harder to use effectively.

Overall, the decision to use Lombok with Spring will depend on the specific needs of the project and the preferences of the development team. While there are some potential advantages to using Lombok, there are also some potential drawbacks that need to be considered.

## Source Code

The code is hosted at [@github](https://github.com/Mianto/lombok-with-spring), please reach out for suggestions.

### References
- A quick intro to Dependency Injection: what it is, and when to use it: (https://www.freecodecamp.org/news/a-quick-intro-to-dependency-injection-what-it-is-and-when-to-use-it-7578c84fa88f/)
- Spring Framework Documentation: (https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-dependencies)
- Baeldung's Dependency Injection Guide: (https://www.baeldung.com/dependency-injection-types-java)
- Intro to Inversion of Control and Dependency Injection with Spring: (https://www.baeldung.com/inversion-control-and-dependency-injection-in-spring)
- Lombok Features: (https://projectlombok.org/features/)
