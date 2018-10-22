# 单例模式

> **单例模式**确保一个类只有一个实例，并提供一个全局访问点。

**单例模式用处：**
线程池（threadpool)、缓存（cache）、对话框、处理偏好设置、注册表的对象、日志对象、充当打印机、显卡设备的驱动程序的对象。

## 0x01 经典的单例模式（懒汉式，线程不安全）

Lazy初始化：是
多线程安全：否

```java
/**
 * 单线程单例模式，并发情况下不安全
 */
public class Singleton {
    // 私有构造函数
    private Singleton() {}
    // 单例对象
    private static Singleton instance = null;
    
    /**
     * 获取单例对象的方法
     * @return instance
     */
    public static Singleton getIntance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```
**不适合多线程环境，在多线程环境下可能会产生不止一个实例**
为什么这样写呢？我们来解释几个关键点：

1. 要想让一个类只能构建一个对象，自然不能让它随便去做new操作，因此Signleton的构造方法是私有的。
2. instance是Singleton类的静态成员，也是我们的单例对象。它的初始值可以写成Null，也可以写成new Singleton()。至于其中的区别后来会做解释。
3. getInstance是获取单例对象的方法。

如果单例初始值是`null`，还未构建，则构建单例对象并返回。这个写法属于单例模式当中的**懒汉模式**。
如果单例对象一开始就被`new Singleton()`主动构建，则不再需要判空操作，这种写法属于**饿汉模式**。

这两个名字很形象：饿汉主动找食物吃，懒汉躺在地上等着人喂。

## 0x02 处理多线程(synchronized)（懒汉式，线程安全）

Lazy初始化：是
多线程安全：是

```java
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
**单例模式只有第一次执行此方法时才需要同步，换句话说，一旦设置好instance变量，就不再需要同步这个方法。**

## 0x03 使用“急切”创建实例（饿汉式）

Lazy初始化：否
多线程安全：是

```java
public class Singleton {
    
    // 在静态初始化器中创建单例
    private static Singleton instance = new Singleton();
    private Singleton() {}
    
    public static Singleton getInstance() {
        return instance;
    }
}
```
**依赖JVM在加载这个类时马上创建此类唯一的单例实现。JVM保证在任何线程访问instance静态变量之前，一定先创建此实例**

## 0x04 使用双重检查加锁

在`getInstance`中减少使用同步。利用双重检查加锁（double-checked locking），首先检查实例是否已经创建了，如果尚未创建，才进行同步。这样以来，只有第一次会同步。
```java
public class Singleton {

    // 双重加锁检查
    private volatile static Singleton instance;
    private Singleton() {}
    
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized(Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

## 0x05 登记式/静态内部类

Lazy初始化：是
多线程安全：是
这种方式能达到双检锁方式一样的功效，但实现更简单。对静态域使用延迟初始化，应使用这种方式而不是双检锁方式。这种方式只适用于静态域的情况，双检锁方式可在实例域需要延迟初始化时使用。
```java
public class Singleton {
    private static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }
    private Singleton (){}

    public static final Singleton getInstance() {
        return SingletonHolder.INSTANCE;
    }
}
```

## 0x06 枚举

这种实现方式还没有被广泛采用，但这是实现单例模式的最佳方法。它更简洁，自动支持序列化机制，绝对防止多次实例化。
```java
public enum Singleton {
    INSTANCE;
    public void whateverMethod() {
    }
}
```

[单例模式的七种写法](https://blog.csdn.net/itachi85/article/details/50510124)

2018-08-19 15:28:50 星期日