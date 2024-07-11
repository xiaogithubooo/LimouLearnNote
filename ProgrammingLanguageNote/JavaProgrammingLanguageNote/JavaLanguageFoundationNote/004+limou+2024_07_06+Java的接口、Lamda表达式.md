

**叠甲：以下文章主要是依靠我的实际编码学习中总结出来的经验之谈，求逻辑自洽，不能百分百保证正确，有错误、未定义、不合适的内容请尽情指出！**

[TOC]

>   概要：...

>   资料：...

---

>   吐槽：一些个人吐槽，可以看也可以不看...

>   警告：一些容易错误、遗漏的点...

>   区别：一些容易混淆、混乱的点...

>   补充：一些补充性知识，最好是看一下...

# 1.接口

## 1.1.接口的使用

接口可能是 `Java` 非常独特的一个特点之一（尤其是对于 `Cpp` 来说），可以用来替代多继承。接口主要使用 `interface` 关键字来定义，而类通过 `implements` 来全部实现，而接口本身不是类（必须是全部实现，否则该类必须设计为抽象类）。并且因为接口中的方法默认是 `public` 的，所以会要求实现这些方法的类也必须提供 `public` 访问级别的实现。

从方便理解上来说，可以把接口中的成员变量和成员方法前理解为隐藏了一些部分关键字：

-   成员变量隐藏了 `(public final static) 数据类型 成员变量`
-   成员函数隐藏了 `(public abstract 返回类型 成员方法)`

```java
// 使用接口的例子
interface Test { // 一般也需要把接口同类一样定义到一个文件中(而且通常带上 public 关键字), 不过这里为了方便表述放在了一个文件中
    // 一般接口内成员都是公有的, 成员变量默认为常量, 成员函数默认为抽象
    // 接口内部也不允许出现构造器和代码块
    String data = "limou";
    void func1(int i);
    void func2(String str);
}

// 实现类
class MyClass implements Test { // 实现接口的类, 必须把接口内的方法全部实现
    @Override
    public void func1(int i) {
        System.out.print(data + " ");
        System.out.println(i);
    }
    @Override
    public void func2(String str) {
        System.out.print(data + " ");
        System.out.println(str);
    }
}

public class Main  {
    public static void main(String[] args) {
        // Test t = new Test(); // error, 因为接口本身也算是一种比较抽象的存在
        Test mt = new MyClass();
        mt.func1(10);
        mt.func2("gimou");
    }
}
```

## 1.2.接口的多态

前面我有提过，接口可以用来实现多态。

```java
// 接口实现多态的例子
// 定义接口
interface Animal {
    void makeSound();
}

// 实现接口的具体类 1
class Dog implements Animal { // 实现接口的类 1
    @Override
    public void makeSound() {
        System.out.println("Bark");
    }
}

// 实现接口的具体类 2
class Cat implements Animal { // 实现接口的类 2
    @Override
    public void makeSound() {
        System.out.println("Meow");
    }
}

// 主程序
public class Main {
    public static void main(String[] args) {
        // 接口引用指向实现类对象
        Animal animal;

        // 引用 Dog 对象
        animal = new Dog();
        animal.makeSound(); // 输出 "Bark"

        // 引用 Cat 对象
        animal = new Cat();
        animal.makeSound(); // 输出 "Meow"
    }
}
```

>   吐槽：虽然在很多教程中提及，不要把 `Java` 的接口看作类，但是说句实在话，接口的使用包括多态的实现都很像一个精简版本的抽象类，编写代码的时候可以用这种理解（但不能混为一谈）...

## 1.3.接口的继承

一个接口可以被另外一个接口以关键字 `extends` 的形式继承（有点类似类继承，但是有很大的不同）。接口可以实现类似多继承一样的效果，但是却不会出现 `Cpp` 中多继承的棱形继承问题。

`Java` 的接口为什么不会出现 `Cpp` 多继承的问题呢？`Java` 强制规定了 `class` 不允许多继承，因此避免了棱形继承。但是接口怎么做到避免呢？首先如果假设有以下代码。

>   ```java
>   // 一个类实现多个接口的例子
>   interface Animal {
>      int id = 0; // 隐式的 public static final
>      void eat();
>      void sleep();
>   }
>   
>   interface Cat extends Animal {
>      int id = 1; // 隐式的 public static final
>      void getCatId();
>   }
>   
>   interface Dog extends Animal {
>      int id = 2; // 隐式的 public static final
>      void getDogId();
>   }
>   
>   public class Main implements Cat, Dog  {
>      public void eat() {
>          System.out.println("eat");
>      }
>      public void sleep() {
>          System.out.println("sleep");
>      }
>      public void getCatId() {
>          // 这里 id 引用了哪个接口的 id？
>          // System.out.println(id);
>      }
>      public void getDogId() {
>          // 这里 id 引用了哪个接口的 id？
>          // System.out.println(id);
>      }
>   
>      public static void main(String[] args) {
>          Main main = new Main();
>          main.eat();
>          main.sleep();
>          main.getCatId();
>          main.getDogId();
>      }
>   }
>   ```
>
>   首先明确一点，上面这样的代码如果解开注释是无法直接通过编译的，因为会有二义性的问题（但是如果所有的接口中只有一个 `id` 那就不会出现二义性）。但和 `Cpp` 一样，二义性通常很好解决，把实现中的 `id` 改为 `接口.id` 即可。
>
>   但是会不会出现冗余呢？不会，为什么？因为 `id` 是属于接口的，而不是属于实现类中的成员（这也是说为什么接口不是类的一个主要原因之一）。因此实现类中，不会出现两个相同的属性被子类“继承”，不会占用一个对象的存储空间。
>
>   ```java
>   // 一个类实现多个接口的例子
>   interface Animal {
>      int id = 0; // 隐式的 public static final
>      void eat();
>      void sleep();
>   }
>   
>   interface Cat extends Animal {
>      int id = 1; // 隐式的 public static final
>      void getCatId();
>   }
>   
>   interface Dog extends Animal {
>      int id = 2; // 隐式的 public static final
>      void getDogId();
>   }
>   
>   public class Main implements Cat, Dog  {
>      public void eat() {
>          System.out.println("eat");
>      }
>      public void sleep() {
>          System.out.println("sleep");
>      }
>      public void getCatId() {
>          // 这里 id 引用了哪个接口的 id？
>           System.out.println(Cat.id);
>      }
>      public void getDogId() {
>          // 这里 id 引用了哪个接口的 id？
>           System.out.println(Dog.id);
>      }
>   
>      public static void main(String[] args) {
>          Main main = new Main();
>          main.eat();
>          main.sleep();
>          main.getCatId();
>          main.getDogId();
>      }
>   }
>   ```

>   补充：如果接口被继承的过程中，出现相同的方法签名怎么办？实现类如果同时实现两个接口，那实现的是哪一个接口的方法签名？实际上 `Java` 在这种情况下只会需要实现一个即可。
>
>   ```java
>   // 出现完全相同方法冲突
>   interface A {
>       void foo();
>   }
>   
>   interface B extends A {
>       // 接口 B 继承了接口 A，并且没有重新定义 foo 方法
>   }
>   
>   interface C extends A {
>       // 接口 C 也继承了接口 A，并且定义了一个相同签名的 foo 方法
>       void foo(); 
>   }
>   
>   class MyClass implements B, C {
>       // MyClass 实现了接口 B 和 C
>       // 由于接口 B 和 C 都继承了接口 A，并且接口 B 没有重新定义 foo 方法，
>       // MyClass 只需提供一次 foo 方法的具体实现即可。
>       
>       @Override
>       public void foo() {
>           System.out.println("Implementing foo() in MyClass");
>           // 具体的实现代码
>       }
>   }
>   ```
>
>   但如果只是方法名相同，参数不同，就需要同时实现两个同名方法。
>
>   ```java
>   // 参数不同的方法名冲突
>   interface A {
>       void foo(int x);
>   }
>   
>   interface B extends A {
>       // 接口 B 继承了接口 A，并且没有重新定义 foo 方法
>   }
>   
>   interface C extends A {
>       // 接口 C 也继承了接口 A，但重新定义了 foo 方法，参数类型不同
>       void foo(String s);
>   }
>   
>   class MyClass implements B, C {
>       // MyClass 实现了接口 B 和 C
>       // 由于接口 B 和 C 都继承了接口 A，但 foo 方法的参数列表不同，
>       // MyClass 需要提供两个不同的 foo 方法的实现。
>   
>       @Override
>       public void foo(int x) {
>           System.out.println("Implementing foo(int) in MyClass");
>           // 具体的 int 类型参数实现
>       }
>   
>       @Override
>       public void foo(String s) {
>           System.out.println("Implementing foo(String) in MyClass");
>           // 具体的 String 类型参数实现
>       }
>   }
>   ```
>
>   那如果仅仅是返回值不同呢？很抱歉，这是不被允许的，除非参数也不一样。
>
>   ```java
>   // 返回值不同的方法冲突
>   interface A {
>       int foo(int x);
>   }
>   
>   interface B extends A {
>       // 接口 B 继承了接口 A，并且没有重新定义 foo 方法
>   }
>   
>   interface C extends A {
>       // 接口 C 也继承了接口 A，但重新定义了 foo 方法，返回值类型不同
>       double foo(double x); // 但是这里的 x 类型就不允许是 int 了
>   }
>   
>   class MyClass implements B, C {
>       // MyClass 实现了接口 B 和 C
>       // 由于接口 B 和 C 都继承了接口 A，但 foo 方法的返回值类型不同，
>       // MyClass 会因为重复的方法签名而导致编译错误。
>   
>       @Override
>       public int foo(int x) {
>           System.out.println("Implementing foo(int) in MyClass");
>           // 具体的 int 类型返回值实现
>           return x;
>       }
>   
>       @Override
>       public double foo(double x) {
>           System.out.println("Implementing foo(int) in MyClass");
>           // 具体的 double 类型返回值实现
>           return x * 1.0; // 示例简化，实际应用中可能会进行类型转换
>       }
>   }
>   
>   ```

## 1.4.接口的作用

接口本身算是比抽象类更加抽象的实现，通常在两者中会依据以下情况进行抉择：

-   **有多继承需求**：`Java` 的类只能进行单继承，但是一个类可以却可以实现多个接口。如果一个类需要继承多个不相关的类或者需要表达多种行为，接口提供了一种灵活的解决方案（同时避免 `Cpp` 中的多继承问题，至于原因后面提）
-   **继承无关关系**：接口提供了一种基于类型的契约机制，允许不同的类实现相同的接口，而不用关心它们的具体继承关系。这在设计模式中的应用尤为显著，例如策略模式、观察者模式等
-   **适用于多态性**：接口是实现多态性的一种重要机制，通过接口引用可以指向实现了该接口的任何类的对象，这样可以在运行时动态确定调用哪个类的方法
-   **公共接口设计**：在设计公共 `API` 时，接口更容易理解和使用。它们定义了规范，使用者只需要关心接口定义的方法和语义，而不需要了解具体的实现细节。并且还会强迫实现者按住接口的规定进行实现，提高代码效率，对实现类进行规范拓展。这种行为，其实就被称为 ==面向接口编程==
-   **提高代码维护**：如果根据接口设计出不同的实现类，就可以很快使用不同的实现类进行方案替换，这样多个实现类直接可以进行切换，使用实现类来做到中间层的隔离

## 1.5.接口的变化

在 `jdk8` 之后，接口也发生了进化，有些比较新的特性（对于 `jdk8` 之前来说）也值得我们学习。

-   **默认方法**：接口可以包含默认方法，在实现类中如果不进行实现，就会默认实现默认方法的内容
-   **静态方法**：接口可以包含静态方法，这些方法可以直接通过接口名称调用，不需要实例化接口的实现类
-   **私有方法**：接口可以包含私有方法，这些方法只能在接口内部被调用，不会暴露给接口的实现类或者外部代码（但是这是 `jdk9` 特性）

此外还有一个 **函数式接口** 的概念，如果在接口定义前加上 `@FunctionalInterface` 注解则代表接口只包含一个抽象方法的接口，通常用于支持 `Lambda` 表达式，该注解用来强制执行该特性，以确保接口只有一个抽象方法。

```java
// 定义一个函数式接口, 用于表示可以计算面积的形状, 并且确保只有一个抽象接口
@FunctionalInterface
interface Shape {
    // 抽象方法(计算面积)
    double calculateArea();

    // 默认方法(打印名称)
    default void printShape() {
        log();
        System.out.println("Shape: " + this.getClass().getSimpleName());
    }

    private void log() {
        System.out.println("This is a log");
    }

    // 静态方法(比较面积)
    static void compareShapes(Shape shape1, Shape shape2) {
        double area1 = shape1.calculateArea();
        double area2 = shape2.calculateArea();

        if (area1 == area2) {
            System.out.println("The areas of the shapes are equal.");
        } else if (area1 > area2) {
            System.out.println("Shape 1 has a larger area.");
        } else {
            System.out.println("Shape 2 has a larger area.");
        }
    }
}

// 实现一个圆形类
class Circle implements Shape {
    private double radius;

    Circle(double radius) {
        this.radius = radius;
    }

    @Override
    public double calculateArea() { // 实现接口
        return Math.PI * radius * radius;
    }
}

// 实现一个矩形类
class Rectangle implements Shape {
    private double width;
    private double height;

    Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }

    @Override
    public double calculateArea() { // 实现接口
        return width * height;
    }
}

public class Main {
    public static void main(String[] args) {
        Circle circle = new Circle(5);
        Rectangle rectangle = new Rectangle(4, 6);

        circle.printShape(); // 使用默认方法打印形状名称(和之前的接口一样)
        rectangle.printShape(); // 使用默认方法打印形状名称(实现类没有显示实现, 但是按照接口中默认方法的内容隐式实现了)

        Shape.compareShapes(circle, rectangle); // 使用静态方法比较两个形状的面积(无需使用实现类实现就可以被调用)
    }
}
```

# 2.Lamda 表达式

## 2.1.

## 2.2.



---

>   结语：...