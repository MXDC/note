---
categories:
  - 23种设计模式
---
##### 依赖关系（Dependency）

![](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTcwMzAyMTkzMzIyMjUw?x-oss-process=image/format,png)

表示一个类对不在其实例作用域内的另一个类或对象的引用，通常有以下几种情况的依赖：

1. 局部变量
2. 方法的参数或返回值
3. 静态方法的调用

##### 关联关系(Association)

表示一个类持有另一个类或对象，是一种 “has-a” 关系，通常表现为：(Worker has Task)

1. 字段

2. 属性

   ![](https://img-blog.csdnimg.cn/20181127213855605.png)

##### 聚合关系



##### 组合关系(Composition)

组合是整体和部分的关系，但是整体和部分不可分离

```java
class Person{
    private Head head;//组合，不可分离
    private IDcard idCard; //聚合
}
```

##### 泛化

![](https://img-blog.csdnimg.cn/20181127213803746.png)

##### 实现

![](https://img-blog.csdnimg.cn/20181127213413278.png)

![](https://img-blog.csdnimg.cn/20181127213438552.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0lyb25fWWU=,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181127213455173.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0lyb25fWWU=,size_16,color_FFFFFF,t_70)