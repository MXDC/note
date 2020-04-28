#  Simple Factory

## 动机（Motivation）

![](https://design-patterns.readthedocs.io/zh_CN/latest/_images/SimpleFactory.jpg)



* 简单工厂模式包含三个角色：工厂角色负责实现创建所有实例的内部逻辑；抽象产品角色是所创建的所有对象的父类，负责描述所有实例所共有的公共接口；具体产品角色是创建目标，所有创建的对象都充当这个角色的某个具体类的实例

* 简单工厂又叫静态工厂

* 简单工厂模式的要点在于：当你需要什么，只需要传入一个正确的参数，就可以获取你所需要的对象，而无须知道其创建细节。（封装实例化代码）

  

  #  Factory Method

  ## 动机（Motivation）

  在工厂类定义一个抽象方法，将具体产品的创建过程交给专门的工厂子类去完成。

## 模式定义

工厂方法模式(Factory Method Pattern)又称为工厂模式，也叫虚拟构造器(Virtual Constructor)模式或者多态工厂(Polymorphic Factory)模式，它属于类创建型模式。在工厂方法模式中，工厂父类负责定义创建产品对象的公共接口，而工厂子类则负责生成具体的产品对象，这样做的目的是将产品类的实例化操作延迟到工厂子类中完成，即通过工厂子类来确定究竟应该实例化哪一个具体产品类

## 结构模式

工厂方法模式包含如下角色：

- Product：抽象产品
- ConcreteProduct：具体产品
- Factory：抽象工厂
- ConcreteFactory：具体工厂

 ![](https://api.onedrive.com/v1.0/shares/s!AnfzhZ6EzsFXgTbPGsGrUoYtNWUD/root/content)



## 实例应用

![ew](https://api.onedrive.com/v1.0/shares/s!AnfzhZ6EzsFXgTgU3thdrq7tiNTp/root/content)

* 使用工厂方法模式的另一个优点是在系统中加入新产品时，无须修改抽象工厂和抽象产品提供的接口，无须修改客户端，也无须修改其他的具体工厂和具体产品，而只要添加一个具体工厂和具体产品就可以了。这样，系统的可扩展性也就变得非常好，完全符合“开闭原则”。

## 抽象工厂

在很多软件系统中需要更换界面主题，要求界面中的按钮、文本框、背景色等一起发生改变时，可以使用抽象工厂模式进行设计。

![ss](https://api.onedrive.com/v1.0/shares/s!AnfzhZ6EzsFXgTQizNzUFvXA6PBc/root/content)

