---
title: "@ComponentScan 小记"
date: 2023-12-08
categories:
  - Java
  - Spring
tags:
  - Java
  - Spring
summary: scan ？还是不 scan ？
series:
  - 开发记录
draft: false
---
**如果没有在启动类中添加 ComponentScan 指定扫描目录，Spring Boot 会默认扫描启动类所在包及其子包中的组件**
1. 如果 Spring 启动类（带有 `@SpringBootApplication` 注解的类）位于顶层包中，Spring Boot 将会扫描整个项目的所有组件。
   
2. 如果的组件类（包括 `Component`、`Controller`、`Service` 等）不在启动类所在的包及其子包下，而没有进行额外的配置，Spring Boot 可能无法正确地扫描到这些组件，从而导致应用无法正常运行。

**为了确保所有组件都被扫描到：**

1. 将启动类放置在项目的顶层包中。

2. 在启动类上使用 `@ComponentScan` 注解明确指定要扫描的包。例如：
```Java
@SpringBootApplication
@ComponentScan(basePackages = "com.example.yourpackage")
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

在 basePackages 中指定扫描的包路径。
将组件类放置在启动类所在包及其子包下。

### 起因：
增加了 `Controller` 但是请求不生效  
排查发现已有的 `@ComponentScan` 使用 `basePackages` 指定了一个具体文件夹(`xx.xxx.component`)，未包含 `Controller` 的文件夹  
将 `@ComponentScan` 直接移除解决（需结合实际处理）