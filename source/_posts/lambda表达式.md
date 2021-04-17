---
title: lambda表达式
date: 2021-01-11 21:29:02
description: lambda 表达式。
tags: Java
---

### lambda 历史

λ（波长单位），带有参数变量的表达式称为 lambda 表达式

### lambda 表达式的基本语法：参数列表 -> 表达式

#### 参数列表

1. 如果没有参数： 直接用()来表示，参数外面 一定要加()；

   ```java
   	@Test
   	public void testOldUse2() {
   	    new Thread(() -> System.out.println("hello lambda")).start();
   	}
   ```

2. 如果有一个参数，并且参数不写类型，那么这个参数外面可以不加()；

   ```java
   public static void main(String[] args) {
      	Frame frame = new Frame();
      	frame.setSize(100, 100);
      	Button button = new Button("lambda");
      	button.addActionListener(e -> System.out.println("haha"));
      	frame.add(button);
      	frame.setVisible(true);
   }
   ```

3. 如果有两个或多个参数，不管是否写参数类型，都要加()；

   ```java
   	@Test
   	public void testNewUse3() {
      	Lambda1.User[] us = new Lambda1.User[]{new Lambda1.User("张日天", 90),
      	new Lambda1.User("叶孤城", 80), new Lambda1.User("王五", 93)};

      	// 对User数组按照score排序
      	// 参数列表类型，可以从前面的传入的数组推导出来
      	Arrays.sort(us, (o1, o2) -> Integer.compare(o1.score, o2.score));
      	System.out.print(Arrays.toString(us));
   }
   ```

4. 如果参数要加修饰符或者标签，参数一定要加上完整的类型;

   ```java
   	@Test
   	public void test2() {
      	String[] strings = new String[]{"zhangfei", "guanyu", "liubei"};
      	Arrays.sort(strings, (final String s1, final String s2) -> 			Integer.compare(s1.length(), s2.length()));
      	System.out.println(Arrays.toString(strings));
   	}
   ```

#### 表达式

1. 如果表达式只有一行，可以直接写，不需要{}；
2. 如果表达式有多行，需要{}完成代码块；
3. 如果表达式是代码块，并且方法需要返回值，在代码块必须返回一个值；
4. 如果只有单行，并且方法需要返回值，不能有 return，编译器自动推导 return；

### lambda 表达式中的变量

#### 参数：

#### 局部变量：

#### 自由变量（不是参数也不是局部变量）：

结论：lambda 表达式中的自由变量会被保存，无论什么时候执行 lambda 表达式，都可以直接使用。

1. 自由变量在 lambda 表达式中不能修改，被当成 final；（操作自由变量的代码块，称为闭包）；
2. 参数和局部变量的使用方式和普通的变量使用方式相同；
3. lambda 表达式中的 this 指向，创建 lambda 表达式的方法中的 this 【重要】

   ```java
   // lambda表达式中的this指向，创建lambda表达式的方法中的this
   public void repeatPrint(String content, int times) {
       Runnable runnable = () -> {
           for (int i = 0; i < times; i++) {
               System.out.println(content);
           }
           System.out.println(this);
       };
       new Thread(runnable).start();
   }

   @Test
   public void testVar() {
       this.repeatPrint("haha", 5);
   }
   ```

   <!-- more -->

#### 函数式接口

1. 我们能够写 lambda 表达式的地方？一个接口，且接口里面只有一个抽象方法；
2. 在 Java 中，把只有一个抽象方法的接口称为函数式接口，如果一个接口是函数是接口，可以在接口上添加@FunctionalInterface 标明这是一个函数式接口；
3. 无论是否标示为@FunctionalInterface，只要满足函数式接口的接口，Java 都会直接识别为函数是接口;(加标签好处：检查；文档)
4. 简化函数式接口的使用是 Java 提供 lambda 表达式唯一的作用；
5. 可以用接口直接引用 lambda 表达式；
6. 函数式接口里面可以写 Object 里面的方法；
7. lambda 表达式中的异常处理，lambda 表达式产生的异常要么直接在代码块重处理，要么接口的方法声明抛出；

```java
@FunctionalInterface
public interface IMyWork {
    void doWork() throws Exception;
    boolean equals(Object o);
}

public void wrapWork(IMyWork work) {
    System.out.println("do some wrap work...");
    try {
        work.doWork();
    } catch (Exception e) {
        e.printStackTrace();
    }
}

@Test
public void test() {
    // 根据环境，上下文推导执行的操作
    IMyWork work = () -> {
        System.out.println("do real work");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    };
    this.wrapWork(work);
}
```

### 方法引用

#### 类 :: 静态方法

```java
public int compare(int x, int y) {
    return Integer.compare(x, y);
}

@Test
public void test1() {
    Integer[] is = new Integer[]{2, 3, 4, 6, 4, 3, 5};
    Arrays.sort(is, Integer::compare);
    System.out.println(Arrays.toString(is));
}
```

#### 对象 :: 方法

```java
@Test
public void test2() {
    Lambda5Test lt = new Lambda5Test();
    Integer[] is = new Integer[]{2, 3, 4, 6, 4, 3, 5};
    // 方法引用（对象 :: 方法)
	//  Arrays.sort(is, lt::compare);
	//  System.out.println(Arrays.toString(is));

    List<Integer> la = Arrays.asList(2, 3, 4, 5, 6, 4, 3, 5);
    la.forEach(System.out::println);
}
```

#### 对象 :: 静态方法

### 构造器引用(需要一个无参数构造方法)

类 :: new

构造器引用对应的函数式接口里面的方法格式一定是 返回一个对象/方法没有参数

```java
@FunctionalInterface
public interface IMyCreator<T extends List<?>> {
	T create();
}

public <T> List<T> asList(IMyCreator<List<T>> creator, T... a) {
    List<T> list = creator.create();
    for (T t : a) list.add(t);
    return list;
}

@Test
public void test3() {
    // 构造方法引用
    // 类 :: new
    List<Integer> la = this.asList(ArrayList::new, 2, 3, 5, 6, 4, 2, 8);
    la.forEach(System.out::println);
    System.out.println(la.getClass());
}
```

### 接口的默认方法

1.  可以实现多重继承； 2. 解决冲突： 1. 如果两个接口中有相同方法签名的默认方法，自己必须实现冲突的方法，指定使用哪个父接口中的默认实现。

        ```
        @Override
        public void breath() {
            ILandAnimal.super.breath();
        }
        ```
        2. 如果父类里面一个方法和接口中一个默认方法有相同方法签名，使用父类里面的方法；
        3. 你永远不要期望通过接口里面的默认方法来改版Object对象里面的方法；

    3. 接口里面可以直接写接口的静态方法；
    4. 常见的一些模式被消灭了
       1. 工具栏
       2. 适配器模式
