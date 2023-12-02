仿函数的最大缺点是：

1.   如果命名不合规范并且没有给出注释，直接阅读难以理解（排序的调用过程中，不查看仿函数细节，则很难直接得知是升序排序还是降序排序）
2.   每次实现仿函数都需自己构建一个类

因此`C++`从其他语言借鉴了`lambda`表达式语法，让我们可以无需提前创建类，提高代码可读性。

# 1.lambda表达式的语法

`lambda`表达式的格式为：`[capture-list](parameters)mutable->return-type{statement}`

-   `capture-list`：捕捉列表，编译器识别`[]`判定是否为`lambda`函数，因此`[]`不可省略。而捕捉列表本身可以根据父域（对于`lambda`函数内的变量来说的父域）的上下文，获取变量供`lambde`函数使用
-   `parameters`：参数列表，和函数的参数列表基本一致，可以为空
-   `mutable`：这是一个关键字，意思为“可变的/易变的”，默认情况下，`lambda`函数总是类似`const`函数的行为（除非使用引用捕捉，就不会有`const`行为），而使用该关键字可以取消`const`行为，并且参数列表此时不能忽略
-   `return-type`：返回值类型，和普通函数基本类似，没有返回值时可以省略，若返回值明确也会自动推导，也可以省略
-   `statement`：函数体，内部可以使用参数列表中的参数和捕获的变量列表

在`lambda`函数定义中，也具有函数签名的特征，并且参数列表和返回值类型是可选部分，而捕捉列表和函数体可以为空。

因此最简单的`lambda`函数为`[]{}`，这个`lambda`函数不能做任何事情。

# 2.lambda表达式的运用

让我们尝试使用`lambda`表达式来书写一个`Add()`。

```cpp
#include <iostream>
using namespace std;
int main()
{
    auto add = [](int x, int y)->int { return x + y; };//整体代表一个 lambda 对象
    cout << [](int x, int y)->int { return x + y; }(1, 2) << endl;
    cout << add(1, 2) << endl;
    return 0;
}
```

而使用`lambda`表达式就可以替代仿函数在`sort()`中的使用：

```cpp
#include <iostream>
#include "Date.hpp" //以前写的 Date 类
using namespace std;
int main()
{
    vector<Date> v = { { 2013, 7, 9 }, { 2022, 6, 5 }, { 2023, 4, 17 } };
    auto less = [](const Date& d1, const Date& d2)->bool { return d1 < d2; };
    sort(v.begin(), v.end(), less);
    return 0;
}
```

那捕捉列表怎么用呢？假设我们需要一个交换函数，使用`lambda`有两种方式书写。

```cpp
#include <iostream>
using namespace std;
int main()
{
    //1.设置用于交换的数
    int x = 10, y = 5;
    cout << "x=" << x << " " << "y=" << y << '\n';

    //2.用于交换的 lambda 表达式（指针参数版）
    auto swap1 = [](int* px, int* py)
        {
            int tmp = *px;
            *px = *py;
            *py = tmp;
        };
    swap1(&x, &y);
    cout << "x=" << x << " " << "y=" << y << '\n';

    //3.用于交换的 lambda 表达式（引用参数版）
    auto swap2 = [](int& rx, int& ry)
        {
            int tmp = rx;
            rx = ry;
            ry = tmp;
        };
    swap2(x, y);
    cout << "x=" << x << " " << "y=" << y << '\n';

    //4.用于交换的 lambda 表达式（列表捕捉版）
    auto swap3 = [&x, &y]()
        {
            int temp = x;
            x = y;
            y = temp;
        };
    swap3();
    cout << "x=" << x << " " << "y=" << y << '\n';

    return 0;
}
```

而捕捉列表可以有多种语法形式来捕捉：

```cpp
//特定
auto func1 = [x, y](){};//对应变量传值捕捉
auto func2 = [&x, &y](){};//对应变量引用捕捉（需要注意不要误认为是取地址）
auto func3 = [&x, y](){};//对应变量引用+对应变量传值混合捕捉

//泛指
auto func4 = [&](){};//所有变量均可引用捕捉（包括 this）
auto func5 = [=](){};//所有变量均可传值捕捉（包括 this）
auto func6 = [&, x](){};//除了 x 参数是传值捕捉，其他都是引用捕捉
auto func7 = [this](){};//捕获当前的 this 指针
```

# 3.lambda表达式的误解

1.   `lambda`表达式的捕获列表只能捕获父域的变量，如果在父域内没有对应的变量，则会编译报错。注意，这个父域是对于`lambda`内部的来说的，包含`lambda`表达式所处的表达式

     ```cpp
     #include <iostream>
     using namespace std;
     int main()
     {
         //交换
         int x = 10, y = 5;
         cout << "x=" << x << " " << "y=" << y << '\n';
     
         do {
             do {
                 auto swap = [&x, &y]()
                     {
                         int temp = x;
                         x = y;
                         y = temp;
                     };
                 swap();
             } while (0);
         } while (0);
     
         cout << "x=" << x << " " << "y=" << y << '\n';
     
         return 0;
     }
     ```

2.   `lambda`表达式产生的`lambda`对象不允许相互赋值，这涉及到底层实现，不同的或者说哪怕相同的`lambda`表达式结构生成的对象的类型均不一样，并且在不同编译器上运行都不一样（有些编译器的实时可能会看不出来）

     ```cpp
     #include <iostream>
     using namespace std;
     int main()
     {
         //交换
         int x = 10, y = 5;
     
         auto swap1 = [&x, &y]()
             {
                 int temp = x;
                 x = y;
                 y = temp;
             };
     
         auto swap2 = [&x, &y]()
             {
                 int temp = x;
                 x = y;
                 y = temp;
             };
         
         cout << typeid(swap1).name() << '\n';
         cout << typeid(swap2).name() << '\n';
     
         //swap1 = swap2; //报错
     
         return 0;
     }
     ```

3.   `lambda`对象虽然不允许赋值，但是允许拷贝构造出一个新副本

     ```cpp
     #include <iostream>
     using namespace std;
     int main()
     {
         //交换
         int x = 10, y = 5;
     
         auto swap = [&x, &y]()
             {
                 int temp = x;
                 x = y;
                 y = temp;
             };
         
         auto swapCopy(swap);
     
         cout << typeid(swap).name() << '\n';
         cout << typeid(swapCopy).name() << '\n';
     
         return 0;
     }
     ```

4.   可以将`lambda`表达式赋值给和`lambda`返回值相同类型的函数指针（但是带有捕捉列表的`lambda`表达式不允许这么做）

     ```cpp
     #include <iostream>
     using namespace std;
     void(*p)(int, int);
     int main()
     {
         //交换
         int x = 10, y = 5;
     
         auto swap = [](int x, int y)
             {
                 cout << "x = " << x << '\n';
                 cout << "y = " << y << '\n';
             };
         
         p = swap;
         (*p)(x, y);
     
         return 0;
     }
     ```

     ```cpp
     #include <iostream>
     using namespace std;
     
     int main()
     {
         //交换
         int x = 10, y = 5;
     
         //lambda表达式没有捕获列表，可以直接赋值给函数指针
         auto func1 = []() { cout << "Hello, World!" << endl; };
         void(*ptr1)() = func1;
         ptr1(); //输出：Hello, World!
     
         //lambda表达式带有捕获列表，无法直接赋值给函数指针
         //int(*ptr2)() = [x, &y]() { return x + y; }; //编译错误
     
         return 0;
     }
     ```

5.   如果是引用捕捉，无需使用关键字`mutable`也可以在`lambda`表达式内修改。也就是说，捕捉变量实际上是一个变量拷贝了父域的变量，而引用则直接使用该变量（不过，如果一个是引用捕捉一个传值引用，就还是需要加上`mutable`）

     ```cpp
     #include <iostream>
     using namespace std;
     int main()
     {
         //交换
         int x = 10, y = 5;
     
         //auto func = [&x, y]()
         //    {
         //        x = 0;
         //        y = 0;//出错，不允许改变
         //    };
     
         auto func = [&x, y]()mutable //必须使用 mutable
             {
                 x = 0;
                 y = 0;//只是改变了局部变量
             };
         
         func();
         cout << "x = " << x << endl; //输出 x = 0
         cout << "y = " << y << endl; //输出 y = 5
     
         return 0;
     }
     ```

6.   `this`的传值可以让`lambda`表达式成为类似成员函数的存在写入类中

     ```cpp
     #include <iostream>
     using namespace std;
     
     class Data
     {
     public:
         void function()
         {
             auto func = [this]()
                 {
                     cout << "x = " << x << ", y = " << y << '\n';
                     x = 100;
                     y = 500;
                     cout << "x = " << x << ", y = " << y << '\n';
                 };
             func();
         }
     
     private:
         int x = 10;
         int y = 50;
     };
     int main()
     {
         Data d;
         d.function();
         return 0;
     }
     ```

# 4.lambda表达式的疑问

我们的确可以使用`lambda`表达式解决了使用`sort(v.begin(), v.end(), less)`的问题，但是我们还有一种情况会使用仿函数，就是在使用`map`的时候会写出`map<string, int, 仿函数类类型>`，对最后的仿函数需要将`string`转化为`key`，但是仿函数的类型我们尚且可以知道，那么`lambda`怎么办呢？

`lambda`的类型比较难以获取，并且还是匿名的，尽管有些其他的办法获取类型，但是我们更加推荐使用包装类`function{}`，这些我们下一篇介绍。
