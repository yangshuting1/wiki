## Singleton Pattern

### 定义

通过单例模式可以保证系统中一个类只有一个实例。并提供一个访问它的全局访问点


### 应用场景

线程池，缓存，对话框，处理偏好设置和注册表的对象，日志对象，充当打印机，显卡等设备的驱动程序的对象。

### 具体实现

有三种实现方式：懒汉式，饿汉式和双重锁

#### 懒汉式

优点：不会有两个线程同时进入，线程安全

缺点：只有第一次执行时，才需要同步。之后调用会产生额外负担，性能变差

```java

public class Singleton1 {


    //私有的构造方法，外部无法通过new
    private Singleton1(){}

    //定义个静态，私有属性
    private static Singleton1 instance;

    //增加synchronized关键字，迫使整个线程在进到这个方法之前，要等到其他线程全部离开。（不会有两个线程同时进入）
    
    public static synchronized Singleton1 getInstance(){
        if(instance==null){
            instance = new Singleton1();
        }
        return instance;
    }
}


```

#### 饿汉式

急切需要创建实例时，不用延迟实例化

```java

public class Singleton2 {

    private Singleton2(){}

    //在静态初始化时创建对象，保证了线程安全
    private static Singleton2 instance2= new Singleton2();

    //供外部访问class的静态方法
    public static Singleton2 getInstance2(){
    
        return instance2;
    }
}

```


#### 双重锁


性能最优

**首先检查是否实例已经创建，如果未创建，才同步。这样一来，只有第一次才同步。**

```java
public class Singleton3 {
    private Singleton3() {}

    //每次都小心地重新读取这个变量的值，而不是使用保存在寄存器里的备份
    private volatile static Singleton3 instance3 ;

    public static Singleton3 getInstance3() {
        //检查实例，如果不存在，就进入同步区
        if (instance3 == null) {
            //只有第一次会彻底执行。
            //线程获得的是对象锁,,别的线程在该类所有对象上的任何操作都不能进行.
            synchronized (Singleton3.class) {
                if (instance3 == null) {
                    instance3 = new Singleton3();
                }

            }
        }
        return instance3;
    }

}

```


主要优点：

1. 提供了对唯一实例的受控访问。
2. 由于在系统内存中只存在一个对象，因此可以节约系统资源，对于一些需要频繁创建和销毁的对象单例模式无疑可以提高系统的性能。
3. 允许可变数目的实例。
 
主要缺点：

1. 由于单利模式中没有抽象层，因此单例类的扩展有很大的困难。
2. 单例类的职责过重，在一定程度上违背了“单一职责原则”。
3. 滥用单例将带来一些负面问题，如为了节省资源将数据库连接池对象设计为的单例类，可能会导致共享连接池对象的程序过多而出现连接池溢出；如果实例化的对象长时间不被利用，系统会认为是垃圾而被回收，这将导致对象状态的丢失





