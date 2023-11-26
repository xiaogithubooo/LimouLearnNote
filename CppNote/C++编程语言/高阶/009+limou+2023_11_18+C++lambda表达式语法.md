仿函数的最大缺点是：如果命名不合规范并且没有给出注释，直接阅读排序的调用过程而不查看仿函数细节，则很难直接得知是升序排序还是降序排序，因此`C++`从其他语言借鉴了`lambda`表达式语法。

# 1.lambda表达式语法

`lambda`表达式的格式为：`[capture-list](parameters)mutable->return-type{statement}`

-   `capture-list`：捕捉列表
-   `parameters`：参数列表
-   `mutable`：异变
-   `return-type`：返回值类型
-   `statement`：函数体

在`lambda`函数定义中，也具有函数签名的特征，并且参数列表和返回值类型是可选部分，而捕捉列表和函数体可以为空。因此最简单的`lambda`函数为`[]{}`，这个`lambda`函数不能做任何事情。

# 2.lambda表达式运用

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
int main()
{
    int x = 10, y = 5;
    
    auto swap1 = [](int& rx, int& ry)
    {
        int tmp = rx;
        rx = ry;
        ry = tmp;
    };
    swap1(x, y);
    
    //使用捕捉列表直接捕捉参数
    auto swap2 = [&x, &y]() //直接给值就叫传值拷贝捕捉，默认这份拷贝不可修改。加上 mutable 就可以修改了，但是还不够，只是内部可以修改，还是拷贝。因此还是使用引用，注意不要和取地址混淆 
    {
        int temp = x;
        x = y;
        y = temp;
    };
    
    return 0;
}
```

而捕捉列表可以有多种语法形式来捕捉：

```cpp
auto func1 = [x, y](){};//传值捕捉
auto func2 = [&x, y](){};//混合捕捉
auto func3 = [&](){};//全部引用捕捉
auto func4 = [=](){};//全部传值捕捉
auto func5 = [&, x](){};//除了 x 传值捕捉，其他都是引用捕捉
```
