---
title: Create a simple spring boot application using gradle
date: 2020-06-08
description: Gradle is a nice tool to create a java application. You don't need to use a IDE software in order to create a simple hello world program.
comments: true
tags:
  - spring-boot
  - gradle
  - java
---


To create a simple spring boot application as a classic Hello World!", you don't need to use an IDE for it. Luckily, Gradle website explain to you how to do this via a [simple tutorial]. I will try to sumarize it.

## Getting Started

Initialize your project:

```
gradle init --type java-application
```

It generates these files, pay attention to the directory structure.

```
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

```

Modify the `build.gradle` file adding the plugin and dependencies block

<script src="https://gist.github.com/jenciso/dcfbcc301e173d86afbd128e7b8d13c1.js"></script>

Creating a "Hello World" sample application

```shell
mkdir src/{main,test}/java/hello
```

Into `src/main/java/hello` dir add two files: `App.java` and `HelloGradleController.java`

<script src="https://gist.github.com/jenciso/3ddb83aa76ac337310b0a9253ccd273e.js"></script>

And within the `src/test/java/hello` create this file:

AppTest.java

<script src="https://gist.github.com/jenciso/ae8af51e37e62f46703d5944f59c9c10.js"></script>

Define the main class name for the spring boot jar file. In your `build.gradle` file add this block

```java
application {
   mainClassName = 'hello.App'
}

bootJar {
    mainClassName = 'hello.App'
}
```

At this point, you could build and run the application

```
./gradlew bootJar
```
Or using the jar file
```
java -jar ./build/libs/spring-hello-world.jar
```
Another way to run the Application is by executing the following Gradle command:
```
./gradlew bootRun
```

Sample:

[![asciicast](https://asciinema.org/a/dWeIbajqy3an00eRqXfpuqslW.svg)](https://asciinema.org/a/dWeIbajqy3an00eRqXfpuqslW)

[simple tutorial]: https://guides.gradle.org/building-spring-boot-2-projects-with-gradle/#creating_a_hello_gradle_sample_application

