---
title: Create a simple spring boot application using gradle
date: 2020-06-08
description: Gradle is a nice tool to create a java application. You don't need to use a IDE software in order to create a simple hello world program.
tags:
  - spring-boot
  - gradle
  - java
---

## Intro

If you want to create a simple spring boot application likes a hello-world program, you don't need to use a IDE software for it. Gradle website has a simple tutorial that inspire me to writte this post.

## Steps

Initialize your project and choose the default options:

```shell
gradle init --type java-application
```

The generated project will has the following structure

```shell
$ tree spring-hello-world/
spring-hello-world/
├── build.gradle
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
├── settings.gradle
└── src
    ├── main
    │   ├── java
    │   │   └── spring
    │   │       └── hello
    │   │           └── world
    │   │               └── App.java
    │   └── resources
    └── test
        ├── java
        │   └── spring
        │       └── hello
        │           └── world
        │               └── AppTest.java
        └── resources

15 directories, 8 files
```

You need to modify the `build.gradle` file to use the spring boot plugins needed.

```java
plugins {
    // Apply the java plugin to add support for Java
    id 'java'

    // Apply the application plugin to add support for building a CLI application.
    id 'application'

    // Spring Boot plugins
    id 'org.springframework.boot' version '2.0.5.RELEASE'
    id 'io.spring.dependency-management' version '1.0.7.RELEASE'
}
```

Define the dependencies

```java
dependencies {
    implementation 'org.springframework.boot:spring-boot-dependencies:2.0.5.RELEASE'
    implementation 'org.springframework.boot:spring-boot-starter-web'

    testImplementation 'org.springframework.boot:spring-boot-starter-test'

    components {
        withModule('org.springframework:spring-beans') {
            allVariants {
                withDependencyConstraints {
                    // Need to patch constraints because snakeyaml is an optional dependency
                    it.findAll { it.name == 'snakeyaml' }.each { it.version { strictly '1.19' } }
                }
            }
        }
    }
}
```

Creating a "Hello World" sample application

```shell
mkdir src/{main,test}/java/hello
```

Within `src/main/java/hello` directory, create these two files:

App.java

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class App {

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }

}
```

HelloGradleController.java

```java
package hello;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController("/")
public class HelloGradleController {

    @GetMapping
    public String helloGradle() {
        return "Hello Gradle!";
    }

}
```

And within the `src/test/java/hello` create this file:

AppTest.java
```java
package hello;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@RunWith(SpringRunner.class)
@SpringBootTest(classes = App.class)
@AutoConfigureMockMvc
public class AppTest {

    @Autowired
    private MockMvc mvc;

    @Test
    public void helloGradle() throws Exception {
        mvc.perform(get("/"))
            .andExpect(status().isOk())
            .andExpect(content().string("Hello Gradle!"));
    }

}
```

Define the main class name for the spring boot jar file. In your `build.gradle` file add this block

```java
application {
   mainClassName = 'hello.App'
}

bootJar {
    mainClassName = 'hello.App'
}
```

At this point, you could build and run the spring boot application

```shell
./gradlew bootJar
```
Or using the jar file
```shell
java -jar ./build/libs/spring-hello-world.jar
```
Another way to run the Application is by executing the following Gradle command:
```shell
./gradlew bootRun
```

Sample:

[![asciicast](https://asciinema.org/a/dWeIbajqy3an00eRqXfpuqslW.svg)](https://asciinema.org/a/dWeIbajqy3an00eRqXfpuqslW)

## References

https://guides.gradle.org/building-spring-boot-2-projects-with-gradle/#creating_a_hello_gradle_sample_application

