---
title: Mapstruct
date: 2021-12-04 23:59:57
tags:
    - java
    - 教程
categories:
    - [教程,java]
cover: 'https://www.linuxidc.com/upload/2014-09/140905163467611.jpg'

---





# MapStruct





## MapStruct是什么



**MapStruct** 是一个代码生成器，它基于约定优于配置方法极大地简化了 Java bean 类型之间映射的实现。

生成的映射代码使用简单的方法调用，因此速度快、类型安全且易于理解。



## MapStruct的作用

在一个成熟的工程中，尤其是现在的分布式系统中，应用与应用之间，还有单独的应用细分模块之后，DO 一般不会让外部依赖，这时候需要在提供对外接口的模块里放 DTO 用于对象传输，也即是 DO 对象对内，DTO对象对外，DTO 可以根据业务需要变更，并不需要映射 DO 的全部属性。

为了避免get/set的大量操作，MapStruct 就是这样的一个属性映射工具，只需要定义一个 Mapper 接口，MapStruct 就会自动实现这个映射接口，避免了复杂繁琐的映射实现。



## MapStruct的使用



相关的依赖

```xml
<properties>
    <mapstruct.version>1.3.1.Final</mapstruct.version>
</properties>
<dependencies>
    <dependency>
        <groupId>org.mapstruct</groupId>
        <artifactId>mapstruct</artifactId>
        <version>${mapstruct.version}</version>
    </dependency>
    <dependency>
        <groupId>org.mapstruct</groupId>
        <artifactId>mapstruct-jdk8</artifactId>
        <version>${mapstruct.version}</version>
    </dependency>
    <dependency>
        <groupId>org.mapstruct</groupId>
        <artifactId>mapstruct-processor</artifactId>
        <version>${mapstruct.version}</version>
    </dependency>
</dependencies>
```



相关的实体：

```java
@Data
public class Person {
    private Integer age;
    private String fullname;
}
```

```java
@Data
public class PersonDTO {
    private Integer age;
    private String name;
}
```



定义一个映射器

```kotlin
@Mapper
public interface UserMap {

    @Mapping(source = "fullname", target = "name")
    PersonDTO personToPersonDto(Person person);
}
```

如果你的DTO和实体类中的字段名称是一致的，只需要写方法签名即可，不需要写任何代码。
如果参数名称有变化，需要使用`@Maping`注解，`source`为原参数名称，`target`为转换后的类的参数名称。

编译后，会在同级目录生成实现类，如下：

![生成的目录](https://upload-images.jianshu.io/upload_images/11774306-215da8684bcd12e5.png?imageMogr2/auto-orient/strip|imageView2/2/w/806/format/webp)



## MapStruct的用法

### MapStruct的相关语法：

> 官方参考文档：https://mapstruct.org/documentation/stable/reference/html/

**`@Mapper`注解**

将接口标记为映射接口，并让 MapStruct 处理器在编译期间启动。

**`@Mapping`**

对于源对象和目标对象中具有不同名称的属性，可以`@Mapping`使用注解来配置名称。

在需要和可能的情况下，将对源和目标中具有不同类型的属性执行类型转换，例如，`type`属性将从枚举类型转换为字符串。

```
    // 内部属性套嵌映射使用 通过对象名.属性的形式指定映射
    @Mapping(source = "engine.horsePower", target = "horsePower") 
    @Mapping(source = "engine.fuel", target = "fuel")
    @Mapping(source = "make", target = "manufacturer")
    @Mapping(source = "numberOfSeats", target = "seatCount")
    CarDTO carToCarDto(Car car);
```

> source : 源字段属性 ， target ： 目标字段属性；（如果源字段和目标字段名称类型相同，可省略不写）



**`@mappings`**

配置多个@mapping

```
@Mappings({
	@Mapping(source = "engine.horsePower", target = "horsePower"),
    @Mapping(source = "engine.fuel", target = "fuel"),
    @Mapping(source = "make", target = "manufacturer")
})
 CarDTO carToCarDto(Car car);
```





**`@MappingTarget`**

更新现有的Bean实例 , 在某些情况下，您需要的映射不会创建目标类型的新实例，而是更新该类型的现有实例。使用如下：

```kotlin
	// 将carDTO更新到Car
	@Mapping(target = "make", source = "manufacturer")
    @Mapping(target = "numberOfSeats", source = "seatCount")
    void updateCarFromDto(CarDTO carDTO,@MappingTarget Car car);
```



### 使用映射器

#### 直接使用

```java
@Mapper
public interface CarMap {
    CarMap CAR_MAP = Mappers.getMapper(CarMap.class);
}
```

#### 整合Spring

设置`componentModel = "spring"`，需要使用的地方直接通过`@Resource`注入即可

```kotlin
@Mapper(componentModel = "spring")
public interface CarMap {
...
}
```



### 数据类型转换

#### 隐式类型转换

在许多情况下，MapStruct会自动处理类型转换。

例如，如果某个属性int在源Bean中是int类型，但String在目标Bean中是类型，则生成的代码将分别通过分别调用String#valueOf(int)和来透明地执行转换Integer#parseInt(String)。

- 之间的所有Java基本数据类型及其相应的包装类型，例如之间int和Integer，boolean和Boolean等生成的代码是null转换一个包装型成相应的原始类型时一个感知，即，null检查将被执行。
- 在所有Java原语数字类型和包装器类型之间，例如在int和long或byte和之间Integer。



#### 数字，日期 格式化

```kotlin
	// 科学计数法
	@Mapping(target = "engine.horsePower", source = "engine.horsePower", numberFormat = "#.##E0")
	// 将price前面添加$前缀，后面添加两位0
    @Mapping(target = "price", source = "price", numberFormat = "$#.00")
	// 时间格式化的格式为yyyy-MM-dd HH:mm:ss
    @Mapping(target = "createTime", dateFormat = "yyyy-MM-dd HH:mm:ss")
    @Mapping(source = "make", target = "manufacturer")
    @Mapping(source = "numberOfSeats", target = "seatCount")
    CarDTO updateCarFromDto(Car car);
```



### 使用表达式

例如： 有些时候需要使用自己写的方法对一些属性进行映射，比如VO中有一个map，需要转换的类中存储的是该map的json 字符串形式。

```java
@Data
public class AddPassagewayParam {
    private HashMap<String,Object> accessMap;
}
```

```java
@Data
public class SmsPassageway implements Serializable {
    // json字符串形式
    private String accessMsg;
```

需要将map映射为json str
这时候我们借助“表达式”可以这样写

```kotlin
@Mapper(componentModel = "spring")
public interface SmsPassagewayMap {

    @Mapping(target = "accessMsg",
            expression = "java(com.jd.icity.tools.JsonHelper.object2Json(addPassagewayParam.getAccessMap()))"
    )
    SmsPassageway addPassagewayParam2SmsPassageway(AddPassagewayParam addPassagewayParam);
}
```

