---
title: BeanFactory VS FactoryBean
date: 2023-12-20
categories:
  - Java
tags:
  - Java
summary: 豆子工厂和工厂豆子？
series:
  - 面试拾遗
draft: false
---
在 Spring 框架中，BeanFactory 和 FactoryBean 是俩重要的概念，它们在 Spring 容器中负责管理和创建 Bean。虽然它们都涉及 Bean 的创建，但它们有不同的角色和职责。

## BeanFactory

BeanFactory 是 Spring 框架中最基本的容器，负责管理和装配 Bean。它提供了一种松散耦合的方式，通过IoC（控制反转）和DI（依赖注入）来管理应用程序中的组件。

BeanFactory 示例：

```Java
import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class BeanFactoryExample implements CommandLineRunner {

    @Autowired
    private MyBean myBean;

    public static void main(String[] args) {
        SpringApplication.run(BeanFactoryExample.class, args);
    }

    @Override
    public void run(String... args) {
        myBean.doSomething();
    }
}


```
在上述代码中，`@SpringBootApplication`注解标记了 Spring Boot 应用程序的入口点，并且通过`@Autowired`注解注入了`MyBean`。  

FactoryBean 与 BeanFactory 不同，FactoryBean 是一个接口，它允许开发人员自定义 Bean 的实例化过程。通过实现 FactoryBean 接口，开发人员可以控制 Bean 的创建逻辑，灵活定制 Bean 的初始化过程。

FactoryBean 示例：
```Java
import org.springframework.beans.factory.FactoryBean;
import org.springframework.stereotype.Component;

@Component
public class MyFactoryBean implements FactoryBean<MyBean> {

    @Override
    public MyBean getObject() throws Exception {
        MyBean myBean = new MyBean();
        // 进行自定义的初始化逻辑
        myBean.setSomeProperty("自定义初始化");
        return myBean;
    }

    @Override
    public Class<?> getObjectType() {
        return MyBean.class;
    }

    @Override
    public boolean isSingleton() {
        return true;
    }
}

```
在上述代码中，`MyFactoryBean`类使用了`@Component`注解，将其作为一个Spring Bean 进行管理，并通过实现`FactoryBean`接口来控制`MyBean`的创建过程。
## 结合实际使用场景说明二者差别
### 场景描述

假设我们正在构建一个电商平台，其中商品信息来自外部 API，并且我们希望在系统启动时将商品信息加载到 Spring 容器中。在这种情况下，我们可以使用 BeanFactory 来管理商品服务。
### 使用BeanFactory的情况

如果我们使用 BeanFactory，我们会简单地配置一个 Bean 定义来加载商品信息：
```Java
import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    public void displayProductInfo() {
        Product product = productRepository.getProduct();
        System.out.println("Product Information: " + product.getName() + ", " + product.getPrice());
    }
}
```
`ProductService`负责展示商品信息，而`ProductRepository`可能在内部调用外部API获取商品信息。在这个简单的场景中，使用 BeanFactory 足以管理这些服务的生命周期和依赖关系。
### 使用FactoryBean的情况

现在，假设我们的商品对象需要在创建时执行一些特殊的初始化逻辑，例如根据不同的条件设置一些额外的属性。这时，我们可以使用 FactoryBean 来实现更高级别的控制。
```Java
import org.springframework.beans.factory.FactoryBean;
import org.springframework.stereotype.Component;

@Component
public class ProductFactoryBean implements FactoryBean<Product> {

    @Override
    public Product getObject() throws Exception {
        // 从外部API获取商品信息
        ExternalApiProductInfo externalApiProductInfo = ExternalApi.getProductInfo();

        // 创建Product对象
        Product product = new Product();
        product.setName(externalApiProductInfo.getName());
        product.setPrice(externalApiProductInfo.getPrice());

        // 执行特殊的初始化逻辑
        if (externalApiProductInfo.isFeatured()) {
            product.setSpecialOffer("Buy now and get a discount!");
        }

        return product;
    }

    @Override
    public Class<?> getObjectType() {
        return Product.class;
    }

    @Override
    public boolean isSingleton() {
        return true;
    }
}

```
在这个例子中，`ProductFactoryBean`控制了商品对象的创建过程，并允许在初始化时执行特殊逻辑。
## 对比总结

- **BeanFactory**：适用于简单的场景，其中对象的创建和初始化过程相对直接，无需复杂的逻辑。
    
- **FactoryBean**：适用于需要更高级别控制和自定义初始化逻辑的场景，尤其是当对象创建过程较为复杂或需要额外处理时。

# 参考
[What is the difference between BeanFactory and FactoryBean in Spring?](https://stackoverflow.com/questions/77596988/what-is-the-difference-between-beanfactory-and-factorybean-in-spring)
[Difference between BeanFactory and FactoryBean in Spring](https://www.springcloud.io/post/2023-07/bean-factory/#gsc.tab=0)