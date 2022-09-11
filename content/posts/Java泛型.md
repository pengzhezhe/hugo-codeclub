---
title: Java 泛型
date: 2022-03-19 15:20:16
categories: 
- Java
tags:
- Java
---

泛型，即“参数化类型”。一提到参数，最熟悉的就是定义方法时有形参，然后调用此方法时传递实参。那么参数化类型怎么理解呢？顾名思义，就是将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定义成参数形式（可以称之为类型形参），然后在使用/调用时传入具体的类型（类型实参）。

泛型的本质是为了参数化类型（在不创建新的类型的情况下，通过泛型指定的不同类型来控制形参具体限制的类型）。也就是说在泛型使用过程中，操作的数据类型被指定为一个参数，这种参数类型可以用在类、接口和方法中，分别被称为泛型类、泛型接口、泛型方法。

## 泛型类

泛型类型用于类的定义中，被称为泛型类。通过泛型可以完成对一组类的操作对外开放相同的接口。最典型的就是各种容器类，如：List、Set、Map。

```java
//此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型
//在实例化泛型类时，必须指定T的具体类型
public class Generic<T>{ 
    //key这个成员变量的类型为T,T的类型由外部指定  
    private T key;

    public Generic(T key) { //泛型构造方法形参key的类型也为T，T的类型由外部指定
        this.key = key;
    }

    public T getKey(){ //泛型方法getKey的返回值类型为T，T的类型由外部指定
        return key;
    }
}
```

## 泛型接口

泛型接口与泛型类的定义及使用基本相同。泛型接口常被用在各种类的生产器中。

```java
public interface Generator<T> {
    public T next();
}
```

当实现泛型接口的类，未传入泛型实参时，在声明类的时候，需将泛型的声明也一起加到类中

```java
class FruitGenerator<T> implements Generator<T>{
    @Override
    public T next() {
        return null;
    }
}
```

当实现泛型接口的类，传入泛型实参时，则所有使用泛型的地方都要替换成传入的实参类型

```java
public class FruitGenerator implements Generator<String> {

    private String[] fruits = new String[]{"Apple", "Banana", "Pear"};

    @Override
    public String next() {
        Random rand = new Random();
        return fruits[rand.nextInt(3)];
    }
}
```

## 泛型方法

> 泛型类，是在实例化类的时候指明泛型的具体类型；泛型方法，是在调用方法的时候指明泛型的具体类型 。

1. 在泛型方法的声明中，在权限修饰符之后、返回值之前需要加上该泛型方法中使用到的泛型，例如 `public <T> T methodA(T t) {...}`。
2. 在泛型类中使用了泛型的成员变量的方法并不是泛型方法，只有声明了 `<T>` 的方法才是泛型方法。
3. `<T>` 表明该方法将使用泛型类型T，此时才可以在方法中使用泛型类型T。
4. 与泛型类的定义一样，此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型。


```java
public <T> T genericMethod(Class<T> tClass)throws InstantiationException ,
  IllegalAccessException{
        T instance = tClass.newInstance();
        return instance;
}
```

## 泛型通配符

- 非限定通配符：另一方面表示了非限定通配符，因为可以用任意类型来替代。
- 一种是<? extends T>它通过确保类型必须是T的子类来设定类型的上界。
- 另一种是<? super T>它通过确保类型必须是T的父类来设定类型的下界。
- 泛型类型必须用限定内的类型来进行初始化，否则会导致编译错误。


## 参考文章

- https://blog.csdn.net/s10461/article/details/53941091