---
title: 单例模式
date: 2023-12-11
summary: 问就是枚举
series:
  - 设计模式
  - 面试拾遗
draft: false
---
# 先说怎么写：

用枚举：
```Java
public enum Singleton {
    INSTANCE;
    // 其他属性和方法等...
}

// 获取单例
Singleton singleton = Singleton.INSTANCE;
```

# 为什么用枚举？

看看一些其他的 Java 单例写法：
### 1. 饿汉式（Eager Initialization）：

```Java
public class Singleton {
    private static final Singleton instance = new Singleton();
    private Singleton() {}
    public static Singleton getInstance() {
        return instance;
    }
}
```
**特点：**
- 线程安全：在类加载时就创建实例，保证了线程安全。
- 构造方法私有化，防止其他类调用
- getInstance() 为暴露出的唯一获取实例方法

**缺点：**
- 类加载时就创建实例，可能造成资源浪费，尤其是在实例很大且不一定会被使用的情况下。

### 2. 懒汉式（Lazy Initialization）：

javaCopy code

```Java
public class Singleton {
    private static Singleton instance;
    private Singleton() {}
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}

```

**特点：**
- 延迟实例化，避免了资源浪费。
- 通过 synchronized 同步了示例获取方法，线程安全
  
**缺点：**
- 每次获取实例都经过了同步方法，性能不太好

### 3. 双重检查锁（Double-Checked Locking）：
```Java
public class Singleton {
    private static volatile Singleton instance;
    private Singleton() {}
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```
**特点：**

- 通过 volatile 保障多线程下单例可见性
- getInstance() 中的判空操作使同步块只会执行一次

**缺点：**
- 代码复杂

### 4. 静态内部类（Static Inner Class）：
```Java
public class Singleton {
    private static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }

    private Singleton() {}

    public static Singleton getInstance() {
        return SingletonHolder.INSTANCE;
    }
}
```

  
**特点：**

1. 懒加载：静态内部类只有在被使用时才会被加载，因此在类加载时不会初始化单例实例。
2. 线程安全且无锁：由于类加载过程是线程安全的，JVM 中的 ClassLoader 负责加载类的任务。ClassLoader 在加载类的过程中会使用**互斥锁**，确保同一时刻只有一个线程能够加载同一个类。这就保证了在多线程环境中类只会被加载一次。

# 综合来看
单例面临哪几个问题？
1. 类实例化的时机，用时候才创建，不用的时候不执行实例化
2. 线程安全，避免出现多个线程同时创建出实例
3. 反序列化创建实例
4. 反射调用构造方法创建实例
   
## 枚举是如何解决这些问题
### 1. 懒加载：

枚举实现的单例不是懒加载的，因为枚举常量在类加载时就被初始化。但它简洁。
### 2. 线程安全：

枚举实现的单例是天然线程安全的。在 Java 中，枚举的实例在类加载过程中被初始化，由JVM 保证线程安全。因此，枚举单例不需要额外的同步措施。

### 3. 反序列化：

枚举实现的单例天然地防范了反序列化攻击。枚举类型在序列化和反序列化过程中，Java会根据枚举常量的名称来识别实例。由于枚举实例在类加载时就被初始化，所以在反序列化时只会返回已经存在的枚举常量，不会创建新的实例。

### 4. 反射：

枚举实现的单例天然地防范了反射攻击。由于枚举的构造方法是私有的，并且无法通过反射调用私有构造方法，所以尝试通过反射创建新的实例会失败。因此，枚举实例是安全的，无法通过反射破坏其单例特性。

# 总结：

如果实例创建开销非常大，选择静态内部类实现。
反之，枚举是不二之选。