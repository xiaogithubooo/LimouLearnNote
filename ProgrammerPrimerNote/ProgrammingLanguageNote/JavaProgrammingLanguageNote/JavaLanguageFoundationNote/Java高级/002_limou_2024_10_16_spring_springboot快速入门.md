# 1.Spring Boot 简介

`Spring Boot` 是一个构建在 `Spring` 框架顶部的项目（也就是一个模块）。它提供了一种简便，快捷的方式来设置，配置和运行基于 `Web` 的简单应用程序。

它是一个 `Spring` 模块，提供了 **RAD(快速应用程序开发)** 功能。它用于创建独立的基于 `Spring` 的应用程序，因为它需要最少的 `Spring` 配置，因此可以运行。

基本上 `Spring Boot = Spring Framework + Embedded HTTP Server - XML`

可以使用 `Spring STS IDE, Spring Initializr, IDEA Ultimate` 构建一个 `Spring Boot` 项目。

# 2.Spring Boot 功能

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

# 3.Spring Boot 创建

```