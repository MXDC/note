#### Spring Boot源码安装

spring framework是基于gradle构建的。而spring boot是基于maven构建的

#### 在github上下载源码

点击branch可以选择分支

#### 编译源码

- 进入spring-boot-2.1.x源码根目录
- 执行mvn命令: `mvn clean install -DskipTests -Pfast` *//跳过测试用例*
- ./mvnw clean install -DskipTests -Pfast，这里会下载mavne，不会使用本地maven

> 最好先编译好在导入，不然idea会执行不必要的测试和命令

#### 新建一个module

将项目导入idea后新建一个maven模块

在`spring-boot-project`新建一个maven项目

pom文件如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
		 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-parent</artifactId>
		<version>${revision}</version>
		<relativePath>../spring-boot-parent</relativePath>
	</parent>
	<modelVersion>4.0.0</modelVersion>

	<artifactId>spring-boot-mxdc</artifactId>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
	</dependencies>
</project>
```

```java
package com.mxdc;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @author MXDC
 * @date 2019/10/28
 **/
@SpringBootApplication
public class MxdcApplication {
	public static void main(String[] args) {
		SpringApplication.run(MxdcApplication.class);
	}
}

```

