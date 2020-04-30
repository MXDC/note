---
categories:
  - spring源码解析
---
#### spring framework下载源码

spirng 5.0问题多多搞了一半天没整明天，建议使用5.1和gradle 4.10.3版本，不然问题多多瞎折腾。

在github上搜索spirng 找到 spring framework项目，在分支了可以选择版本。这里下载5.x

##### 可以在spring.gradle中修改maven镜像

```groovy
//     maven { url "http://maven.aliyun.com/nexus/content/groups/public/"}
```

#### idea编译

![](https://raw.githubusercontent.com/MXDC/images_bed/master/img/1571971155393.jpg)

使用工具编译时，可能会出现内存溢出情况，这里我们编译时需要增加相关参数；
-XX:MaxPermSize=2048m -Xmx2048m -XX:MaxHeapSize=2048m

> 如果出现错误，修改build.gradle中的相同提示信息中所要求的版本。或注释掉相关内容。

构建完成之后，如此依次编译spring-core ,spring-oxm，spring-context，spring-bean即可。

#### 解决编译错误问题一：cglib和objenesis jar包的缺失

- 问题原因：为了避免第三方class的冲突，Spring把最新的cglib和objenesis给重新打包（repack）了。它并没有在源码里提供这部分的代码，而是直接将其放在jar包当中，这也就导致了我们拉取代码后出现编译错误。
- 问题解决：

这是因该会出问题包spring-core中的cglib包找不到的问题，解决办法：执行这2个任务

other->cglibRepackJar

other->objenesisRepackJar

![](https://img2018.cnblogs.com/blog/1016598/201907/1016598-20190706183725521-1159082514.png)
作者：木兮vtalk链接：https://juejin.im/post/5d7662a2f265da03af19fabe来源：掘金著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

编译过程中会出现提示错误信息，我们忽略这个信息

#### AspectJ出错





