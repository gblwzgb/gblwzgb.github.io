---
title: Maven插件开发(1)
date: 2017-04-01 11:34:47
categories: Maven
---

学习Maven插件的编写，有助于理解Maven的生命周期，goal，-D，-P参数等。

以一个Hello,World的例子来入门Maven插件的开发吧。

<!-- more -->

Maven插件本身也是Maven项目，所以我们先搭一个Maven项目的骨架出来。
 
**先创建一个pom.xml文件**
```xml
 <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>sample.plugin</groupId>
    <artifactId>hello-maven-plugin</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>maven-plugin</packaging>

    <name>Sample Maven Plugin</name>

    <dependencies>
        <dependency>
            <groupId>org.apache.maven</groupId>
            <artifactId>maven-plugin-api</artifactId>
            <version>3.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.maven.plugin-tools</groupId>
            <artifactId>maven-plugin-annotations</artifactId>
            <version>3.4</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>

</project>
```
指定这个sample项目的`groupId`，`artifactId`，`version`，`packaging`。加入该sample项目所需要依赖的jar包。
 
这里要注意的是，写一个Maven插件，`packaging`必须指定为`maven-plugin`。
 
`artifactId`最好是指定为`xxxx-maven-plugin`或者是`maven-xxxx-plugin`。这对后面执行这个插件有好处。

**编写Java代码**
```java
package sample.plugin;

import org.apache.maven.plugin.AbstractMojo;
import org.apache.maven.plugin.MojoExecutionException;
import org.apache.maven.plugin.MojoFailureException;
import org.apache.maven.plugins.annotations.Mojo;

/**
 * Says "Hi" to the user.
 */
@Mojo(name = "sayhi")
public class GreetingMojo extends AbstractMojo {

    public void execute() throws MojoExecutionException, MojoFailureException {
        getLog().info("Hello,world.");
    }

}
```
- 抽象类`AbstractMojo`实现了接口`Mojo`和`ContextEnabled`，除了`execute`方法要我们自己实现外，其他的都可以直接使用。
- `@Mojo`注解是必须要的，否则install插件的时候会失败。该注解用来控制在什么时候以何种方式来执行这个mojo。
- `execute`方法可以抛出两个异常
    - `MojoExecutionException`，指的是非预期的异常。
    - `MojoFailureException`，指的是预期的异常。

**安装插件**

在pom.xml目录下，执行`mvn clean install`命令。

看到`BUILD SUCCESS`后，就可以在本地仓库里看到自己写的Maven插件了。

**使用插件**

我们在pom.xml里依赖一下新写的插件
```
<build>
    <plugins>
        <plugin>
            <groupId>sample.plugin</groupId>
            <artifactId>hello-maven-plugin</artifactId>
            <version>1.0.0-SNAPSHOT</version>
        </plugin>
    </plugins>
</build>
```
依旧还是在pom.xml目录下，按下面的规则执行插件

`mvn groupId:artifactId:version:goal`

在刚写的sample项目里就是

`mvn sample.plugin:hello-maven-plugin:1.0.0-SNAPSHOT:sayhi`

可以看到屏幕上输出了`Hello,world.`

是不是执行命令太长，感觉这么写很烦，可以简写成`mvn hello:sayhi`。前提是你要遵循插件的`artifactId`指定为`xxxx-maven-plugin`或者是`maven-xxxx-plugin`。



### 结语
本篇通过一个简单的demo来入门Maven插件，下次再写一些更实际的例子。

[参考资料](http://maven.apache.org/guides/plugin/guide-java-plugin-development.html)
