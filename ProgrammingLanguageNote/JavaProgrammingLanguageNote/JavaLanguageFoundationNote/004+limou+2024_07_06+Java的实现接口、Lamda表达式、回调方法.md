

**叠甲：以下文章主要是依靠我的实际编码学习中总结出来的经验之谈，求逻辑自洽，不能百分百保证正确，有错误、未定义、不合适的内容请尽情指出！**

[TOC]

>   概要：...

>   资料：...

---

# 1.实现接口

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

>   补充：为什么使用接口而不是用抽象类呢？因为有些类需要规范实现多个方法，因此就需要多种接口。而抽象类对于类来说只能继承一个，因此抽象类具有局限性，但多个接口可以被同一个类实现，这点就是接口的最大有时。

接口本身算是比抽象类更加抽象的实现，通常在两者中会依据以下情况进行抉择：

-   **有多继承需求**：`Java` 的类只能进行单继承，但是一个类可以却可以实现多个接口。如果一个类需要继承多个不相关的类或者需要表达多种行为，接口提供了一种灵活的解决方案（同时避免 `Cpp` 中的多继承问题，至于原因后面提）
-   **继承无关关系**：接口提供了一种基于类型的契约机制，允许不同的类实现相同的接口，而不用关心它们的具体继承关系。这在设计模式中的应用尤为显著，例如策略模式、观察者模式等
-   **适用于多态性**：接口是实现多态性的一种重要机制，通过接口引用可以指向实现了该接口的任何类的对象，这样可以在运行时动态确定调用哪个类的方法
-   **公共接口设计**：在设计公共 `API` 时，接口更容易理解和使用。它们定义了规范，使用者只需要关心接口定义的方法和语义，而不需要了解具体的实现细节。并且还会强迫实现者按住接口的规定进行实现，提高代码效率，对实现类进行规范拓展。这种行为，其实就被称为 ==面向接口编程==
-   **提高代码维护**：如果根据接口设计出不同的实现类，就可以很快使用不同的实现类进行方案替换，这样多个实现类直接可以进行切换，使用实现类来做到中间层的隔离

## 1.2.接口的引用

前面我有提过，接口可以用来实现多态，这里的原理就是使用引用来实现。

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
        
        // 接口不是类, 但是可以作为接口变量引用实现了接口的类对象
    }
}
```

>   吐槽：虽然在很多教程中提及，不要把 `Java` 的接口看作类，但是说句实在话，接口的使用包括多态的实现都很像一个精简版本的抽象类，编写代码的时候可以用这种理解（但不能混为一谈）...

## 1.3.接口的继承

一个接口可以被另外一个接口以关键字 `extends` 的形式继承（有点类似类继承，但是有很大的不同）。接口与类不同，允许被多继承，因此可以实现类似 `Cpp` 多继承一样的效果，但是却不会出现 `Cpp` 中多继承的棱形继承问题。

`Java` 的接口为什么不会出现 `Cpp` 多继承的问题呢？`Java` 强制规定了 `class` 不允许多继承，因此避免了棱形继承。但是接口怎么做到避免呢？首先如果假设有以下代码（以下代码是故意模仿 `Cpp` 来使用多继承的）。

```java
// 一个类实现多个接口的例子
interface Animal {
    int id = 0; // 隐式的 public static final
    void eat();
    void sleep();
}

interface Cat extends Animal {
    int id = 1; // 隐式的 public static final
    void getCatId();
}

interface Dog extends Animal {
    int id = 2; // 隐式的 public static final
    void getDogId();
}

public class Main implements Cat, Dog  {
    public void eat() {
        System.out.println("eat");
    }
    public void sleep() {
        System.out.println("sleep");
    }
    public void getCatId() {
        // 这里 id 引用了哪个接口的 id？
        // System.out.println(id);
    }
    public void getDogId() {
        // 这里 id 引用了哪个接口的 id？
        // System.out.println(id);
    }

    public static void main(String[] args) {
        Main main = new Main();
        main.eat();
        main.sleep();
        main.getCatId();
        main.getDogId();
    }
}
```

首先明确一点，上面这样的代码如果解开注释是无法直接通过编译的，因为会有二义性的问题（但是如果所有的接口中只有一个 `id` 那就不会出现二义性，这种情况是允许编译的）。但和 `Cpp` 一样，二义性通常很好解决，把实现中的 `id` 改为 `接口.id` 即可。

但是会不会出现冗余呢？不会，为什么？因为 `id` 是属于接口的，而不是属于实现类中的成员（这也是说为什么接口不是类的一个主要原因之一）。因此实现类中，不会出现两个相同的属性被子类“继承”，不会占用一个对象的存储空间。

```java
// 一个类实现多个接口的例子
interface Animal {
    int id = 0; // 隐式的 public static final
    void eat();
    void sleep();
}

interface Cat extends Animal {
    int id = 1; // 隐式的 public static final
    void getCatId();
}

interface Dog extends Animal {
    int id = 2; // 隐式的 public static final
    void getDogId();
}

public class Main implements Cat, Dog  {
    public void eat() {
        System.out.println("eat");
    }
    public void sleep() {
        System.out.println("sleep");
    }
    public void getCatId() {
        // 这里 id 引用了哪个接口的 id？
        System.out.println(Cat.id);
    }
    public void getDogId() {
        // 这里 id 引用了哪个接口的 id？
        System.out.println(Dog.id);
    }

    public static void main(String[] args) {
        Main main = new Main();
        main.eat();
        main.sleep();
        main.getCatId();
        main.getDogId();
    }
}
```

>   补充：如果接口被继承的过程中，出现相同的方法签名怎么办？实现类如果同时实现两个接口，那实现的是哪一个接口的方法签名？实际上 `Java` 在这种情况下只会要求实现一个即可。
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
>       // 由于接口 B 和 C 都继承了接口 A, 且接口 B 没有重新定义 foo 方法
>       // MyClass 只需提供一次 foo 方法的具体实现即可
>       // 因此这里通过了编译, 不然本类就需要实现两个方法才能成功编译
>   
>       @Override
>       public void foo() {
>           System.out.println("Implementing foo() in MyClass");
>           // 具体的实现代码
>       }
>   }
>   
>   public class Main {
>       public static void main(String[] args) {
>           MyClass mc = new MyClass();
>           mc.foo();
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
>   
>   public class Main {
>       public static void main(String[] args) {
>           MyClass mc = new MyClass();
>           mc.foo(1);
>           mc.foo("limou");
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
>       // 接口 B 继承了接口 A, 且没有重新定义 foo 方法
>   }
>   
>   interface C extends A {
>       // 接口 C 也继承了接口 A，但重新定义了 foo 方法，返回值类型不同
>       String foo(int x); // 但是这里的 x 类型就不允许是 int 了, 因此代码在这里编译不通过
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
>           return 1;
>       }
>   
>       @Override
>       public String foo(int x) {
>           System.out.println("Implementing foo(int) in MyClass");
>           return "1";
>       }
>   }
>   
>   public class Main {
>       public static void main(String[] args) {
>           MyClass mc = new MyClass();
>           mc.foo(1);
>           mc.foo("limou");
>       }
>   }
>   ```
>
>   另外，如果有两个接口内部的方法相同，且同时被一个类多实现，也无法通过编译。
>
>   ```java
>   // 类多实现相同的接口
>   interface A {
>       int foo(int x);
>   }
>   
>   interface B {
>       int foo(int x);
>   }
>   
>   class MyClass implements B, C { // 无法多实现
>       @Override
>       public int foo(int x) {
>           System.out.println("Implementing foo(int) in MyClass");
>           return 1;
>       }
>   
>       @Override
>       public int foo(int x) {
>           System.out.println("Implementing foo(int) in MyClass");
>           return 2;
>       }
>   }
>   
>   public class Main {
>       public static void main(String[] args) {
>           MyClass mc = new MyClass();
>           mc.foo(1);
>           mc.foo("limou");
>       }
>   }
>   ```

还有一种比较复杂的情况，就是接口和父类混合继承的情况，这是被 `Java` 所允许的。

```java
// 接口和父类混合继承
interface Printable {
    void print();
}

class Shape {
    private String color;

    public Shape(String color) {
        this.color = color;
    }

    public String getColor() {
        return color;
    }

    public void describe() {
        System.out.println("A shape with color " + color);
    }
}

class Rectangle extends Shape implements Printable {
    private double width;
    private double height;

    public Rectangle(String color, double width, double height) {
        super(color);
        this.width = width;
        this.height = height;
    }

    public double area() {
        return width * height;
    }

    @Override
    public void print() {
        System.out.println("Rectangle of width " + width + ", height " + height + ", color " + getColor() + ", and area " + area());
    }
}

public class Main {
    public static void main(String[] args) {
        Rectangle rect = new Rectangle("blue", 3, 4);
        rect.print();  // 输出: Rectangle of width 3.0, height 4.0, color blue, and area 12.0
        rect.describe();  // 输出: A shape with color blue
    }
}
```

>   补充：如果父类和接口中有冲突的方法，优先使用父类的（变相为实现了），有时间再研究，待补充...

## 1.4.接口的变化

在 `jdk8` 之后，接口也发生了进化，有些比较新的特性（对于 `jdk8` 之前来说）也值得我们学习。

-   **默认方法**：接口可以包含默认方法，在实现类中如果不进行实现，就会默认实现默认方法的内容
-   **静态方法**：接口可以包含静态方法，这些方法可以直接通过接口名称调用，不需要实例化接口的实现类
-   **私有方法**：接口可以包含私有方法，这些方法只能在接口内部被调用，不会暴露给接口的实现类或者外部代码（但是这是 `jdk9` 特性）

此外还有一个 **函数式接口** 的概念，如果在接口定义前加上 `@FunctionalInterface` 注解则代表接口只包含一个抽象方法的接口，通常用于支持 `Lambda` 表达式，该注解用来强制执行该特性，以确保接口只有一个抽象方法。

```java
// 定义一个函数式接口, 用于表示可计算面积的形状, 并确保只有一个抽象接口
@FunctionalInterface
interface Shape {
    // 抽象方法(计算面积)
    double calculateArea(); // 由于注解的存在, 有且只能有一个

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

>   补充：因此如果是我们之前的 `int[]` 数组使用 `Arrarys.sort()` 进行排序就要像下面这么写。
>
>   ```java
>   // 对之前 int[] 降序排序的补充
>   import java.util.Arrays;
>   import java.util.Collections;
>   
>   public class Main {
>       public static void main(String[] args) {
>           // int[] arr = {5, 2, 9, 1, 5, 6};
>           Integer[] arr = {5, 2, 9, 1, 5, 6}; // 这里必须修改为 Integer[], 否则会报错(不过我暂时也不太明白为什么...)
>   
>           Arrays.sort(arr); // 先进行升序排序
>           System.out.println("升排序后的数组: " + Arrays.toString(arr));
>   
>           Arrays.sort(arr, Collections.reverseOrder()); // 然后进行降序排序
>           System.out.println("降序排序后的数组: " + Arrays.toString(arr));
>       }
>   }
>   ```

# 2.Lamda 表达式

## 2.1.Lamda 表达式的使用

`Java` 的 `Lamda` 表达式的形式和 `Cpp` 不同，因此事先说明一下：**两者对于 Lamda 的理解很不一样**。

```java
// Java-Lamda
(参数类型 形式参数) -> { // 参数类型如果可以推导则不写
    代码体;
}
/* 如果只有一个参数且可推导, 甚至可以直接写参数名字不用写括号 */
```

```cpp
// Cpp-Lamda
[捕获参数](参数类型 形式参数) -> 返回类型 {
    代码体;
}
```

>   警告：这种不一样不仅仅体现在定义形式上，还体现在两种语言各自对 `Lamda` 的用法上。

## 2.2.Lamda 表达式的引用

方法引用是对 `Lamda` 表达式的进一步简化，后面用到再说，待补充...

# 3.回调方法

## 3.1.设置两对象之间大小关系

在之前提到 `Arrarys` 工具类的时候，有一个问题被我故意忽略了，那就是排序中不同类对象的排序问题，怎么使用 `sort()` 实现降序排序呢？

这里我们先假设我们有个简易的包装类 `Data{}`，对两个 `int` 类型的数据进行包装，如果一个对象 `d1` 中的两个 `int` 数据的和要比另外一个同类对象 `d2` 中两个 `int` 数据的和要大，那我就定义 `d1>d2`。相等则 `d1==d2`，小于则 `d1<d2`。现在要求一个存储 `Data` 的数组 `Data[]` 进行正向和逆向排序。

`Arrays.sort()` 承诺可以对对象的数组进行排序，但是需要对象所对应的类满足接口需求，也就是说必须实现 `Comparable` 接口。

```cpp
// 自定义类实现 Comparable 泛型接口中的 comparTo() 达到正序和逆序的目的
import java.util.Arrays;

class Data implements Comparable<Data> { // Comparable 接口在现在的实现中已经是一种泛型了, 内部的 compareTo() 的参数不再是旧版本的 Object 类型, 而是泛型, 根据实现类的不同而改变
    private int a;
    private int b;

    Data(int a, int b) {
        this.a = a;
        this.b = b;
    }

    public int getAAddB() {
        return this.a + this.b;
    }

    public int compareTo(Data data) { // 实现了 Comparable 接口, 就必须实现 compareTo() 方法, 这个方法的返回值必须是 int 类型
        // 返回值的含义是, 1 表示当前对象大于参数对象, 0 表示当前对象等于参数对象, -1 表示当前对象小于参数对象
        // return this.getAAddB() - data.getAAddB(); // 正序
        return data.getAAddB() - this.getAAddB(); // 逆序
    }

    @Override
    public String toString() {
        return this.getClass().getName()
                + "{a=" + this.a
                + ", b=" + this.b
                + ", a+b=" + (this.a + this.b)
                + "}";
    }
}

public class Main {
    public static void main(String[] args) {
        Data[] datas = new Data[5];
        datas[0] = new Data(7, 2);
        datas[1] = new Data(3, 0);
        datas[2] = new Data(5, 6);
        datas[3] = new Data(7, 8);
        datas[4] = new Data(6, 1);
        Arrays.sort(datas);
        System.out.println("数组内容: " + Arrays.toString(datas));

        // 但是我又希望正序排序了怎么办? 重新重写 compareTo() 么?
    }
}
```

但是，上述的做法至少有两个致命的问题。

-   对于我们前面使用的 `int[]` 数组中由于被自动装箱成了 `Integer` 包装类，因此像使用这种方法对 `int[]` 进行降序排序就需要修改包装类内部的 `compareTo()` 方法。但我们根本做不到，因为这个类是 `Java` 的核心类，不会允许用户任意修改，只允许用户调用
-   就算用户可以修改，为了实现降序就把对象间的大小关系改变了，那后续的代码怎么办呢？也会因为回调的设置导致大小关系逆转，这很危险！

因此我们得出结论，自己定义的类内部的确可以使用 `Comparable` 接口内部的 `compareTo()`，但是这么做更多是为了确定对象直接的大小关系，不适合用作指引排序。

另外，在实际实现规范中，也建议 `compareTo()` 的行为和 `equals()` 保持一致（少数情况除外，如大数 `x=1.00` 和 `y=1.0` 的比较就不适合这条规范，因为 `x.equals() == false` 因为精度不同，而 `x.compareTo(y)==0` 因为没有理由认为这两个数不相等）。

## 3.2.设置对象数组排序的规则

而 `Java` 给出的解决方案是，使用比较器，让用户自定义一个比较器，在排序的时候也传递给排序算法进行排序，而不是使用默认的对象比较方法 `compareTo()`。

因此，类内部实现的接口 `compareTo()` 就作为比较大小的规则，而排序时传递的比较器则不会修改这一规则，只是不调用默认的 `compareTo()` 而依照用户的请求进行比较然后排序。

```java
// 传递 "比较器" 帮助 sort() 进行逆序排序
import java.util.Arrays;
import java.util.Collections;

class Data implements Comparable<Data> {
    int a;
    int b;

    Data(int a, int b) {
        this.a = a;
        this.b = b;
    }

    public int getAAddB() {
        return this.a + this.b;
    }

    @Override
    public String toString() {
        return "Data{" +
                "a=" + a +
                ", b=" + b +
                ", a+b=" + (a + b) +
                "}";
    }

    @Override
    public int compareTo(Data o) {
        return this.getAAddB() - o.getAAddB(); // 定义类对象之间的大小规则
    }
}

public class Main {
    public static void main(String[] args) {
        Data[] datas = new Data[5];
        datas[0] = new Data(7, 2);
        datas[1] = new Data(3, 0);
        datas[2] = new Data(5, 6);
        datas[3] = new Data(7, 8);
        datas[4] = new Data(6, 1);

        // 正常进行降序排序
        Arrays.sort(datas, Collections.reverseOrder());
        System.out.println(Arrays.toString(datas));

        // 不会影响升序排序
        Arrays.sort(datas);
        System.out.println(Arrays.toString(datas));
    }
}
```

不过，在有了 `Lamda` 表达式后，`sort()` 还有另外一个支持 `Lamda` 表达式的重载版本。

```java
// 传递 "Lamda" 帮助 sort() 进行逆序排序
import java.util.Arrays;
import java.util.Collections;

class Data implements Comparable<Data> {
    int a;
    int b;

    Data(int a, int b) {
        this.a = a;
        this.b = b;
    }

    public int getAAddB() {
        return this.a + this.b;
    }

    @Override
    public String toString() {
        return "Data{" +
                "a=" + a +
                ", b=" + b +
                ", a+b=" + (a + b) +
                "}";
    }

    @Override
    public int compareTo(Data o) {
        return this.getAAddB() - o.getAAddB(); // 定义类对象之间的大小规则
    }

}

public class Main {
    public static void main(String[] args) {
        Data[] datas = new Data[5];
        datas[0] = new Data(7, 2);
        datas[1] = new Data(3, 0);
        datas[2] = new Data(5, 6);
        datas[3] = new Data(7, 8);
        datas[4] = new Data(6, 1);

        // 正常进行降序排序
        Arrays.sort(datas, (Data d1, Data d2) -> { return d2.getAAddB() - d1.getAAddB(); });
        System.out.println(Arrays.toString(datas));

        // 不会影响升序排序
        Arrays.sort(datas);
        System.out.println(Arrays.toString(datas));
    }
}
```

可以看到代码和前者一样简洁明了，`Lamda` 表达式转化为接口的行为。

>   补充：为什么使用 `Lamda` 是可行的？在 `Java` 中允许 `Lamda` 表达式可以转化为接口，这是 `Java` 的 `Lamda` 和 `Cpp` 的 `Lamda` 最大的不同。我们细看而比较器的原理，看看 `Lamda` 被转化为哪一个接口了。
>
>   ```java
>   // 比较器的原理
>   import java.util.Arrays;
>   import java.util.Comparator;
>   
>   class Data {
>       int a;
>       int b;
>   
>       Data(int a, int b) {
>           this.a = a;
>           this.b = b;
>       }
>   
>       public int getAAddB() {
>           return this.a + this.b;
>       }
>   
>       @Override
>       public String toString() {
>           return "Data{" +
>                   "a=" + a +
>                   ", b=" + b +
>                   ", a+b=" + (a + b) +
>                   "}";
>       }
>   }
>   
>   class DataComparator implements Comparator<Data> {
>       @Override
>       public int compare(Data d1, Data d2) { // 比较器的原理就是实现了这一个接口
>           return d1.getAAddB() - d2.getAAddB(); // 按照 a + b 值升序排序
>       }
>   }
>   
>   public class Main {
>       public static void main(String[] args) {
>           Data[] datas = new Data[5];
>           datas[0] = new Data(7, 2);
>           datas[1] = new Data(3, 0);
>           datas[2] = new Data(5, 6);
>           datas[3] = new Data(7, 8);
>           datas[4] = new Data(6, 1);
>   
>           // 使用自定义比较器进行升序排序
>           Arrays.sort(datas, new DataComparator());
>           System.out.println("升序排序结果: " + Arrays.toString(datas));
>   
>           // 使用Lambda表达式进行降序排序
>           Arrays.sort(datas, (d1, d2) -> d2.getAAddB() - d1.getAAddB()); // 实际上就是传递给了 compare 接口
>           System.out.println("降序排序结果: " + Arrays.toString(datas));
>       }
>   }
>   ```

>   补充：`Java` 一开始对实现 `Lamda` 犹豫不决，但是 **把 Lamda 作为接口的简易实现** 这一思路转变，使得 `Lamda` 在 `jdk8` 中被成功引入了。因此严格来说 `Java` 是没有通过 `Lamda` 表达式来增加函数类型的，`Java` 的设计者保持我们对接口概念的理解。
>
>   因此从某些意义上来说 `Java` 的 `Lamda` 和 `Cpp` 的 `Lamda` 是两个相似但是主要作用不太一样的东西。

>   补充：对于一些只要求实现一个方法的接口，可以提供一个 `Lamda` 表达式，这种接口也被称为 **函数式接口**，上面辅助 `sort()` 排序的 `Lamda` 表达式就是一种函数式接口。

## 3.3.设置定时器到来时的行为

定时器 `Timer{}` 的对象会定期执行相关的回调方法，这个方法有些时候很有用。但是回调方法需要使用定时器的人通过以下步骤来制定。

-   把接口 `ActionListener` 中的 `actionPerformed()` 交给实现类去实现
-   再把定时时间和实现类创建的对象传递给 `Timer` 的构造器构造出一个定时器对象
-   然后使用定时器对象的 `start()` 开始定时即可

虽然这个实现类的出现可能有点别扭，我们其实只是想要实现一个符合规范的回调方法而已，但是为了接口的实现必须使用一个类来实现，但这种方式确实可行（后面还有其他方案）。

```java
// 使用 "接口" 设置定时器的回调行为
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.time.Instant;
import javax.swing.Timer;

class TimePrinter implements ActionListener {
    public void actionPerformed(ActionEvent event) {
        System.out.println("A print work " + Instant.ofEpochMilli(event.getWhen())); // 打印一个信息和执行时间
        Toolkit.getDefaultToolkit().beep(); // 发出一声蜂鸣声
    }
}

public class Main {
    public static void main(String[] args) {
        Timer timer = new Timer(3000, new TimePrinter()); // 创建一个定时器，每秒钟执行一次
        timer.start(); // 启动定时器
        while (true); // 防止定时器还没到来程序就终止了
    }
}
```

同样可以使用 `Lamda` 表达式来转化为接口避免使用实现类创建对象。

```java
// 使用 "Lamdba" 设置定时器的回调行为
import java.awt.*;
import java.awt.event.ActionEvent;
import java.time.Instant;
import javax.swing.Timer;

public class Main {
    public static void main(String[] args) {
        Timer timer = new Timer(3000, (ActionEvent event) -> {
            System.out.println("A print work " + Instant.ofEpochMilli(event.getWhen())); // 打印一个信息和执行时间
            Toolkit.getDefaultToolkit().beep(); // 发出一声蜂鸣声
        }); // 创建一个定时器，每秒钟执行一次
        timer.start(); // 启动定时器
        while (true); // 防止定时器还没到来程序就终止了
    }
}
```

---

>   结语：...