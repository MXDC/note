#### Maven多模块构建
`maven`具有继承和聚合的特性。继承特性能抽取各个模块相同的相同的依赖和插件配置等，能够简化pom，同一管理插件和依赖。聚合特性能将多个模块聚合在一起。
##### 聚合
用户可以在一个打包方式为`pom`的`maven`项目中使用任意数量的`<module>`元素来声明要聚合的项目。
```xml
<packaging>pom</packaging>
...
<modules>
    <module>module1</module>
    <module>module2</module>
<modules>
```

> 1. 对于聚合模块其打包方式必须为`pom`，否则无法构建。
> 2. module的值为当前`pom`文件的相对目录。

聚合项目和其他项目的关系并非一定是父子关系(树形结构)，也可以使用平行目录。如果使用平行目录对应pom也要修改,使其之下正确的模块目录。

```xml
<packaging>pom</packaging>
...
<modules>
    <module>../module1</module>
    <module>../module2</module>
<modules>
```

* 对应聚合模块，可以删除无关的文件，只需保留pom.xml 文件。

