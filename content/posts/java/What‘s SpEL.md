---
title: "什么是 SpEL"
date: 2022-12-15
categories:
  - Java
tags:
  - Java
  - SpEL
summary: SpEL 的全称是：Spring Expression Language，即 Spring 表达式语言，它支持在运行时对对象图进行（object graph）查询和操作，以及方法调用和字符串模板功能。
---
### 起因
最近工作中读到一些奇怪的字符串，例如：

`#addDays(#max(item.dayA, item.dayB), 1);`

从字面上能看出来它像是一些自定义的函数和变量访问

这些字符串存储在数据库中，在代码中使用 `SpelExpressionParser` 解析并执行

### [什么是 SpEL？](https://docs.spring.io/spring-framework/docs/3.0.x/reference/expressions.html) 

SpEL 的全称是：Spring Expression Language，即 Spring 表达式语言，它支持在运行时对对象图进行（[object graph](https://en.wikipedia.org/wiki/Object_graph)）查询和操作，以及方法调用和字符串模板功能。
	
	
### SpEL 的应用场景？
在 XML 使用表达式设置系统变量或构造函数
```XML
<property name="randomNumber" value="#{ T(java.lang.Math).random() * 100.0 }"/>
<property name="defaultLocale" value="#{ systemProperties['user.region'] }"/>
```

在 `@Value` 注解中读取系统属性和环境变量
```Java
@Value( "#{ systemProperties['user.region'] }" )
private String defaultLocale;
```

调用 Java 类的静态方法
```Java
Class dateClass = parser.parseExpression("T(java.util.Date)").getValue(Class.class);

Class stringClass = parser.parseExpression("T(String)").getValue(Class.class);

boolean trueValue = 
   parser.parseExpression("T(java.math.RoundingMode).CEILING < T(java.math.RoundingMode).FLOOR")
  .getValue(Boolean.class);
```
解析对象属性值
``` Java
String city = (String) parser.parseExpression("placeOfBirth.City").getValue(context);
```

注册和调用自定义函数
```java
// 先定义一个反转字符串方法
public abstract class StringUtils {
  public static String reverseString(String input) {
    StringBuilder backwards = new StringBuilder();
    for (int i = 0; i < input.length(); i++) 
      backwards.append(input.charAt(input.length() - 1 - i));
    }
    return backwards.toString();
  }
}
```

```java
// 在代码中进行方法注册和调用
ExpressionParser parser = new SpelExpressionParser();
StandardEvaluationContext context = new StandardEvaluationContext();
// registerFunction 会将 reverseString 方法注册到 context
context.registerFunction("reverseString", 
                         StringUtils.class.getDeclaredMethod("reverseString", 
                                                             new Class[] { String.class }));
String helloWorldReversed = parser.parseExpression("#reverseString('hello')").getValue(context, String.class);
```

还有更多功能并未罗列全，可以参考结尾的参考文献。

### 总结
SpEL 适合运行时场景，以字符串表达对对象图的访问。

代码中应该避免写太冗长复杂的 SpEL 表达式或用 SpEL 来实现太复杂的业务逻辑， 这会降低代码的可读性并增加维护难度。

#### 参考文献：

1. [Spring Expression Language (SpEL)](https://docs.spring.io/spring-framework/docs/3.0.x/reference/expressions.html) 
2. [深入掌握Spring SpEL及其实战应用 - 作者：bug绝缘体](https://zhuanlan.zhihu.com/p/149920813)
3. [Spring Expression Language Guide - 作者：baeldung](https://www.baeldung.com/spring-expression-language)

