---
title: 优雅的处理NullPointerExcepter
copyright: true
abbrlink: c664e20c
date: 2019-10-16 09:50:56
categories:
  - NoteBook
tags:
  - JAVA
top: 
---
Optional是Java8提供的为了解决null安全问题的一个API。

阿里巴巴编码规约-异常处理

```
说明：本手册明确防止 NPE 是调用者的责任。  
10. 【推荐】防止 NPE，是程序员的基本修养，注意 NPE 产生的场景：   
	1）返回类型为基本数据类型，return 包装数据类型的对象时，自动拆箱有可能产生 NPE。       
	反例：`public int f() { return Integer 对象}`， 如果为 null，自动解箱抛 NPE。  
	2） 数据库的查询结果可能为 null。   
	3） 集合里的元素即使 isNotEmpty，取出的数据元素也可能为 null。 
	4） 远程调用返回对象时，一律要求进行空指针判断，防止 NPE。  
	5） 对于 Session 中获取的数据，建议 NPE 检查，避免空指针。  
	6） 级联调用 `obj.getA().getB().getC()；`一连串调用，易产生 NPE。  
	正例：使用 JDK8 的 Optional 类来防止 NPE 问题。  
```

## 一：Optional类方法
![Optional类方法](method.jpg)
### **1. 创建 Optional 相关方法**

方法：Optional.of、Optional.ofNullable、Optional.empty()：

```
`Optional emptyOptional = Optional.empty(); Optional nonEmptyOptional = Optional.of("name"); Optional nonEmptyOptional = Optional.ofNullable(null); `
```

### **2. 检查 Optional 值**

方法：Optional.isPresent()、Optional.ifPresent()
- 如果 Optional有值，isPresent() 返回 true
- ifPresent() 如果值存在，则执行代码块

### **3. 通过 Optional 取值**

- get() 返回值包含在 Optional 中返回，（建议配合isPresent() 使用，假如 Optional 不包含一个值, get() 将会抛出一个异常）
- orElse() 如果值不存在，则返回默认值  
- orElseGet() 与 orElse() 类似，如果 Optional 不包含值，用函数作为返回值
- orElseThrow() 与 orElseGet() 类似，监测到值为 null 时抛出异常

### 4. map转换值：Stream和Optional 的map方法对比图
- Stream和optional的fiatMap方法对比图  
![Optional-Map](Map.png)
![Optional-FlatMap](FlatMap.png)
![Optional-Use-Map](UseMap.png)
可以把Optional看成包含一个元素的Stream对象。
### 5. filter
```
`String name = "Aa"; Optional optionalName = Optional.of(name).filter(str -> str.length() > 2);`
```
## 注意：
- ### 无法序列化
  由于Optional 类设计时就没特别考虑将其作为类的字段使用，所以它也并未实现Serializable；  
  把 Optional 类型用作属性或是方法参数在 IntelliJ IDEA 中更是强力不推荐的  
  用Optional 声明域模型中某些类型是不错的主意。  
  如果非要实现序列化的模型域，可以参考下例  

- ### 基于值的类（说明：这是一个基于值的class类，对于同一性（特性）敏感的操作 （包含引用的相等性如:==）,同一性的hashcode或者同步等等、对optional实例可能会产生不可预料的结果，这种结果应该被避免。）
  https://docs.oracle.com/javase/8/docs/api/java/lang/doc-files/ValueBased.html

  这里说的是基于值的类需要满足以下几点：
  1、 final类型和不可变的（可能会包含可变对象的引用）  
  2、 有equals、hashCode、toString方法的实现，它是通过实例的状态计算出来的，而并不会通过其它的对象或变量去计算。   
  3、 不会使用身份敏感的操作，比如在二个实例之间引用相等性、hashCode或者内在的锁。  
  4、 判断二个值相等仅仅通过equal方法，而不会通过==去判断。  
  5、 它不提供构造方法，它通过工厂方法创建它的实例，这不保证返回实例的一致性。  
  6、 当它们相等时，它是可以自由替换的。如果x和y 调用equal方法返回true，那么可以将x和y任意交换，它的结果不会产生任何变化。  

## 二：实战示例
### 1. 用Optional封装可能为Null的值
- 假设有一个Map<String,Object>方法； 如果map 没有关联的key，就会返回null。
  ```
  Object value = map.get("key");
  ```
  可以替换成
  ```
  Optional<Object> value = Optional.ofNullable(map.get("key"));
  ```
### 2. 作为返回值（不建议作为参数）


### 3. 把所有内容整合起来
## 三：增强
- **Java 9**
- or()：如果值存在，返回包含该值的 Optional 对象；否则，返回 or() 函数生成的 Optional 对象。
- ifPresentOrElse(Consumer<? super T>action, Runnable emptyAction)：如果值存在，使用该值执行指定调用，否则使用空值执行调用。
- stream()：如果值存在，返回该值的顺序流（Stream）；否则返回空流。
- **Java 10**
- orElseThrow()：如果值存在，返回该值；否则抛出NoSuchElementException。注意：与 Java 8 不同，不接受任何参数。
- **Java 11**
- isEmpty()：如果值不存在，返回 true；否则返回 false。
- 