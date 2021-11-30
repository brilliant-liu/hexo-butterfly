---
title: Maven
date: 2021-11-25 11:10:26
tags:
  - Maven
categories:
  - MAVEN
cover: https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fpic2.zhimg.com%2Fv2-94a00bfec307801902f81d81140a1c8a_1200x500.jpg&refer=http%3A%2F%2Fpic2.zhimg.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg

---

## 



## 什么是依赖冲突
依赖冲突是指项目依赖的某一个jar包，有多个不同的版本，因而造成类包版本冲突

## 依赖冲突的原因
依赖冲突很经常是类包之间的间接依赖引起的。每个显式声明的类包都会依赖于一些其它的隐式类包，这些隐式的类包会被maven间接引入进来，从而造成类包冲突。



##  如何解决依赖冲突
首先查看产生依赖冲突的类jar，其次找出我们不想要的依赖类jar，手工将其排除在外就可以了。具体执行步骤如下

###  通过dependency:tree是命令来检查版本冲突 

>  mvn -Dverbose dependency:tree

当敲入上述命令时，控制台会出现形如下内容

``` tex
[INFO] org.example:hello:jar:1.0-SNAPSHOT
[INFO] +- org.springframework:spring-context:jar:5.2.7.RELEASE:compile
[INFO] |  +- (org.springframework:spring-aop:jar:5.2.7.RELEASE:compile - omitted for conflict with 5.2.0.RELEASE)
[INFO] |  +- org.springframework:spring-beans:jar:5.2.7.RELEASE:compile
[INFO] |  |  \- (org.springframework:spring-core:jar:5.2.7.RELEASE:compile - omitted for duplicate)
[INFO] |  +- org.springframework:spring-core:jar:5.2.7.RELEASE:compile
[INFO] |  |  \- org.springframework:spring-jcl:jar:5.2.7.RELEASE:compile
[INFO] |  \- org.springframework:spring-expression:jar:5.2.7.RELEASE:compile
[INFO] |     \- (org.springframework:spring-core:jar:5.2.7.RELEASE:compile - omitted for duplicate)
[INFO] \- org.springframework:spring-aop:jar:5.2.0.RELEASE:compile
[INFO]    +- (org.springframework:spring-beans:jar:5.2.0.RELEASE:compile - omitted for conflict with 5.2.7.RELEASE)
[INFO]    \- (org.springframework:spring-core:jar:5.2.0.RELEASE:compile - omitted for conflict with 5.2.7.RELEASE)
```



其中omitted for duplicate表示有jar包被重复依赖，最后写着omitted for conflict with xxx的，说明和别的jar包版本冲突了，而该行的jar包不会被引入。比如上面有一行最后写着omitted for conflict with 5.2.7.RELEASE，表示spring-core 5.2.0版本不会被项目引用，而spring-core 5.2.7版本会被项目引用



> 注：如果是idea，可以安装maven helper插件来检查依赖冲突



### 解决冲突



**a、使用第一声明者优先原则**

谁先定义的就用谁的传递依赖，即在pom.xml文件自上而下，先声明的jar坐标，就先引用该jar的传递依赖。



**b、使用路径近者优先原则**

即直接依赖级别高于传递依赖



**c、排除依赖**

使用exclusion标签

```xml
<dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.2.7.RELEASE</version>
            <exclusions>
                <exclusion>
                    <artifactId>spring-core</artifactId>
                    <groupId>org.springframework</groupId>
                </exclusion>
            </exclusions>
        </dependency>
```



**d、版本锁定**

使用**dependencyManagement **进行版本锁定，dependencyManagement可以统一管理项目的版本号，确保应用的各个项目的依赖和版本一致。