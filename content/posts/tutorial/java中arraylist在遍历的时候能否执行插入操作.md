---
title: "java中ArrayList在遍历的时候能否执行插入操作"
date: 2026-01-08T16:37:48+08:00
lastmod: 2026-01-08T16:37:48+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "java中ArrayList在遍历的时候能否执行插入操作java中ArrayList在遍历的时候能否执行插入操作java中ArrayList在遍历的时候能否执行插入操作java中ArrayList在遍历的时候能否执行插入操作java中ArrayList在遍历的时候能否执行插入操作"
weight:
slug: ""
draft: false # 是否为草稿
comments: true # 本页面是否显示评论
reward: true # 打赏
mermaid: true #是否开启mermaid
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示路径
cover:
    image: "" #图片路径例如：posts/tech/123/123.png
    zoom: # 图片大小，例如填写 50% 表示原图像的一半大小
    caption: "" #图片底部描述
    alt: ""
    relative: false
---


![image](/api/uploads/89ba4ceb8a864305bd266fd519cee15e.jpg)


# javase

## **java中ArrayList在遍历的时候能否执行插入操作**

在Java中，如果在使用`ArrayList`迭代器进行遍历时，尝试执行插入操作，就会抛出`ConcurrentModificationException`异常，因为在迭代器遍历期间，`ArrayList`的结构发生了变化，导致迭代器失效。

例如，以下代码会抛出异常：

```
javaCopy codeArrayList<String> list = new ArrayList<>();
list.add("one");
list.add("two");
list.add("three");

for (String item : list) {
    if (item.equals("two")) {
        list.add("four"); // 尝试在遍历时插入元素
    }
}
```

为了避免这种异常，可以使用`ListIterator`的`add()`方法进行插入操作。`ListIterator`允许在遍历期间插入元素，并且可以选择在当前迭代器位置之前或之后插入元素。

例如，以下代码可以在遍历时插入元素而不会抛出异常：



```
javaCopy codeArrayList<String> list = new ArrayList<>();
list.add("one");
list.add("two");
list.add("three");

ListIterator<String> iterator = list.listIterator();
while (iterator.hasNext()) {
    String item = iterator.next();
    if (item.equals("two")) {
        iterator.add("four"); // 在当前位置之后插入元素
    }
}
```

## java的多态体现在那些方面

Java中的多态是一种重要的面向对象编程的概念，它允许不同的对象以不同的方式对同一个方法进行响应。

多态可以体现在以下方面：

1. 方法重载：在Java中，可以定义多个具有相同名称但参数列表不同的方法，这被称为方法重载。在方法调用时，根据参数类型和数量的不同，Java编译器可以自动选择合适的方法进行调用。这是静态多态的一种形式。
2. 方法重写：在Java中，子类可以重写其父类中的方法，以便子类在其特定的上下文中提供更具体的实现。在运行时，具体对象的方法调用将调用正确的重写版本。这是动态多态的一种形式。
3. 向上转型：Java允许将子类的对象赋给父类引用变量，这被称为向上转型。在这种情况下，可以访问父类中定义的方法和属性，但无法访问子类中定义的方法和属性。在运行时，具体对象的方法调用将调用正确的版本，而不是编译时的版本。
4. 接口：Java中的接口是一种特殊的类，它只包含方法的签名而没有实现。一个类可以实现一个或多个接口，并提供实现接口中的所有方法。这种实现方式使得类可以适应不同的接口类型，从而实现多态性。

总之，Java中的多态可以通过方法重载、方法重写、向上转型和接口来实现，这些特性可以使得代码更加灵活、可扩展和易于维护。

## 如何理解java中多态的向上转型

在Java中，多态是面向对象编程中的一个重要概念，它可以让同一个方法在不同的对象上产生不同的行为。而向上转型是多态的一种表现形式，它是指将一个子类对象赋值给一个父类类型的变量或者参数。

理解Java中多态的向上转型，可以从以下几个方面入手：

1. 继承和多态：在Java中，继承是实现多态的基础。通过继承，子类可以继承父类的属性和方法，并且可以在子类中重写父类的方法，实现多态。
2. 类型转换：在Java中，对象之间可以进行类型转换。向上转型是指将一个子类对象转换成它的父类对象，这个过程是自动的，无需显式地进行类型转换操作。
3. 使用多态的好处：通过向上转型，可以将一个对象的类型抽象为它的父类类型，从而提高代码的灵活性和可扩展性。通过向上转型，可以将不同的子类对象赋值给同一个父类类型的变量，从而可以更方便地实现代码复用和重构。

总之，Java中的多态和向上转型是面向对象编程中的重要概念，它们可以提高代码的灵活性和可扩展性，同时也可以提高代码的复用性和重构性。要理解Java中多态的向上转型，需要深入理解继承、多态和类型转换等概念，并且在实际编程中不断积累经验。

## lock 和 synchronized的区别

1. synchronized是java 中的同步关键字，lock是juc包下的接口，有很多实现类 reentranlock(重入锁)
2. synchronized有两种方式去控制锁的力度， 修饰方法和修饰代码块，我们可以通过加锁对象的生命周期来控制锁的作用范围，比如锁对象是静态对象或者是类对象，此时这个锁就是全局锁，如果是普通实例对象，这个锁的范围取决于这个实例的生命周期，lock锁的粒度是通过里面提供的lock()和unlock()方法来控制的，锁的作用域，取决与lock实例的生命周期，lock的灵活的比synchronized更高，只需要调用加锁和释放锁的方法就实现了，同时lock还提供了非阻塞的竞争锁方法，trylock()，通过返回true/false来告诉当前线程是否有线程正在使用锁，而synchronized因为是关键字所以无法实现，另外synchronized释放锁是被动的，需要等代码执行完毕，或者代码快中出现异常才释放锁。

# Spring

## Spring如果通过@autoware实现依赖的注入（做了那些事），如果让你来实现，怎么实现

使用Spring的`@Autowired`注解可以实现依赖注入，主要是通过反射来自动解析和设置依赖对象。当Spring容器初始化Bean时，会扫描Bean中的`@Autowired`注解，并查找可用的Bean类型。如果找到多个符合条件的Bean，则会根据`@Primary`和`@Qualifier`注解进行筛选，最终注入符合条件的Bean到目标Bean中。

如果要手动实现依赖注入，可以按照以下步骤：

1. 创建Bean容器：定义一个类似于Spring的Bean容器，用于管理Bean对象的生命周期和依赖关系。
2. 扫描类：扫描所有需要注入依赖的类，并记录它们的依赖关系。
3. 创建Bean对象：根据类定义创建Bean对象，并将其放入Bean容器中。
4. 解决依赖关系：遍历Bean容器中的每个Bean对象，查找并设置它们的依赖关系。
5. 返回Bean容器：返回已经完成依赖注入的Bean容器，供调用者使用。

实现依赖注入的关键在于如何解决依赖关系。可以通过以下方式来解决：

1. 构造函数注入：定义类的构造函数，使用它来注入依赖对象。
2. Setter方法注入：定义类的setter方法，使用它来注入依赖对象。
3. 字段注入：在类中定义需要注入的字段，并在Bean容器中查找并设置它们的值。

总之，手动实现依赖注入的过程比较复杂，需要考虑到多个方面，包括Bean对象的生命周期、依赖关系的解析和设置等。而Spring的`@Autowired`注解可以自动实现依赖注入，并且提供了更为方便的使用方式。

## 事务在一个方法内读取的数据进行下一步操作需要开启事务吗？

如果一个方法内读取的数据需要进行下一步操作，且下一步操作与该数据具有关联，那么通常需要在该方法内开启事务。

事务是用来维护数据的一致性和完整性的，它可以保证一系列操作要么全部成功，要么全部失败。如果一个方法内需要读取的数据涉及到后续操作的一致性和完整性，那么为了保证数据的正确性，需要在该方法内开启事务。这样可以保证所有的读取和操作都在同一个事务中进行，从而保证数据的一致性和完整性。

如果一个方法内的操作只涉及到读取数据，而没有后续操作或者后续操作与读取的数据没有关联，那么不一定需要开启事务。在这种情况下，读取数据只是查询操作，不会对数据造成影响，也不会影响数据的一致性和完整性。但是，如果该方法的调用者希望保证数据的一致性和完整性，也可以在外部事务中调用该方法。

总之，是否需要在一个方法内开启事务，取决于该方法涉及到的数据读取和操作是否需要保证数据的一致性和完整性。如果需要，那么应该在该方法内开启事务，如果不需要，那么可以不用开启事务。

## 说一下HashMap数据结构有什么特点?

`HashMap` 是一种常见的哈希表实现，它是一种用于存储键-值对的数据结构。以下是 `HashMap` 的主要特点：

1. **快速检索：** `HashMap` 提供了快速的查找操作。通过哈希函数，它可以在常量时间内定位到存储桶（bucket）中的位置，从而实现快速的键值查找。
2. **键唯一性：** 每个键都必须是唯一的。当插入新的键值对时，如果键已经存在，新的值会替换旧的值。这确保了每个键都映射到唯一的值。
3. **哈希冲突：** 不同的键可能映射到相同的哈希值，这就是哈希冲突。`HashMap` 使用一种解决冲突的方法，例如链地址法或开放地址法。在链地址法中，每个桶存储一个链表或其他数据结构，用于存储哈希冲突的键值对。
4. **动态扩容：** 当 `HashMap` 中的元素数量达到一定阈值时，它会自动进行扩容，重新分配更大的存储空间。这有助于保持较低的负载因子，提高性能。
5. **无序性：** `HashMap` 中的键值对没有固定的顺序。如果需要有序性，可以考虑使用 `TreeMap` 或其他有序映射实现。
6. **允许 null 值和 null 键：** `HashMap` 允许存储 null 值，同时也允许将 null 作为键。

总的来说，`HashMap` 提供了高效的查找操作，但在存储大量数据时，需要注意合理设置初始容量和负载因子，以避免频繁的扩容操作。

## SpringBoot 自动装配机制的原理

## SpringBoot 解释约定大于配置

 

## Springboot自动装配是什么

自动把第三方组件中的bean装载在Ioc容器内，不需要开发人员手动的去写bean的配置，在Springboot中只需要在启动类上加上SpringBootApplication来实现自动装配，这个注解是一个复合注解，主要是@enableAutoConfiguration，自动装配的实现主要依靠三个关键技术，

1. 引入Starter，启动依赖组件时，这个组件里面必须要包含一个‘@Configuration配置类，在这个类里面使用@Bean注解 去声明要装配到ioc容器的bean对象
2. 这个类是放在第三方jar包里面，通过springboot中约定大于配置的理念去吧这个配置类的全路径放在**classPath:/META-INF/spring.factories**这里，这样springboot就可以的到第三方jar这个配置类的位置，这个步骤主要是用到了springboot中的**springFactioriesloader**来完成的。

3. springboot拿到第三方声明的配置类之后，使用ImportSelector这个接口来实现对这个配置类的动态加载，由此来完成自动装配。

## Springboot中有哪些方式可以把Bean注入到IOC容器

1. *使用xml声明Bean，Spring容器在启动的时候会解析这个xml，并且把bean注入到ioc容器
2. *使用@CompontScan注解扫描声明了@Controller，@Service，@Repository，@Component注解的类
3. *使用@Configuration注解声明配置类，使用@Bean注解实现bean的定义
4. *使用@Import注解，导入配置类或者普通的Bean
5. 使用FacoryBean工厂bean，动态构建Bean实例，springCloud中的openFegin里面的动态代理就是通过FacoryBean实现的
6. 实现ImportBeanDefinition-Register接口，可以动态注入Bean实例，这个在Springboot启动类中有用到
7. 实现ImportSelector接口，动态批量注入配置类或者Bean'对象，这个在springboot自动装配里面有用到

# mybatis

## Mybatis中#{}和${}的区别

1. 这两种都是mybatis去实现动态SQL的方式，可以把参数传递到xml文件中
2. #占位符等同于jdbc中的一个？占位符，可以防止SQL注入，而$占位符相当于把参数直接拼接到sql中，mybatis不会对其做任何处理，无法防止SQL注入
3. 总结，$相当与动态参数（可以用在动态传递表名，设置排序字段等），#是占位符。

## Mybatis里面的缓存机制
1. mybatis的一级缓存是SQLSession级别的缓存，在操作数据库时需要构造SqlSession对象，不同的SqlSession之间缓存数据区域（HashMap）是互相不影响的。
   一级缓存的作用域是SqlSession范围的，需要注意的是：如果SqlSession执行了DML操作（insert、update、delete），并执行commit（）操作，mybatis则会清空SqlSession中的一级缓存，这样做的目的是为了保证缓存数据中存储的是最新的信息，避免出现脏读现象。

2. 二级缓存是mapper级别的缓存，使用二级缓存时，多个SqlSession使用同一个Mapper的sql语句去操作数据库，得到的数据会存在二级缓存区域，相比一级缓存，二级缓存是跨SqlSession的。
   二级缓存是多个SqlSession共享的，其作用域是mapper的同一个namespace，Mybatis默认没有开启二级缓存，需要在setting全局参数中配置开启二级缓存。

3. 共同点两者都它是使用HashMap进行数据存储，且都是在第一次执行完毕会将数据库中查询的数据写到缓存（内存），第二次查询时会从缓存中获取数据，不再去底层数据库查询，从而提高查询效率。

