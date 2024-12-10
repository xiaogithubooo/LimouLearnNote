# 快速入门

> [!WARNING]
>
> 注意：不过我的习惯是先体验原生的生成器来配置，然后再来体验 `IDE` 这些便携工具，这种知晓一切的感觉很舒服。因此本教程对于没有下载 `IDEA` 的新手来说是可以无痛运行的（当然，您需要安装 `Java`），因此本教程使用在 `Linux` 环境下使用，请您最好在 `Linux` 操作系统中运行。

## 1.Spring Boot 简介

`Spring Boot` 是一个构建在 `Spring` 框架顶部的项目（也就是一个模块）。它提供了一种简便，快捷的方式来设置，配置和运行基于 `Web` 的简单应用程序。

它是一个 `Spring` 模块，提供了 **RAD(快速应用程序开发)** 功能。它用于创建独立的基于 `Spring` 的应用程序，因为它需要最少的 `Spring` 配置，因此可以运行。

基本上 `Spring Boot = Spring Framework + Embedded HTTP Server - XML`

可以使用 `Spring STS IDE, Spring Initializr, IDEA Ultimate` 构建一个 `Spring Boot` 项目。

## 2.Spring Boot 功能

- **Web 开发** 这是用于 `Web` 应用程序开发的非常适合的子模块。我们可以轻松创建一个独立的 `HTTP` 应用程序，该应用程序使用 `Tomca, Jetty, Undertow` 等嵌入式服务器。我们可以使用  `spring-boot-starter-web` 模块快速启动和运行应用程序。

- **SpringApplication 类** 提供了一种方便的方式来引导 `Spring` 应用程序，可以从 `main` 方法开始，我们可以仅通过调用静态 `run()` 方法来调用应用程序。

  ```java
  // Main.java
  public static void main(String[] args) {
      SpringApplication.run(ClassName.class, args);  
  }
  ```

- **应用程序事件和侦听器** `Spring Boot` 使用事件来处理各种任务。它允许我们创建用于添加侦听器的工厂文件。我们可以使用  `ApplicationListener 键` 来引用它。总是在 `META-INF` 文件夹中创建工厂文件，例如  `META-INF/spring.factories`。

- **应用管理** `Spring Boot` 提供了为应用程序启用与管理员相关的功能的功能。它用于远程访问和管理应用程序。我们可以使用 `spring.application.admin.enabled` 属性在 `Spring Boot` 应用程序中启用它。

- **外部配置** `Spring Boot` 允许我们外部化我们的配置，以便我们可以在不同环境中使用同一应用程序。该应用程序使用 `YAML` 文件来外部化配置。

- **属性文件** `Spring Boot` 提供了一组丰富的应用程序属性。因此，我们可以在项目的属性文件中使用它。该属性文件用于设置诸如  `server-port = 8082` 等属性，它有助于组织应用程序属性。

- **YAML 支持** 它提供了一种方便的方法来指定层次结构。它是 `JSON` 的超集（任何 `JSON` 文档复制进去都可以通过使用）。 `SpringApplication` 类自动支持 `YAML`，它是属性文件的代替方法。

- **类型安全配置** 强大的类型安全配置用于管理和验证应用程序的配置。应用程序配置始终是至关重要的任务，应该是类型安全的。我们还可以使用此库提供的注释。

- **日志** `Spring Boot` 对所有内部记录都使用通用记录。默认情况下管理日志记录依赖项。如果不需要自定义，我们不应更改日志记录依赖项。

- **安全性** `Spring Boot` 应用程序是 `spring` 的 `Web` 应用程序。因此，默认情况下，通过所有 `HTTP` 端点上的基本身份验证，它是安全的。可以使用一组丰富的端点来开发安全的 `Spring Boot` 应用程序。

## 3.Spring Boot 创建

### 3.1.基本概念

`Spring Boot` 是简化版的 `Spring`，旨在快速开发中小型的、快速交付、微服务架构的项目，要比 `Spring` 来得轻量。

### 3.2.构建项目

下面我们将依据 [官方快速入门文档](https://spring.io/quickstart) 尝试构建一个 `Spring boot` 项目。

![image-20241209170750346](./assets/image-20241209170750346.png)

1. 我们将打开浏览器访问 [spring initializr](https://start.spring.io/) 来快速生成一个 `Spring boot` 项目开发脚手架
2. 项目构建工具选择 `Maven`
3. 项目语言选择 `Java`，这也是主要的工业开发语言
4. 选择官方推荐的 `Spring Boot 3.4.0` 版本
5. 创建 `web` 项目，在 `Dependencies(依赖项)` 对话框中，搜索并添加`Spring Web` 依赖项
6. 填写元数据 **项目公司域名组、项目包名、项目名、项目描述、项目包名**
7. 选择项目包类型
8. 选择语言版本，这里我选择 `Java 17`，请注意在您的 `Linux` 里使用 `sudo apt install -y openjdk-17-jd` 下载 `Java 17`，避免版本不匹配
9. 点击 “生成” 按钮，下载 `zip` 文件，然后将其解压缩到计算机上的文件夹中
10. 此时浏览器可以从远端下载到您配置好的 `Spring boot` 项目脚手架代码

### 3.3.目录结构

下面就是一个 `Spring boot` 生成的目录结构，相关注释我都写在里面了。

```Shell
# 查看项目目录结构
$ tree demo
demo
├── HELP.md # 项目相关的帮助文档或使用指南
├── mvnw # 在 Linux 或 macOS 上执行的脚本(如果开发环境中没有安装 Maven, mvnw 可以自动下载并使用指定版本的 Maven)
├── mvnw.cmd #  在 Windows 上执行的脚本
├── pom.xml
└── src # 项目源代码
    ├── main # 主要代码
    │   ├── java
    │   │   └── com
    │   │       └── example
    │   │           └── demo
    │   │               └── DemoApplication.java # 程序入口文件
    │   └── resources
    │       ├── application.properties
    │       ├── static
    │       └── templates
    └── test # 测试代码
        └── java
            └── com
                └── example
                    └── demo
                        └── DemoApplicationTests.java # 测试程序入口文件

15 directories, 7 files

```

> [!IMPORTANT]
>
> 补充：`Maven` 有什么用呢？主要是 `Java` 项目的构建工具，类似 `C++` 中的 `CMake` 工具，但是使用起来会稍微简单一些，可以简化和自动化软件的构建、测试、部署、依赖流程。

### 3.4.文件解说

> [!WARNING]
>
> 注意：您可以先跳过这里，先看快速入门，再来看这里的文件解说。

### 3.5.快速入门

我们将依据 [官方快速入门文档](https://spring.io/quickstart) 在 `Spring boot` 项目运行打印出 `Hello World`。

在 `src/main/java/com/example/demo` 文件夹中找到 `DemoApplication.java` 文件，初始内容如下，我给出了部分注释。

```Java
// DemoApplication.java
package com.example.demo; // 反域名包名, 声明当前类所在的包

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @SpringBootApplication 复合注解
 * = @SpringBootConfiguration 标识这是一个 Spring Boot 应用程序的配置类
 * + @EnableAutoConfiguration 启用 Spring Boot 的自动配置机制, 根据依赖和类路径内容自动配置应用程序, 避免手动配置大量的 Spring 配置类
 * + @ComponentScan 启用组件扫描, 默认会扫描当前包及其子包中的所有类(如 @Controller, @Service, @Repository, @Component 等注解标记的类)
 */
@SpringBootApplication
public class DemoApplication { // 入口类

    public static void main(String[] args) { // 入口方法
        SpringApplication.run(DemoApplication.class, args); // 启动 Spring boot
    }

}
```

接下来的编码很简单，我们只需要让 `Spring boot` 项目中添加我们自己的代码，然后运行起来就可以了。

```Java
// DemoApplication.java
package com.example.demo;

// 这里的 import 引入了关于 Spring Boot 和 Spring Web 必要的类和注解
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@RestController // 控制器注解, 表示该类是一个 RESTful 控制器, 返回的是对象本身, 并自动将其序列化成 JSON 格式返回给客户端, 而不是返回视图
public class DemoApplication {

    public static void main(String[] args) {
      SpringApplication.run(DemoApplication.class, args);
    }

    @GetMapping("/hello") // 这是一个 Spring Web 注解, 用于将 HTTP GET 请求映射到 hello() 上, 路径为 '/hello'
    public String hello(@RequestParam(value = "name", defaultValue = "World") String name) {
      return String.format("Hello %s!", name);
    }

}
```

> [!IMPORTANT]
>
> 补充：补充一些基础知识，如果您缺少这些知识，请一定要进行补全。
>
> 1. **RESTful 控制器：**`REST(Representational State Transfer)`是一种基于 `HTTP` 协议的通信方式，主要用于客户端和服务器之间的通信，通常用于提供 **Web API**。`RESTful API` 遵循一系列的设计原则，通常会使用 `HTTP` 动作（如 `GET、POST、PUT、DELETE` 等）来进行数据的操作。控制器注解可以让类中经过映射的方法接收 `HTTP` 请求并返回响应。
> 2. **返回对象本身，而非视图：**传统的 `Spring MVC` 控制器（使用 `@Controller` 注解）通常会返回一个视图名（例如 `JSP` 标签或 `Thymeleaf` 模板），`Spring` 会将数据与视图模板结合并返回给客户端。不过这种开发方式已经有些过时，是不可能完全做到前后端分离开发的，和早期的 `C++ Web` 开发是差不多的，存在很多开发困难。但是，**RESTful 控制器** 的作用是提供一个 **Web API**，可以直接返回 **数据**（如 `JSON` 或 `XML`），而不是视图。在 `@RestController` 中，返回的对象会被自动序列化为 `JSON` 格式（默认使用 **Jackson** 序列化库来将 Java 对象转换成 JSON 格式），直接作为 `HTTP` 响应返回给客户端。

该怎么在命令行中启动我们的 `Spring boot` 项目就像运行一个普通 `Java` 程序一样呢？首先需要使用 `mvnw` 安装需要的依赖（我们得到的仅仅是带有脚本的脚手架和一些基本的文件，不包含 `Spring boot` 的源代码），然后才能让我们的 `Spring boot` 项目生成 `Java` 的字节码，进而

```shell
# 命令行原生启动 Spring boot
# 进入项目
$ cd demo
$ pwd
/home/ljp/test/java/demo

# 安装依赖
$ ./mvnw clean install # 哪怕是有稳定的网络都需要安装很久, 请耐心等待...

# 运行项目
$ ./mvnw spring-boot:run

# 打包项目
# $ ./mvnw package # 会在 target/ 中打包好 jar 包

# 前台部署
# java -jar target/demo-0.0.1-SNAPSHOT.jar

```

在本地机器的浏览器 `URL` 中直接访问 `http://127.0.0.1:8080/hello`（或者携带参数的 `http://127.0.0.1:8080/hello?name=limou`），就可以在浏览器页面中渲染出 `Hello World!`（`Hello limou!`），其中直接运行项目适合进行开发测试，打包适合直接部署到服务器上运行。

> [!IMPORTANT]
>
> 补充：为什么返回的字符串后不会被渲染为 `JSON` 字符串？
>
> - `Spring` 框架如果检测到返回值是一个字符串，会将它直接作为 `HTTP` 响应体（`text/plain` 或 `text/html`）。
> - 如果返回的内容是一个复杂对象，例如 `Map` 或自定义类实例，`Spring` 才会自动将其序列化为 `JSON` 格式。

> [!IMPORTANT]
>
> 补充：当然仅仅是这样是不够的，为什么，因为这样仅仅是写好了一个后端接口而已，`cpp-http` 可以做到、`python-flask、python-django` 可以做到、`node.js-koa.js` 可以做到，为什么偏偏工业偏爱使用 `java-spring boot` 呢？因此您需要了解更多关于 `Spring boot` 的配置内容。