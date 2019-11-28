

# Singleton Pattern

## 动机（Motivation）

* 一个系统只能有一个窗口管理器或文件系统；一个系统只能有一个计时工具或ID（序号）生成器。
* 如何保证一个类只有一个实例并且这个实例易于被访问呢？
* **一个更好的解决办法是让类自身负责保存它的唯一实例。**这个类可以保证没有其他实例被创建，并且它可以提供一个访问该实例的方法。这就是单例模式的模式动机。

## 模式定义

单例模式(Singleton Pattern)：单例模式确保某一个类**只有一个实例**，而且**自行实例化**并向整个系统**提供这个实例**，这个类称为单例类，它提供全局访问的方法。

单例模式的要点有三个：一是某个类只能有一个实例；二是它必须自行创建这个实例；三是它必须自行向整个系统提供这个实例。 

## 单列模式种实现方式

1）饿汉式（静态常量）
2）饿汉式（静态代码块）
3）懒汉式（线程不安全）
4）懒汉式（线程安全，同步方法，）
5）懒汉式（线程安全，同步代码块，不能实现）
6）双重检查
7）静态内部类
8）枚举

1. **饿汉式(静态常量)应用实例**

   ```java
   class Singleton{
       // 1. 私有化构造方法
       private Singleton (){}
       // 2. 实例化
       private static final Singleton singleton = new Singleton()
       // 3. 对外提供一个公有的静态方法
       public static Singleton getInstance(){
           return singleton;
       }
   }
   ```

   优点：写法简单，类加载时就实例化，没有多线程同步问题。

   缺点：没有Lazy Loading效果，可能会浪费空间

2. **饿汉式(静态代码块)应用实例**

```java
class Singleton{
    // 1.私有化构造函数
    private Singleton(){}
    
    // 2. 实例化
    private static Singleton singleton;
    static {singleton = new Singletion() }
    
     // 3. 对外提供一个公有的静态方法
    public static Singleton getInstance(){
        return singleton;
    }
}
```

优缺点同上。

3. **懒汉式（线程不安全）**

   ```java
   class Singleton{
       // 1.私有化构造函数
       private Singleton(){}
       
       // 2. 实例化
       private static Singleton singleton;
       
        // 3. 对外提供一个公有的静态方法，用到时才实例化
       public static Singleton getInstance(){
   		if (singleton == null){
               singleton = new Singleton();
           }
           return singleton;
       }
   }
   ```

   单线程可以使用，多线程不安全（多个线程进入if代码块会生成多个实例），开发不要使用。

4.  **懒汉式（同步方法）**

   ```java
   class Singleton{
       // 1.私有化构造函数
       private Singleton(){}
       
       // 2. 实例化
       private static Singleton singleton;
       
        // 3. 对外提供一个公有的静态方法，用到时才实例化,
       public static synchronized Singleton getInstance(){
   		if (singleton == null){
               singleton = new Singleton();
            // 同步代码块
               // synchronized (Singleton.class){singleton = new Singleton();}
           }
           return singleton;
       }
   }
   ```

   方法进行同步效率低(后面获取实例都要进行同步)，

5.  双重检查

   ```java
   class Singleton{
       // 1.私有化构造函数
       private Singleton(){}
       
       // 2. 实例化
       private static volatile Singleton singleton;
       
        // 3. 对外提供一个公有的静态方法，用到时才实例化,
       public static synchronized Singleton getInstance(){
   		if (singleton == null){
               synchronized(Singleton.class){
                   if(singleton == null){
                       singleton = new Singleton();
                   }
               }
           }
           return singleton;
       }
   }
   ```

   推荐使用，实现懒加载。线程安全

6.  静态内部类

   ```java
   class Singleton{
       // 1.私有化构造函数
       private Singleton(){}
       // 外部类加载内部类不会加载，实现懒加载
     	private static class SingletonInstance(){
           private static final Singleton INSTANCE = new Singleton();
       }
       
        // 3. 对外提供一个公有的静态方法，用到时才实例化,
       public static  Singleton getInstance(){
           return SingletonInstance.INSTANCE;
       }
   }
   
   ```
   
7.  枚举

   ```java
   enum Singleton{
       INSTANCE;
   }
   ```

   可以防止反序列化，线程安全