# CHAPTER 01 - GETTING STARTED WITH SPRING

## Definition

Spring offers a **container** that:
  1. Wires the **components** by using pattern Dependency Injection (DI)
  2. Manages (creates, injects, etc) the **components**


* container = Spring Application Context
* components = beans
* DI = Component B requires Component A functionality to work, but we want to NOT Depend on A. So, instead by using Interfaces we inject A into B. A can be changed to AA with ease.
* Spring will bootstrap (initialize, update, download, etc), or manage mundane activities keeping the project build always coherent in regard to its dependencies and components.

### Wiring
  Wiring is made by using constructor arguments or by using property access methods. Then DI is utilized to complete the wiring.

  1. component scanning: Spring first discover components to become Beans.
  2. autowiring: Spring then does DI in those Beans.

Spring Boot does improve more by utilizing **autoconfiguration** which does most work automatically.

## XML Configuration Vs Java Based Configuration

Spring classic way of setting up the wiring for the application context is by using XML by:

1. Describing the beans
2. Setting the wiring

```xml

<!-- Declaring 2 Beans -->
<bean id="classA" class="com.example.ClassA"/>

<bean id="classB" class="com.example.ClassB">
    <!-- Injecting A into B -->
    <constructor-arg ref="classA"/>
</bean>

```

Spring can now use Java-based configurations too:

```java

@Configuration

public class ExampleConfiguration {
    @Bean
    public ClassA classA() {
        return new ClassA();
    }

    @Bean
    public ClassB classB() {
        return new ClassB(classA());
    }
}

```

When automatic wiring is not possible, it must be explicity by using XML or Java.

## Spring Initializr

Utilizes an instance of `http://start.spring.io` for building a Java startup folder structure for a project type (can be overriden to local instance) using Maven or Gradle.x. Note that  `jar` is preferred to `war` packaging because of cloud platforms (Cloud Foundry). Utilize `war` only when destination is a traditional Java App Server


Initial Spring project folder organization:
* `src/main/java` App source code
* `src/test/java` App Test code
* `src/main/resources` Anything non-Java

Initial Spring content (often):
* `MyAppNameApplication.java` Spring boot app that initializes/bootstraps everything (Main Spring Class)
* `MyAppNameApplicationTests.java` Tests entry point
* `application.properties` Configuration properties (initially empty)
* `pom.xml` Maven main project
* `static` folder for CSS, JS, images.
* `mvnw` and/or `mvnw.cmd` for building project when maven is absent.

## App Starting Point (Bootstrapping)
`@SpringBootApplication` works most of the time with additional annotations:

* `@SpringBootConfiguration`: is specialized version of `@Configuration` which allows to configure beans. 
* `@EnableAutoConfiguration`: Automatically configures components. TODO more info.
* `@ComponentScan`: Enables `@Component`, `@Controller`, `@Service`, etc to be discovered and wired.


```java
// = = = = INITIAL BOOTSTRAPPING POINT = = = =
// Every Spring App has one
package tacos;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

// Annotation sets the App as an Spring App
@SpringBootApplication
public class TacoCloudApplication {

	public static void main(String[] args) {
	    // Sets the application to run, which is the current one which
	    // will bootstrap the components and wiring.
		SpringApplication.run(TacoCloudApplication.class, args);
	}

}

// = = = = INITIAL TESTING POINT = = = =

package com.example.spring5example;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

// Annotations add capabilities for doing Test on Spring with implicit JUnit
// SpringRunner = SpringJUnit4ClassRunner (previous versions)
@RunWith(SpringRunner.class)
@SpringBootTest
public class Spring5ExampleApplicationTests {

    // Annotation marking the test
	@Test
	public void contextLoads() {
	}
}

```

# WEB APPLICATION INTRO

Spring MVC is a web framework part of Spring Ecosystem.

* View: often utilizing a template instead of JSP defines the visuals of a web page
* Controller: Handles/Answers request of the View is the main component of MVC


## Controller
Once a controller is identifyed, it becomes a Bean.

```java
package tacos;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

// Annotation identifying a Controller
@Controller
public class HomeController {

    // Controller handling/answering a request HTTP/GET
    @GetMapping("/")
    public String home() {
        // Calling the View
        return "home";
    }
    
}

```

## View
Thymeleaf and FreeMarker are widely used. Apache Velocity is no longer supported in Spring.

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
<head>
<title>Taco Cloud</title>
</head>
<body>
	<h1>Welcome to...</h1>
	<img th:src="@{/images/TacoCloud.png}" />
</body>
</html>
```


## MVC Testing

```java
package tacos;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;

import static org.hamcrest.Matchers.containsString;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.view;

// Annotation for requesting to use a Spring MVC test
@RunWith(SpringRunner.class)
@WebMvcTest(HomeController.class)
public class HomeControllerTest {

    // MVC component needed for testing will be injected. 
    @Autowired
    private MockMvc mockMvc;
    
    @Test
    public void testHomePage() throws Exception {
        mockMvc.perform(get("/"))                       // Does a GET
                .andExpect(status().isOk())             // Expects HTTP 200
                .andExpect(view().name("home"))         // Expects view "home" answering
                .andExpect(content().string(            // Expects content having "Welcome to"
                        containsString("Welcome to")));        
    }s    
}

```

# SPRING DEVTOOLS

* Automatic App restart when code change, except when dependency changes.
* Automatic Browser refresh when livereload.con plugin installed in browser.
* H2 console if H2 Database utilized.

# QUICK REVIEW OF SPRING MAIN COMPONENTS

* MVC Spring: Web framework that can also create REST APIs.
* Spring Boot: Autoconfiguration and dependency manager that includes an Actuator for metrics.
* Spring Data: Defines how data will be stored/retrieved from DBs, JPA, Mongo, etc.
* Spring Security: Authentication, API security.
* Spring Integration & Spring Batch: Real time and batch data processing integration.
* Spring Cloud: Microservices.