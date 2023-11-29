---
title: Java 方法会改变参数值吗？
date: 2023-11-29
categories:
  - Java
tags:
  - Java
summary: Java 方法传递过去的值会不会被改变？这得看具体情况。
---
### 先说总结
在 Java 中，方法传递的是值，但具体对值进行的操作是否会影响原始对象取决于传递的是原始类型还是引用类型。

1. **原始类型：** 当传递的是原始类型时（如`int`、`char`等），方法内对参数的修改不会影响原始值，因为传递的是值的副本。
    
2. **引用类型：** 当传递的是引用类型时（如对象、数组等），方法内对引用的修改可能会影响原始对象，但这取决于引用类型的可变性。
    
    - **可变引用类型：** 如果引用的对象是可变的，方法内的修改可能会影响原始对象。例如，`StringBuilder`、`ArrayList`等。
    - **不可变引用类型：** 如果引用的对象是不可变的，方法内的修改不会影响原始对象。例如，`String`、`Integer`等。

判断引用类型是否可变的方法包括查看文档、API方法以及源代码，以确定对象的可变性。了解这些概念有助于编写清晰、可维护的代码。
### 起因
阅读 [geeksforgeeks 文章](https://www.geeksforgeeks.org/need-of-wrapper-classes-in-java/?ref=lbp)时，关于原始类型和包装类型，在描述使用包装类型的原因时，其中有这么一段说明：
1. They convert primitive data types into objects. Objects are needed if we wish to modify the arguments passed into a method (because primitive types are passed by value).
翻译即：它们将原始数据类型转换为对象。<u>如果我们希望修改传递给方法的参数（因为原始类型是按值传递的），则需要使用对象。</u>

我随即代码验证：
```Java
public class PrimitiveVsReference {  
    public static void main(String[] args) {  
        // 原始类型  
        int intValue = 1;  
        System.out.println(intValue);  
        add(intValue);  
        System.out.println(intValue);  
        // 包装类型  
        Integer integerValue = 1;  
        System.out.println(integerValue);  
        add(integerValue);  
        System.out.println(integerValue);  
    }  
  
    private static void add(Integer integer) {  
        integer = integer + 1;  
    }  
}
```

然而输出如下：
```Java
1
1
1
1
```
### 为什么 `integerValue` 未改变
**先明确 Java 方法参数传递原则：**

* Java 方法传递的是值。而不是实际参数本身。这被称为按值传递。
  通俗讲：你把一张写有某个信息的纸片给了别人，实际上传递的是纸片上信息的拷贝，而不是原始纸片。（按纸传递？？？）
*  当传递的是原始类型（比如 `int`、`char`、`boolean`等），方法内对参数的修改不会影响到原始值。
  就好比你给别人一份复印件，他们修改了复印件，但原始文件并没有改变。
* 如果你传递的是引用类型（比如类的实例、数组等），方法内对引用的修改**可能**会影响到原始对象。
  这就像你把一个在线文档的 URL 给了别人，他们点开 URL 修改了文档，那么你再打开 URL 查看文档时，文档已被他人修改。

为何 `integerValue` 是引用类型但未被修改。因为 `Integer` 是不可变的，任何对它的修改都会创建一个新的对象。因此对其修改时实际会新建一个 Integer 对象，而不是修改其原始值。
### 接着问
**方法内对引用的修改可能会影响到原始对象，如何界定这个可能呢？**
在Java中，对于引用类型，方法内的修改是否会影响原始对象，取决于该引用类型是可变还是不可变。

1. **可变引用类型（Mutable Reference Types）：** 如果引用的对象是可变的（例如，`StringBuilder`、`ArrayList`等），那么方法内的修改可能会影响原始对象。这是因为可变对象的状态可以被改变。
   下面代码中，`modifyReference` 方法内部的修改不会修改原始对象。
```Java
    public static void main(String[] args) {
        StringBuilder originalReference = new StringBuilder("Hello");
        System.out.println("Before: " + originalReference);
        modifyReference(originalReference);
        System.out.println("After: " + originalReference);
    }

    private static void modifyReference(StringBuilder reference) {
        reference.append(" World");
        System.out.println("Inside method: " + reference);
    }
```
	
2. **不可变引用类型（Immutable Reference Types）：** 如果引用的对象是不可变的（例如，`String`、`Integer`等），那么方法内的修改不会影响原始对象。不可变对象的状态一旦被创建就不能被改变，任何修改都会创建新的对象。
   下面代码中，`modifyReference` 方法内部的修改不会修改原始对象。
```Java
    public static void main(String[] args) {
        String originalReference = "Hello";
        System.out.println("Before: " + originalReference);
        modifyReference(originalReference);
        System.out.println("After: " + originalReference);
    }

    private static void modifyReference(String reference) {
        reference = reference + " World";
        System.out.println("Inside method: " + reference);
    }
```  

### 如何判断引用类型能否被修改？

1. **不可变类型（Immutable Types）：**
    - 如果类型是不可变的，意味着其对象一旦被创建就不能被修改。不可变类型的对象的状态是固定的，任何看似修改对象的操作实际上都是在创建新的对象。
    - 例如，`String`、`Integer`、`BigDecimal`等是不可变类型。
2. **可变类型（Mutable Types）：**
    - 如果类型是可变的，意味着其对象的状态可以被改变。对于可变类型的对象，方法内的操作可能会修改对象的状态。
    - 例如，`StringBuilder`、`ArrayList`、`HashMap`等是可变类型。

在代码中，通常可以通过查看类型的文档或查阅相关资料来确定一个引用类型是可变还是不可变。以下是一些常见的方法来判断引用类型的可变性：
1. **查看文档：** 阅读 Java 官方文档或相关库的文档，了解对象的可变性。文档通常会明确说明对象是否是不可变的。
2. **查看API方法：** 如果类型提供了一些不会修改对象状态的方法，那么可能是不可变的。例如，`String` 提供的方法都是不会修改原始字符串的。

#### 参考文献：

1. [Need of Wrapper Classes in Java](https://www.geeksforgeeks.org/need-of-wrapper-classes-in-java/?ref=lbp)
