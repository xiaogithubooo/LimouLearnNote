关于`const`，我曾因为其在`C`和`C++`中的不同，还有一些编译器对未定义的处理不同，搞得晕头转向...

后来有次在阅读《C primer plus》中我终于梳理了有关`const`的结论。

# 1.const基础知识

首先要明白`const`的难点在于修饰指针的时候，对于`const int* const ptr;`来说：

1. 前一个`const`修饰`*ptr`，不能将`*ptr`的值改变

2. 后一个`const`修饰`ptr`，不能将`ptr`的值改变

# 2.const基本规则

在`C`中：

1. 把`const`数据或者`非const`数据的地址，“初始化”或者“赋值给”指向`const`的指针是合法的

2. 只能把`非const`数据的地址，“赋值给”`非const`的指针

3. 规定使用`非const`标识符修改`const`数据导致的结果是未定义的，编译器遇到这样的代码时可能给出警告（因为这样子就可以通过新的指针改变`const`指针指向的数据）

```c
//演示规则1
void test_1()
{
    double arr1[3] = { 1.2,3.2,3.14 };//非const
    const double arr2[4] = { 0.12,1.2,3.2,3.14 };//const
    const double* pnc = arr1;//有效，初始化
    pnc = &arr1[1];//有效，赋值
    pnc = arr2;//有效，赋值

    int i = 10;
    int* p = &i;
    const int** pp = &p;
    *pp
}
```

```c
//演示规则2
void test_2()
{
    double arr1[3] = { 1.2,3.2,3.14 };
    const double arr2[4] = { 0.12,1.2,3.2,3.14 };
    double* pnc = arr1;//有效，初始化
    pnc = &arr1[1];//有效，赋值
    pnc = arr2;//无效，并且VS2022给出警告
}
```

```c
//演示未定义行为
#include <stdio.h>
int main()
{
    const int i = 10;//不可修改i
    const int* pi = &i;//不可修改(*pi)
    int* temp = pi;
    *temp = 20;
    printf("%d\n", i);
    return 0;
}
//在vs2022的17.7.0版本中，这样做是可以的，输出20
//在CentOS7下的gcc的4.8.5版本中，直接不给编译进行报错
```

> 吐槽：是谁造谣可以用`非const`指针绕过去修改`const`数据的！？啊？`VS2022`可以做到？那我们换个平台试试。
> 
> ![17cee8d12cab734f6f916474a9f2250](C:\Users\LIMOU_~1\AppData\Local\Temp\WeChat%20Files\17cee8d12cab734f6f916474a9f2250.png)
> 
> 你动下试试？类型检查这一方面直接就过不了关...


