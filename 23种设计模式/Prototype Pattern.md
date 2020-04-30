---
categories:
  - 23种设计模式
---
# 原型模式（Prototype Pattern）

## 动机（Motivation）

克隆羊问题

## 应用

spring 中bena的scope为prototype

### 浅拷贝

clone是浅拷贝

### 深拷贝

* 重写clone方法，对引用类型单独处理

* 序列化，工具类的对象必须要实现Serializable接口，否则是没有办法实现克隆的。

* 使用第三方jar包转化为json数据，在转为对象

* Apache Commons Lang序列化

* ```java
   //写入字节流
  ByteArrayOutputStream out = new ByteArrayOutputStream();
  ObjectOutputStream obs = new   ObjectOutputStream(out);
  obs.writeObject(obj);
  obs.close();
  
  //分配内存，写入原始对象，生成新对象
   ByteArrayInputStream ios = new  ByteArrayInputStream(out.toByteArray());
   ObjectInputStream ois = new ObjectInputStream(ios);
  //返回生成的新对象
  cloneObj = (T) ois.readObject();
  ois.close();
  ```

  

  