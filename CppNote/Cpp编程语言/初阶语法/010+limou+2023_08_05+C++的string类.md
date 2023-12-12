# 1.string简介

`string`不是`STL`的一部分（`STL`的诞生较晚），但是和`STL`一起学习会更加容易融会贯通。

而实际上`string`是一个类模板`basic_string`的一个实例化，使用字符的顺序容器实现（也就是字符的顺序表），`string`整个系列支持`char`的动态增长（字符编码有几篇文章值得看一下，是耗子哥推荐的）。

> String are objects represent sequences of characters.（字符串是表示字符序列的对象。）

`string`实际上是由`basic_traits`类实例化而来

```c++
typedef basic_string<char> string;
```

> `basic_string`的实例化除了`string（单字节）`，还有`wstring(宽字符、双字节)`、`u16string(16位字符，双字节)`、`u32string(32位字符，四字节)`，它们各自维护的类型是`char`、`wchar_t`、`char16_t`、`char32_t`，这些是属于编码的问题，不过最常用的还是`string`类。

在`string`类中至少有三个成员变量：

```c++
class string
{
private:
    char* _str;//指向由new开辟出来的动态空间，该空间存储字符串
    size_t _size;//string对象对应字符串的大小
    size_t _capacity;//string对象的容量，在存储的时候会多存储一个空字符，方便转化为C风格的字符串，在不同的环境扩容机制有些许不同，有的直接2倍，有的1.5倍
};
```

# 2.类构造函数

## 2.1.string()

调用构造函数后，会构造一个长度为`0`的空字符串（但是有一定的容量）。

```c++
#include <iostream>
#include <string>

int main() 
{
    std::string str;
    //使用范围for循环遍历字符串中的每个字符
    std::cout << "大小 = " << str.size() << std::endl;
    std::cout << "容量 = " << str.capacity() << std::endl;
    //size()是string类内部的成员函数，
    //可以求得string对象内部存储的字符串大小，
    //而capacity()则是求得容量大小
    //后续还会继续讲解

    return 0;
}
```

## 2.2.string (const string& str)

构造一个`str`的副本，也就是复制`str`构造一个新的`string`对象。

## 2.3.string (const string& str, size_t pos, size_t len = npos)

复制`string`对象的部分的字符（从`pos`开始到`len`的部分）来构造一个新的`string`对象。

或者复制到`str`结束部分（即：`str`太短了或者`len`值为`string::pos`）。

> 补充：什么是`string::pos`呢？`string::pos`实际上是`string`内部的常量成员，下面是其定义：
> 
> ```c++
> static const size_t npos = -1;
> ```
> 
> 我们知道`size_t`是无符号整数类型的别名，因此`npos`取得了有符号的一个最大值，这基本不可能是`str`的长度，也可能是字符串的索引值。

`len`默认值为`npos`的目的是：如果没有给予`len`的值，将会一直读取到字符串的末尾，这一点细节我们实现一个简易的`string`类再来细细探究。

## 2.4.string (const char* s)

复制`s`指针指向的字符序列（以空字符`\0`结尾的`C`语言风格的字符序列）构造出一个`string`对象。

## 2.5.string (const char* s, size_t n)

从`s`指向的字符数组中复制前`n`个字符。

## 2.6.string (size_t n, char c)

用`n`个相同得`c`字符连续填充字符串。

## 2.7.template \<class InputIterator\> string (InputIterator first, InputIterator last)

以和原`string`对象相同的顺序，来复制原`string`对象中范围为`[first,last)`（迭代器）的字符序列，构造新的`string`对象。由于我们还没有学过迭代器，因此我们直接上代码观看迭代器的时候会更加好一下。

```c++
#include <iostream>
#include <string>
int main()
{
    //直接初始化一个字符串
    std::string str1("Hello World");
    std::cout << "str1(\"Hello World\") = "
        << str1 << std::endl;
    //使用迭代器范围构造一个新的字符串
    std::string str2(str1.begin(), str1.begin() + 7);

    //输出：Hello
    std::cout << "str2(str1.begin(), str1.begin() + 7) = " 
        << str2 << std::endl;  
    return 0;
}
```

这里`begin()`就是迭代器的使用，您可以简单理解为有一个指针，指向了这个`string`对象的字符串开头位置，迭代器的出现就是为了能是使得一些对象可以类似指针一样使用。

## 2.8.string (initializer_list\<char\> il)

以列表`il`中的每个字符来构造`string`对象，这和使用`string(const string& str)`构造有些许不同。

```c++
#include <iostream>
#include <string>

int main()
{
    //使用列表方式初始化字符串
    std::string str{ "Hello" };
    //输出：Hello
    std::cout << "str: " << str << std::endl;

    return 0;
}
```

> 补充：std::initializer_list 是一个模板类，它允许创建一个包含任意数量元素的列表。它类似于数组，可以通过下标访问其中的元素，但与数组不同的是，std::initializer_list 是不可变的，不能进行元素的添加或删除操作。（但是这不是初始化列表的意思！）

## 2.9.string (string&& str) noexcept

这个函数被称为“移动构造”，可以将源`string`对象转移到目标`string`对象中。

移动构造函数通常是浅拷贝，它将源对象的资源所有权转移给目标对象，而不是创建新的资源副本。这种机制能够提高性能，并确保在源对象析构时不会重复释放资源。

下面代码如果没能看懂可以以后再来查看：

```c++
#include <iostream>
#include <string>
int main()
{
    //使用构造函数构造源string对象
    std::string source = "Hello World";

    //使用移动构造函数转移资源
    std::string target(std::move(source));

    std::cout << "target = " << target << std::endl;//输出：target = Hello World
    std::cout << "source = " << source << std::endl;//输出：source = （source的内容已被移动）

    return 0;
}
```

> 注意：源对象的状态会变为有效但未指定的状态，并且应该避免继续使用移动后的对象或访问其值，上述代码在大部分编译器（比如VS2022）中有可能在打印`source`的时候提示出类似下面的警告：`使用已移动的 from 对象: source (lifetime.1)`。

## 2.10.英文备注

> 以下是参考[`CPlusPlus`网站](https://c.binjie.fun/#/chat/1690448482357)的英文解释
> 
> (1) empty string constructor (default constructor)
> 
> Constructs an [empty](https://legacy.cplusplus.com/string::empty) string, with a [length](https://legacy.cplusplus.com/string::length) of zero characters.
> 
> (2) copy constructor
> 
> Constructs a copy of _str_.
> 
> (3) substring constructor
> 
> Copies the portion of _str_ that begins at the character position _pos_ and spans _len_ characters (or until the end of _str_, if either _str_ is too short or if _len_ is [string::npos](https://legacy.cplusplus.com/string::npos)).
> 
> (4) from c-string
> 
> Copies the null-terminated character sequence (C-string) pointed by _s_.
> 
> (5) from buffer
> 
> Copies the first _n_ characters from the array of characters pointed by _s_.
> 
> (6) fill constructor
> 
> Fills the string with _n_ consecutive copies of character _c_.
> 
> (7) range constructor
> 
> Copies the sequence of characters in the range [first,last), in the same order.
> 
> (8) initializer list
> 
> Copies each of the characters in _il_, in the same order.
> 
> (9) move constructor
> 
> Acquires the contents of _str_.  
> 
> _str_ is left in an unspecified but valid state.

# 3.类析构函数

`~string()`会释放所有存储容量（该存储容量由字符串所使用的分配器分配）。

也就是说`string`类的析构函数`~string()`主要是释放掉`string`类产生的`string`对象的资源，因为`string`的本质是一个顺序表。

# 4.赋值符号重载函数

| 类别                   | 函数声明                                             |
| -------------------- | ------------------------------------------------ |
| string (1)           | `string& operator= (const string& str);`         |
| c-string (2)         | `string& operator= (const char* s);`             |
| character (3)        | `string& operator= (char c);`                    |
| initializer list (4) | `string& operator= (initializer_list<char> il);` |
| move (5)             | `string& operator= (string&& str) noexcept;`     |

上述的作用基本都是将一个新值赋给已定义后的`string`对象，来替换其当前`string`对象的内容。下面是测试例子：

```c++
#include <iostream>
#include <string>

int main()
{
    //创建多个string对象
    std::string str1, str2, str3, str4;
    str1 = "hello";                        //赋值C风格的字符串(2)
    std::cout << str1 << std::endl;

    str2 = 'x';                            //赋值单字符(3)
    std::cout << str2 << std::endl;

    str3 = str1 + str2;                    //赋值string对象(1)
    std::cout << str3 << std::endl;

    str3 = { 'H', 'E', 'L', 'L', 'O' };    //初始化列表(4)
    std::cout << str3 << std::endl;

    str4 = std::move(str3);                //移动构造(5)
    std::cout << str3 << std::endl;
    std::cout << str4 << std::endl;

    return 0;
}
```

其中初始化列表函数和普通的赋值只是内部实现细节有点区别，其他地方都很类似。

最后一个函数您若是没有看懂，暂且先用着，我们以后再来解释……

# 5.迭代器（Iterators）

如果需要遍历一个`string`对象需要怎么做呢？目前我们有多种方法可以遍历。

| 类别    | 可读可修改                                                                         | 可读不可修改                                                                                               |
| ----- | ----------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| 正向迭代器 | `iterator begin() noexcept;`<br>`iterator end() noexcept;`                    | `const_iterator begin() const noexcept;`<br>`const_iterator end() const noexcept;`                   |
| 反向迭代器 | `reverse_iterator rbegin() noexcept; `<br>`reverse_iterator rend() noexcept;` | `const_reverse_iterator rbegin() const noexcept;`<br>`const_reverse_iterator rend() const noexcept;` |

| 类别         | 函数声明                                                                                                   |
| ---------- | ------------------------------------------------------------------------------------------------------ |
| 普通对象的常量迭代器 | `const_iterator cbegin() const noexcept;`<br>`const_iterator cend() const noexcept;`                   |
| 常量对象的常量迭代器 | `const_reverse_iterator crbegin() const noexcept;`<br>`const_reverse_iterator crend() const noexcept;` |

其中`iterator`是一个类模板：

```c++
template <class T>//这里假设T实例化为string 
class iterator 
{ 
    //1.正向迭代器
    //迭代器的成员函数和操作符重载定义
    iterator begin() noexcept;//可以找到string对象的头
    const_iterator begin() const noexcept;//可以找到const string对象的头
    iterator end() noexcept;//可以找到string对象的尾
    const_iterator end() const noexcept;//可以找到const string对象的尾

//2.反向迭代器
    reverse_iterator rbegin() noexcept;
    const_reverse_iterator rbegin() const noexcept;
    reverse_iterator rend() noexcept;
    const_reverse_iterator rend() const noexcept;
    //...
};
```

## 5.1.正向迭代器：begin()和end()

### 5.1.1.for遍历

这里会使用`string`类的成员函数`size()`，这个函数专门计算`string`对象的大小，这在后面讲解`string`类的成员函数时，还会进行讲解。

```c++
#include <iostream>
#include <string>
int main()
{
    std::string str("abcd");
    for (int i = 0; i < str.size(); i++)
    {
        std::cout << str[i] << " ";//[]运算符重载
    }
    return 0;
}
```

还需要注意的是，`[]`运算符重载使得`string`对象能够像数组一样遍历，我们之后还会再提一次。

### 5.1.2.迭代器

迭代器的行为十分类似指针（但是不是指针）使得`string`对象可以像数组一样被使用：

```c++
#include <iostream>
#include <string>
int main()
{
    std::string str("abcd");
    std::string::iterator it = str.begin();//这里如果类型名字太长，可以使用auto来自动推导
    while (it != str.end())//这里不推荐使用'<'符号
    {
        std::cout << *it << " ";
        (*it)++;
        it++;
    }
    std::cout << std::endl << str << std::endl;
    return 0;
}
```

在`while (it != str.end())`这里不推荐使用`<`符号，尽管在`string`对象里使用或许不会出错，但是在其他类里可能就不满足了。（之前说过迭代器的行为类似指针，因此`string`的底层很有可能就是指针，因此使用`<`不会出太多错）

但是迭代器不仅仅是`string`类使用，基本所有库里的类都可以使用迭代器遍历，迭代器有几种便利之处：

1. 通用，在一些不支持`[]`重载的结构也可使用迭代器

2. 是无需考虑差`1`问题

3. 屏蔽底层实现细节，可以直接使用

而其他类底层的迭代器是靠纯指针实现的，例如：链表，因此使用`<`会有风险。

实际上迭代器就是`C++`封装的一种体现。

### 5.1.3.范围for

```c++
#include <iostream>
#include <string>

int main() 
{
    std::string str = "Hello";
    //使用范围for循环遍历字符串中的每个字符
    for (auto ch : str) 
    {
        std::cout << ch << " ";
    }
    return 0;
}
```

实际上范围`for`的底层代码就是迭代器，只不过范围`for`封装得更加厉害罢了（在一些编译器内部调试的时候可以切到范围`for`的汇编语句来检验两者关系）。

## 5.2.反向迭代器：rbegin()和rend()

```c++
#include <iostream>
#include <string>

int main()
{
    std::string str("abcd");
    std::string::reverse_iterator rit = str.rbegin();
    while (rit != str.rend())
    {
        std::cout << *rit << " ";
        (*rit)++;
        rit++;//注意依旧是++，而不是--
    }
    std::cout << std::endl << str << std::endl;
    return 0;
}
```

## 5.3.常量迭代器

常量迭代器也没有什么声明的，只是将上述两种迭代器从“可读可写”设置为“只可读不可写”，这点我们就不再深入讲解了。

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    //const string str1 = "abcdef";
    //auto it = str1.begin();
    //while (it != str1.end())
    //{
    //    cout << (*it)++ << " ";
    //
    //    it++;
    //}
    //cout << endl;
    string str2 = "abcdef";
    auto cit = str2.cbegin();
    while (cit != str2.cend())
    {
        cout << *cit << " ";
        cit++;
    }
    cout << endl;
    return 0;
}
```

# 6.容积（Capacity）

| 类别         | 函数声明                                                                                                        |
|:---------- | ----------------------------------------------------------------------------------------------------------- |
| 获取长度和容量    | `size_t size() const noexcept;`<br>`size_t length() const noexcept;`<br>`size_t capacity() const noexcept;` |
| 改变长度和容量    | `void resize (size_t n);`<br>`void resize (size_t n, char c);`<br>`void reserve (size_t n = 0);`            |
| 获取环境最大容量数值 | `size_t max_size() const noexcept;`                                                                         |
| 清理对象内容     | `void clear() noexcept;`                                                                                    |
| 判空         | `bool empty() const noexcept;`                                                                              |

## 6.1.size()和length()和capacity()

首先我们有一个问题，为什么会有两个计算长度的成员函数呢？这是因为`string`类得诞生比`STL`早，最开始设计的是`length()`，后来出现了`STL`才开始使用了（并且建议使用）`size()`。两个都可以计算`string`对象内部字符串长度，在功能上是等效的。

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    string str("abcdefghijklmn");
    cout << str.size() << endl;
    cout << str.length() << endl;
    return 0;
}
```

> 补充：如果查看其他的结构会发现都是使用`size()`，而没有`length()`

而另外一个函数`capacity()`则可以求得当前`string`对象的容量。

## 6.2.clear()

这个成员函数会对string对象的字符串作清理，但是不会释放空间，同时原`string`的容量不变，对象的`siez()`值置为空。

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    string str("abcdefghijklmn");
    cout << str << endl;
    cout << str.size() << endl;
    cout << str.capacity() << endl;
    str.clear();
    cout << str << endl;
    cout << str.size() << endl;
    cout << str.length() << endl;

    return 0;
}
```

## 6.3.max_size()

这个成员函数用于告知用户该环境下的`string`对象最多可以申请到多少字节的空间。

但是这个值是写死的，只是在不同环境下会不同罢了，因此基本没怎么用到这个。

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    string str1("abcdefghijklmn");
    cout << str1.max_size() << endl;
    string str2("abcdefghijklmn");
    cout << str2.max_size() << endl;
    return 0;
}
```

## 6.4.resize()

`resize()`可以将`string`对象的大小（即：`size()`的值），有以下集几种情况：

1. 如果`n`小于`string`对象原有的大小，则删除`n`个字符以外的字符（这个操作比较危险）

2. 如果`n`大于`string`对象原有的大小，则尾插任意字符（默认表现为空字符`\0`）扩充到当前的内容，使得大小达到`n`。因此如果有填充某个字符串的需求，也是可以用这个函数的

3. 如果指定了字符`c`，则多开辟的空间初始化为字符`c`

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    //1.创建一个"abcde"为内容的string对象，size()值为5，capacity()默认值为15
    string str("abcde");
    cout << str << endl;
    cout << str.size() << endl;
    cout << str.capacity() << endl << endl;
    //2.调整size()值为20的string对象，而在VS2022中表现为填充15个空字符'\0'，而空字符是没有打印出来的
    str.resize(20);
    cout << str << endl;
    cout << str.size() << endl;
    cout << str.capacity() << endl << endl;

    //3.调整size()值为30的string对象，因此：前5个字符为"abcde"，后面有15个'\0'，并且还有10个'c'字符，整体打印出"abcdecccccccccc"
    str.resize(30, 'c');
    cout << str << endl;
    cout << str.size() << endl;
    cout << str.capacity() << endl << endl;

    //4.缩小size()值，并且可以看到，扩容的空间依旧有效
    str.resize(5);
    cout << str << endl;
    cout << str.size() << endl;
    cout << str.capacity() << endl << endl;

    return 0;
}
```

## 6.5.reserve()

这个函数请求改变容量，要求将`string`对象调整到大小为`n`的容量（或者比`n`更大）这个函数对字符串的长度没有影响，也不能改变它的内容。

如果使用者做了缩小容量的操作，并且影响到原有`string`对象字符串的存储时，该函数会拒绝请求（即：`non-binding`无约束力的）。

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    //1.创建一个"abcde"为内容的string对象，size()值为5，capacity()默认值为15
    string str("abcde");
    cout << str << endl;
    cout << str.size() << endl;
    cout << str.capacity() << endl << endl;
    //2.调整capacity()值为20的string对象
    str.reserve(20);
    cout << str << endl;
    cout << str.size() << endl;
    cout << str.capacity() << endl << endl;

    //3.调整capacity()值为30的string对象
    str.reserve(30);
    cout << str << endl;
    cout << str.size() << endl;
    cout << str.capacity() << endl << endl;

    //4.减小capacity()值为4的string对象（失败请求）
    str.reserve(4);
    cout << str << endl;
    cout << str.size() << endl;
    cout << str.capacity() << endl << endl;

    //4.增大capacity()值为10的string对象
    str.reserve(10);
    cout << str << endl;
    cout << str.size() << endl;
    cout << str.capacity() << endl << endl;

    return 0;
}
```

> 注意：函数名`reserve`有时候会和单词`reversal`（反转）搞混，这个注意一下就行……

## 6.6.empty()

该函数返回字符串是否为空（即：`size()`是否为0)，这个函数不会以任何方式修改字符串的值。

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    string str("abcdef");
    cout << str.empty() << endl;
    str.resize(0);//为空返回true
    cout << str.empty() << endl;
    return 0;
}
```

## 6.7.shrink_to_fit()

`shrink to fit`这个词的意思就是“缩小以适应”，该函数请求减少容量`capacity`来适应大小`size`，当然这只是一种请求（即：`non-binding`无约束力的）。

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    string str("abcde");
    cout << str << endl;
    cout << str.size() << endl;
    cout << str.capacity() << endl << endl;
    str.reserve(1000);//明明大小只有5，容量确高达1000多，浪费资源
    cout << str << endl;
    cout << str.size() << endl;
    cout << str.capacity() << endl << endl;

    str.shrink_to_fit();//使用该函数请求缩小容量（非强制）
    cout << str << endl;
    cout << str.size() << endl;
    cout << str.capacity() << endl << endl;

    return 0;
}
```

# 7.访问元素（Element access）

## 7.1.operator[]

```c++
//重载形式
char& operator[] (size_t pos);
const char& operator[] (size_t pos) const;
```

`pos`的起始位置为`0`，这意味这和数组的使用形式类似。

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    string str("abcdef");
    for (int i = 0; str[i]; i++)
    {
        cout << str[i] << " ";
    }
    return 0;
}
```

如果`pos`等于`string`对象的`size`值，这个符号重载函数就会返回一个指向空字符的引用，该字符位于`string`对象最后一个字符之后（这个字符不可被修改，只所以可以被允许访问的其中一个原因就是方便写循环终止条件，就像上面的循环例子）。

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    string str("abcdef");
    cout << "空字符->[" << str[str.size()] << "]" << endl;//可以看到空字符没有办法打印出来
    return 0;
}
```

## 7.2.at()

```c++
//重载声明
char& at (size_t pos);
const char& at (size_t pos) const;
```

该函数可以获取字符串中的字符，返回一个`pos`指向字符的引用，该函数自动检查`pos`是否是`string`对象中合法的字符索引，非法则抛出异常。

那么`at()`和`[]`有什么区别呢？答案在于自动检查出现非法时，例如下面的代码：

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    try
    {
        string str("abcdef");
        cout << "str = " << str << endl;
        cout << "str.size() = " << str.size() << endl;
        //cout << "str[7] = " << str[7] << endl;//第一情况
        //cout << "at(7) = " << str.at(7) << endl;//第二情况
    }
    catch (const std::out_of_range& e)//出现异常时执行下面代码
    {
        cout << "error" << endl;
    }
    return 0;
}
```

使用`[]`的报错和使用`at`的报错不一样。总的来说，抛异常会更加安全（并且抛异常是面向对象语言的一个显著特征）具体的抛异常细节我们以后再来学习……

## 7.3.back()

```c++
//重载声明
char& back();
const char& back() const;
```

该函数返回最后一个有效字符的引用（不是指空字符），此函数不能再空`string`对象上调用。

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    string str1("abcdef");
    cout << "str1 = " << str1 << endl;
    cout << "str1.size() = " << str1.size() << endl;
    cout << "str1.back() = " << str1.back() << endl;
    string str2("");
    cout << "str2 = " << str2 << endl;
    cout << "str2.size() = " << str2.size() << endl;
    //cout << "str2.back() = " << str2.back() << endl;//会出现报错
    return 0;
}
```

## 7.4.front()

```c++
char& front();
const char& front() const;
```

类似`end()`的使用，不过获取到字符是开头的字符。

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    string str1("abcdef");
    cout << "str1 = " << str1 << endl;
    cout << "str1.size() = " << str1.size() << endl;
    cout << "str1.front() = " << str1.front() << endl;
    string str2("");
    cout << "str2 = " << str2 << endl;
    cout << "str2.size() = " << str2.size() << endl;
    //cout << "str2.front() = " << str2.front() << endl;//直接会出现报错
    return 0;
}
```

# 8.修改器（Modifiers）

## 8.1.operator+=()

用得比较多。

```c++
//重载声明
string& operator+= (const string& str);
string& operator+= (const char* s);
string& operator+= (char c);
```

## 8.2.append()

尾插，即便有很多重载，但是用的比较少，这里只给出声明，您可以自己玩玩看。

```c++
//重载声明
string& append (const string& str);

string& append (const string& str, size_t subpos, size_t sublen);

string& append (const char* s);

string& append (const char* s, size_t n);

string& append (size_t n, char c);

template <class InputIterator>  
string& append (InputIterator first, InputIterator last);

string& append (initializer_list<char> il);
```

## 8.3.push_back ()

尾插单字符，爷用的比较少，您也可以自己玩玩。

```c++
//函数声明
void push_back (char c);
```

添加字符`c`到`string`对象的末尾，并且`size()`长度`+1`。

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    string str("abcdef");
    cout << "str = " << str << endl;
    cout << "str.size() = " << str.size() << endl;
    str.push_back('x');
    cout << "str = " << str << endl;
    cout << "str.size() = " << str.size() << endl;
    return 0;
}
```

可以拿尾插类的函数来测试环境的扩容机制：

```c++
#include <iostream>
#include <string>
using namespace std;
#define NUMBER 100
void test()
{
    string str;
    size_t old = str.capacity();
    cout << "Init:" << old << endl;
    size_t newValue = 0;
    for (size_t i = 0; i < NUMBER; i++)
    {
        str.push_back('x');//推送
        newValue = str.capacity();//记录新值
        if (newValue != old)//新值和旧值对比
        {
            cout << "dilatation = " << newValue << " ";
            old = newValue;
            cout << endl;
        }
    }
    cout << endl;
}
int main()
{
    test();
    return 0;
}
```

## 8.4.instert()

```c++
//重载声明
string& insert (size_t pos, const string& str);//插入string对象内的字符串

string& insert (size_t pos, const string& str, size_t subpos, size_t sublen);//插入子串，注意这里后两个参数不是输入子串的首尾索引，而是输入子串的起始索引和子串长度

string& insert (size_t pos, const char* s);//插入C风格的字符串

string& insert (size_t pos, const char* s, size_t n);////插入C风格的字符串，并且限定长度

string& insert (size_t pos, size_t n, char c);//重复插入n个c字符

iterator insert (const_iterator p, size_t n, char c);//使用迭代器来，插入重复的字符，并返回指向第一个插入元素的迭代器

iterator insert (const_iterator p, char c);//使用迭代定位，插入一个字符，并返回指向第一个插入元素的迭代器

template <class InputIterator>
iterator insert (iterator p, InputIterator first, InputIterator last);//将迭代器[first, last)的字符插入到目标string对象的迭代器定位处

string& insert (const_iterator p, initializer_list<char> il);//在迭代器定位处插入字符序列
```

可以看到这个函数有很多重载版本，让我们来用一个测试例子研究一下，不过这个函数能不用就最好别用，效率不高。

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    cout << "1.string& insert (size_t pos, const string& str);" << endl;
    string str1("abcdef");
    cout << str1 << endl;
    string sstr1 = "xxxxx";
    str1.insert(0, sstr1);
    cout << str1 << endl << endl;
    cout << "2.string& insert (size_t pos, const string& str, size_t subpos, size_t sublen);" << endl;
    string str2("abcdef");
    cout << str2 << endl;
    string sstr2 = "ABCDEEEEFGHIJK";
    str2.insert(str2.size(), sstr2, 4, 4);
    cout << str2 << endl << endl;

    cout << "3.string& insert (size_t pos, const char* s);" << endl;
    string str3 = "abcdef";//这是一个C风格的字符串
    cout << str3 << endl;
    const char* sstr3 = "__________";
    str3.insert(str3.size(), sstr3);
    cout << str3 << endl << endl;;

    cout << "4.string& insert (size_t pos, const char* s, size_t n);" << endl;
    string str4("abcdef");
    cout << str4 << endl;
    const char* sstr4 = "bbbbbxxxxxxxxxxxxxx";//只想截取长度为前5个字符的字符
    str4.insert(1, sstr4, 5);
    cout << str4 << endl << endl;;

    cout << "5.string& insert (size_t pos, size_t n, char c);" << endl;
    string str5("ABCDEFG");
    cout << str5 << endl;
    str5.insert(1, 20, '+');
    cout << str5 << endl << endl;

    cout << "6.iterator insert(const_iterator p, size_t n, char c);" << endl;
    string str6  = "QWERT";
    str6.insert(str6.begin() + 3, 10, 'c');
    cout << str6 << endl << endl;

    cout << "7.iterator insert (const_iterator p, char c);" << endl;
    string str7 = "AAAAAAAAA";
    str7.insert(str7.end(), 'c');
    cout << str7 << endl << endl;

    cout << "8.template <class InputIterator> " << endl;
    cout << "  iterator insert(iterator p, InputIterator first, InputIterator last);" << endl;
    string str8 = "XXXX";
    string sstr8 = "|bcde|";
    str8.insert(str8.begin(), sstr8.begin() + 1, sstr8.end() - 1);
    cout << str8 << endl << endl;

    cout << "9. string& insert (const_iterator p, initializer_list<char> il);" << endl;
    string str9 = "XXXX";
    str9.insert(str9.begin() + 2, { 'a', 'b', 'c' });
    cout << str9 << endl << endl;

    return 0;
}
```

## 8.5.erase()

```c++
string& erase (size_t pos = 0, size_t len = npos);//全部删除（默认）

iterator erase (const_iterator p);//使用迭代器删除指定位置

iterator erase (const_iterator first, const_iterator last);//
```

这个函数主要是用来擦除字符串的一部分，并减小`size()`的值，直接使用则默认为全部删除，这个函数能不用就最好别用，也是效率不太高。

```c++
string str = "abcdef";
cout << str << endl;

str.erase(str.begin() + 3);
cout << str << endl;

str.erase(str.begin(), str.end() - 2);
cout << str << endl;

str.erase();
cout << str << endl;
```

## 8.6.replace()

```c++
//string (1)    
string& replace (size_t pos,        size_t len,        const string& str);
string& replace (const_iterator i1, const_iterator i2, const string& str);

//substring (2)    
string& replace (size_t pos, size_t len,        const string& str, size_t subpos, size_t sublen);

//c-string (3)    
string& replace (size_t pos, size_t len, const char* s);
string& replace (const_iterator i1, const_iterator i2, const char* s);

//buffer (4)    
string& replace (size_t pos, size_t len,        const char* s, size_t n);
string& replace (const_iterator i1, const_iterator i2, const char* s, size_t n);

//fill (5)    
string& replace (size_t pos, size_t len,        size_t n, char c);
string& replace (const_iterator i1, const_iterator i2, size_t n, char c);

//range (6)    
template <class InputIterator>
  string& replace (const_iterator i1, const_iterator i2, InputIterator first, InputIterator last);

//initializer list (7)    
string& replace (const_iterator i1, const_iterator i2, initializer_list<char> il);
```

该函数主要用于替换，参数也很多，您可以稍微玩一下：

```c++
string str = "abc def";
cout << str << endl;

str.replace(3, 2, "&%#");//将str索引为3起始的长度为2的子串替换成"&%#"
cout << str << endl;
```

也不太建议使用，效率比较低下，需要的时候可以使用一下。当然我们也可以自己设计一个算法解决：

```c++
int main()
{
    //设计一个算法让下面string对象的空格改为20%
    string str = "abc def";
    string strx;
    cout << str << endl;
    //空间换时间
    for (auto ch : str)
    {
        if (ch != ' ')
        {
            strx += ch;
        }
        else
        {
            strx += "20%";
        }
    }
    //str = strx;
    //或者使用全局的swap()，但是使用这个会效率很低，内部有一次拷贝构造两次赋值，很糟糕
    str.swap(strx);//string的swap()效率就会高很多，这个成员函数只是交换了指针指向，效率很高
    cout << str << endl;
    //这样的效率都会比较高

    //不过需要注意的是，就算我们直接使用全局的swap()也不会真的调用到最麻烦的哪一个，因为库已经针对全局的swap()进行了重载，内部调用的也是成员的swap()
    return 0;
}
```

## 8.7.swap()

```c++
void swap (string& str);
```

成员交换函数，效率比全局的要高，就是前面代码使用的那一个函数。后面介绍的全局`swap()`调用的就是这个成员函数。

# 9.字符串运算（String operations）

## 9.1.c_str()和data()

```c++
//函数声明
const char* c_str() const noexcept;
const char* data() const noexcept;
```

有时候为了和`C`兼容，需要使用`C`风格的字符串（以`\0`）。而`c_str()`可以使得`string`对象返回底层的`C`风格的字符串成员变量，从这里我们也可以看到，虽然`string`不以`\0`作为结尾标志，但是依旧会在构造`string`对象的时候存储`\0`，以保证能与`C`兼容。

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    string str("abcdef");
    cout << "str" << " = " << str << " 类型：" << typeid(str).name() << endl << endl;
    char* ch;
    cout << typeid(ch).name() << endl;
    cout << "str.c_str()" << " = " << str.c_str() << " 类型：" << typeid(str.c_str()).name() << endl;
    cout << "str.data()" << " = " << str.data() << " 类型：" << typeid(str.data()).name() << endl;
    return 0;
}
```

而`data()`和`s_str()`的使用是等价的，没有太大区别。

```c++
#include <iostream>
#include <string>
#include <cstring>
using namespace std;
int main()
{
    int length;
    string str = "Test string";
    const char* cstr = "Test string";

    if (str.length() == strlen(cstr))
    {
        cout << "1" << endl;

        if (memcmp(cstr, str.data(), str.length()) == 0)
        {
            cout << "1" << endl;
        }
    }
    return 0;
}
```

## 9.2.cpoy()

```c++
//函数声明
size_t copy (char* s, size_t len, size_t pos = 0) const;
```

将`string`对象当前值，从`pos`位置开始的长度为`len`子串拷贝到`s`指向的数组中。

```c++
#include <iostream>
#include <string>
int main()
{
    std::string str("abcd");
    char s[3] = { 0 };//如果这里没有加入初始化{ 0 }，那么后续打印s数组就会出现乱码，原因就是找不到空字符
    str.copy(s, 2);
    std::cout << s << std::endl;
    return 0;
}
```

## 9.3.find()和rfind()

```c++
size_t find (const string& str, size_t pos = 0) const noexcept;

size_t find (const char* s, size_t pos = 0) const;

size_t find (const char* s, size_t pos, size_type n) const;

size_t find (char c, size_t pos = 0) const noexcept;
```

该函数可以寻找字符串或者`string`对象中的内容，。

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    //1.寻找string子串
    string str1("abcd-abcd-abcd");
    string str2("abcd");
    cout << str1.find(str2, 0) << endl;
    cout << str1.find(str2, 1) << endl;
    cout << str1.find(str2, 6) << endl << endl;
    //2.寻找C风格子串
    string str3("ABCD-ABCD-ABCD");
    const char* str4 = "ABCD";
    cout << str3.find(str4, 0) << endl;
    cout << str3.find(str4, 1) << endl;
    cout << str3.find(str4, 6) << endl << endl;

    //3.在范围内寻找C风格子串
    string str5("ABCD-AB-ABCD-AB-ABCD");
    const char* str6 = "ABCD";
    cout << str5.find(str6, 0, 2) << endl;
    cout << str5.find(str6, 1, 2) << endl;
    cout << str5.find(str6, 6, 2) << endl;
    cout << str5.find(str6, 9, 2) << endl;
    cout << str5.find(str6, 14, 2) << endl << endl;

    //4.寻找字符
    string str7("xxxxxxx_xxxx_xxxxxxxxxx_");
    char ch = '_';
    int j = -1;
    for (int i = 0; i < str7.size(); i++)
    {
        int z = str7.find(ch, i);
        if (j != z)
        {
            cout << z << " ";
            j = z;
        }
    }
    return 0;
}
```

而`rfind()`就是逆向查找，具体定义看下文档就行，使用和`find()`类似。

```c++
//重载声明
size_t rfind (const string& str, size_t pos = npos) const noexcept; 

size_t rfind (const char* s, size_t pos = npos) const; 

size_t rfind (const char* s, size_t pos, size_t n) const;

size_t rfind (char c, size_t pos = npos) const noexcept;
```

## 9.4.substr()

```c++
//函数声明
string substr (size_t pos = 0, size_t len = npos) const;
```

这个函数可以用来生成一个新的子串对象（从源string对象的pos开始的长度为len的子字符串）。

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    string str("awwwwf");
    cout << str.substr(1, 4) << endl;
    return 0;
}
```

## 9.5.compare()

```c++
//重载函数
int compare (const string& str) const noexcept;//比较两个string对象

int compare (size_t pos, size_t len, const string& str) const;//在源string对象的子串和str对象比较

int compare (size_t pos, size_t len, const string& str, size_t subpos, size_t sublen) const;//在源string对象的子串内和str对象的子串比较

int compare (const char* s) const;//和C风格的字符串比较

int compare (size_t pos, size_t len, const char* s) const;//string对象的子串和C风格的字符串比较

int compare (size_t pos, size_t len, const char* s, size_t n) const;//string对象的子串和C风格的字符串的子串比较
```

该函数主要是比较字符大小，和`C`语言的`strcmp()`类似返回指定的字符序列。

```c++
#include <iostream>
#include <string>
using namespace std;
// comparing apples with apples
#include <iostream>
#include <string>
int main()
{
    string str1("green apple");
    string str2("red apple");
    //g < r <=> g - r < 0
    //"green apple" < "red apple"
    if (str1.compare(str2) < 0)
        cout << str1 << " is not " << str2 << endl;

    //"apple" == "apple"
    if (str1.compare(6, 5, "apple") == 0)
        cout << "still, " << str1 << " is an apple" << endl;

    //"apple" == "apple"
    if (str2.compare(str2.size() - 5, 5, "apple") == 0)
        cout << "and " << str2 << " is also an apple" << endl;

    //"apple" == "apple" 
    if (str1.compare(6, 5, str2, 4, 5) == 0)
        cout << "therefore, both are apples" << endl;

    const char* str3 = "green apple";
    if (str1.compare(str3) == 0)
        cout << "one and the same" << endl;

    return 0;
}
```

不过这个函数不怎么使用，原因是后面有很多的判断符号重载，这些重载更加方便。

## 9.6.find_first_of()

```c++
//重载函数
size_t find_first_of (const string& str, size_t pos = 0) const noexcept;

size_t find_first_of (const char* s, size_t pos = 0) const;

size_t find_first_of (const char* s, size_t pos, size_t n) const;

size_t find_first_of (char c, size_t pos = 0) const noexcept;
```

在字符串中查找第一次出现指定字符集合中任何一个字符的位置。

```c++
int main()
{
    string str1 = "xxx_big_apple_xxx";
    string str2 = "be";
    cout << str1.find_first_of(str2, 0) << endl;//输出4
    cout << str1.find_first_of(str2, 5) << endl;//输出12
    return 0;
}
```

> 吐槽：这个函数的名字真不好理解……

## 9.7.find_last_of()

```c++
//重载函数
size_t find_last_of (const string& str, size_t pos = npos) const noexcept;

size_t find_last_of (const char* s, size_t pos = npos) const;

size_t find_last_of (const char* s, size_t pos, size_t n) const;

size_t find_last_of (char c, size_t pos = npos) const noexcept;
```

在字符串中查找最后一次出现指定字符集合中任何一个字符的位置。

```c++
int main()
{
    string str1 = "xxx_big_apple_xxx";
    string str2 = "be";
    cout << str1.find_last_of(str2, str1.size() - 1) << endl;//输出12
    cout << str1.find_last_of(str2, 11) << endl;//输出4
    return 0;
}
```

## 9.8.find_first_not_of()

```c++
//重载函数
size_t find_first_not_of (const string& str, size_t pos = 0) const noexcept;

size_t find_first_not_of (const char* s, size_t pos = 0) const;

size_t find_first_not_of (const char* s, size_t pos, size_t n) const;

size_t find_first_not_of (char c, size_t pos = 0) const noexcept;
```

这个函数可以查找不是指定字符集合中的任何一个字符的位置。

下面是一个例子（制作屏蔽词语）：

```c++
int main()
{
    string str("saldgcaslucdgleakjslaejcsfkajsasducgiasfg");
    size_t index = str.find_first_not_of("abcdef", 0);
    cout << str << endl;
    while (index != string::npos)
    {
        str[index] = '*';
        index = str.find_first_not_of("abcdef", index + 1);
    }
    cout << str << endl;
    return 0;
}
```

## 9.9.find_last_not_of()

```c++
//重载函数
size_t find_last_not_of (const string& str, size_t pos = npos) const noexcept;

size_t find_last_not_of (const char* s, size_t pos = npos) const;

size_t find_last_not_of (const char* s, size_t pos, size_t n) const;

size_t find_last_not_of (char c, size_t pos = npos) const noexcept;
```

上面这个函数就是从后面开始查找，这里就不再举例了……

## 9.10.get_allocator()

```c++
//函数声明
allocator_type get_allocator() const noexcept;
```

这个函数就比较复杂了，您可以先去查看一下文档，或者以后我们有机会再来详谈……

## 9.11.assign()

```c++
//重载函数
string& assign (const string& str);

string& assign (const string& str, size_t subpos, size_t sublen);

string& assign (const char* s);

string& assign (const char* s, size_t n);

string& assign (size_t n, char c);

template <class InputIterator>
   string& assign (InputIterator first, InputIterator last);

string& assign (initializer_list<char> il);

string& assign (string&& str) noexcept;
```

这个函数也不经常使用，实际在一种赋值的工作：

```c++
int main()
{
    string str1 = "abcdef";
    string str2 = "xxxxxx";
    str1.assign(str2);
    cout << str1 << endl;
    cout << str2 << endl;
    return 0;
}
```

# 10.常量成员（Member constants）

string类的常量成员，也就是前面介绍的`npos`，之前已经简单提到过，这里就放一下`npos`的定义使得本文章足够完整：

```c++
//常量定义
static const size_t npos = -1;
```

# 11.非成员函数重载（Non-member function overloads）

## 11.1.operator+()

```c++
string operator+ (const string& lhs, const string& rhs);

string operator+ (const string& lhs, const char* rhs);
string operator+ (const char* lhs, const string& rhs);

string operator+ (const string& lhs, char          rhs);
string operator+ (char lhs, const string& rhs);
```

`+`的代价要比`+=`大，因此非必要不要轻易使用`+`，这里就不演示`+`的使用了，您玩一下就行。

## 11.2.relational operators

一些比较运算符重载：

```
//(1)
bool operator== (const string& lhs, const string& rhs);
bool operator== (const char*   lhs, const string& rhs);
bool operator== (const string& lhs, const char*   rhs);

//(2)    
bool operator!= (const string& lhs, const string& rhs);
bool operator!= (const char*   lhs, const string& rhs);
bool operator!= (const string& lhs, const char*   rhs);

//(3)    
bool operator<  (const string& lhs, const string& rhs);
bool operator<  (const char*   lhs, const string& rhs);
bool operator<  (const string& lhs, const char*   rhs);

//(4)    
bool operator<= (const string& lhs, const string& rhs);
bool operator<= (const char*   lhs, const string& rhs);
bool operator<= (const string& lhs, const char*   rhs);

//(5)    
bool operator>  (const string& lhs, const string& rhs);
bool operator>  (const char*   lhs, const string& rhs);
bool operator>  (const string& lhs, const char*   rhs);

//(6)    
bool operator>= (const string& lhs, const string& rhs);
bool operator>= (const char*   lhs, const string& rhs);
bool operator>= (const string& lhs, const char*   rhs);
```

内部使用`string::compare()`函数来进行比较。

## 11.3.swap()

```c++
//重载声明
void swap (string& str);
```

全局交换函数，内部调用了成员函数`swap()`，这个重载是为了防止调用到低效的全局交换函数。

## 11.4.operator>>()和operator<<()

```c++
istream& operator>> (istream& is, string& str);
ostream& operator<< (ostream& os, const string& str);
```

这个就是辅助`string`进行流插入和流输出的重载，也是看下就行，`IO`类的函数我们以后还会深入讲解。

## 11.5.getline()

```c++
istream& getline (istream& is, string& str, char delim);
istream& getline (istream& is, string& str);
```

这个函数使用的频率也比较高，主要是从流`is`中获取一行输入`string`对象，直到遇到文件结束符`EOF`分隔符，由于返回的是流`is`的引用，因此可以重复读取。

```c++
//方式1
string str1;
cin >> str1;
cout << "str1:" << str1 << endl;

//方式2
string str2;
getline(cin, str2);
cout << "str2:" << str2 << endl;

//方式3
string str3;
getline(cin, str3, '$');//遇到$就停止读取，抛弃掉$，不再进入后续的输入
cout << "str3:" << str3 << endl;
```

方式`1`不可以输入带有空格的 ，方式`2`可以，方式`3`可以指定结束符号。

# 12.string相关题目练习

## 12.1.[字符串中的第一个唯一字符](https://leetcode.cn/problems/first-unique-character-in-a-string/)

```c++
class Solution
{
public:
    int firstUniqChar(string s)
    {
        //1.开始计数，生成计数表
        int arr[26] = { 0 };//先定义一个数组存储每个字符的出现个数
        for (auto ch : s)
        {
            arr[ch - 'a']++;//开始计数
        }

        //2.根据string对象和计数表寻找索引
        int i = 0;
        for (i = 0; i < s.size(); i++)
        {
            if (arr[s[i] - 'a'] == 1)
            {
                return i;
            }
        }
        return -1;
    }
};
```

## 12.2.将网址分割为：协议、域名、资源名

```c++
int mian()
{
    //1.输入网址url
    string url;//https->://baidu.com->/limou/file/text
    cin >> url;
    //2.寻找并且拆解url
    string protocol;//协议
    string domain;//域名
    string resource;//资源
    //2.1.寻找协议
    int index1 = url.find(":");//5
    if (index1 == string::npos)
    {
        cout << "没有协议，退出程序" << endl;
        exit(-1);
    }
    //2.2.寻找域名
    int index2 = url.find("/", index1 + 3);//17
    if (index2 == string::npos)
    {
        index2 = url.size();
    }
    //2.3.赋值三个对象
    protocol = url.substr(0, index1);
    domain = url.substr(index1 + 3, index2 - (index1 + 3));//17-5-3 = 12-3 = 9
    if (index2 != url.size())
    {
        resource = url.substr(index2 + 1);
    }

    //3.输出结果
    cout << "protocol = " << protocol << endl;
    if (domain.size() != 0)
    {
        cout << "domain = " << domain << endl;
    }
    else
    {
        cout << "没有域名" << endl;
    }
    if(resource.size() != 0)
    { 
        cout << "resource = " << resource << endl;
    }
    else
    {
        cout << "没有资源" << endl;
    }
    return 0;
}
```

## 12.3.[仅仅反转字母](https://leetcode.cn/problems/reverse-only-letters/)

```c++
class Solution 
{
public:
    bool IsLetter(const char& ch)
    {
        if ((ch >= 'a' && ch <= 'z')
            || (ch >= 'A' && ch <= 'Z'))
        {
            return true;
        }
        return false;
    }
    string reverseOnlyLetters(string str)
    {
        auto begin = 0;
        auto end = str.size() - 1;
        while (begin < end)
        {
            while (!IsLetter(str[begin]) && begin < end)
            {
                begin++;
            }
            while (!IsLetter(str[end]) && begin < end)
            {
                end--;
            }
            swap(str[begin], str[end]);
            begin++;
            end--;
        }
        return str;
    }
};
```

## 12.4.[字符串最后一个单词的长度](https://www.nowcoder.com/practice/8c949ea5f36f422594b306a2300315da?tpId=37&tqId=21224&ru=/exam/oj)

```c++
#include <iostream>
using namespace std;
int main()
{
    //1.输入字符句子
    string str;
    getline(cin, str);//hello nowcode
    //2.逆向寻找
    size_t index = str.rfind(" ");//得到5
    cout << str.size() - index - 1 << endl;//13-5 = 8  
}
```

但是这么做有些不严谨，再严谨点就是下面代码：

```c++
#include <iostream>
using namespace std;

int main()
{
    //1.输入字符句子
    string str;
    getline(cin, str);//hello nowcode
    //2.逆向寻找
    size_t index = str.rfind(" ");//得到5
    if (index != string::npos)
    {
        cout << str.size() - index - 1 << endl;//13-5 = 8   
    }
    else
    {
        cout << str.size();
    }
}
```

## 12.5.[验证回文串](https://leetcode.cn/problems/valid-palindrome/description/)

```c++
class Solution
{
public:
    bool IsLetterAndNumber(const char& ch)//判断是否为字符或者数字
    {
        return((ch >= 'a' && ch <= 'z')
            || (ch >= 'A' && ch <= 'Z')
            || (ch >= '0' && ch <= '9'));
    }
    bool IsOk(char x, char y)//判断是否构成回文的特征
    {
        if (x == y)
        {
            return true;
        }
        if ((x >= 'a' && x <= 'z') && (y >= 'A' && y <= 'Z'))
        {
            if (x - 'a' + 'A' == y)
            {
                return true;
            }
        }
        else if ((y >= 'a' && x <= 'z') && (x >= 'A' && x <= 'Z'))
        {
            if (y - 'a' + 'A' == x)
            {
                return true;
            }
        }
        return false;
    }
    bool isPalindrome(string str)
    {
        //1.取得首尾索引
        //str = "A man, a plan, a canal: Panama"
        int begin = 0;//s[begin] = 'A' 
        int end = str.size() - 1;//str[end] = 'a'
        //2.循环判断回文
        while (begin < end)
        {
            while (begin < end && !IsLetterAndNumber(str[begin]))
            {
                begin++;
            }
            while (begin < end && !IsLetterAndNumber(str[end]))
            {
                end--;
            }
            if (!IsOk(str[begin], str[end]))//如果判断出是相同的就返回true
            {
                return false;
            }
            begin++;
            end--;
        }
        return true;
    }
};
```





```c++
#pragma once
#define _CRT_SECURE_NO_WARNINGS 1
#include <iostream>
#include <string>
#include <cassert>
namespace limou//标准命名空间
{
    class string
    {
    public:
        typedef char* iterator;//迭代器的类型，迭代器实际上是重命名来的
        typedef const char* const_iterator;//常量迭代器
    public:
        //1.成员函数
        //1.1.构造函数
        string(const char* s = "")//缺省参数传递空字符
        {
            std::cout << "string(const char* s = "")" << std::endl;
            _size = strlen(s);//str的长度，不计入'\0'
            _capacity = 15;//str的默认容量，不计入'\0'
            _s = new char[_capacity + 1];//默认申请16个空间其中一个存储'\0'
            while (_size > _capacity)//如果容量不够了，就会进行扩容
            {
                delete[] _s;
                _s = new char[_capacity * 2];
                _capacity *= 2;
            }
            strcpy(_s, s);
        }
        //1.2.析构函数
        ~string()
        {
            std::cout << "~string()" << std::endl;
            delete[] _s;
        }
        //1.3.拷贝构造+赋值重载函数
        string(const string& str)
            : _s(nullptr)//这里的初始化列表就可以避免后续交换的时候，析构cache出现奔溃的现象（delete了随机值）
            , _size(0)
            , _capacity(0)
        {
            //1.传统写法
            //std::cout << "string(const string& str)" << std::endl;
            //_size = str._size;
            //_capacity = str._capacity;
            //_s = new char[_capacity + 1];
            //strcpy(_s, str._s);
            //2.现代写法
            std::cout << "string(const string& str)" << std::endl;
            string cache(str._s);//构造一个
            std::swap(_s, cache._s);
            std::swap(_size, cache._size);
            std::swap(_capacity, cache._capacity);
        }
        //string& operator=(const string& str)
        //{
        //    //1.传统写法
        //    //if (this != &str)
        //    //{
        //    //    std::cout << "string operator=(const string& str)" << std::endl;
        //    //    char* cache = new char[str._capacity + 1];
        //    //    strcpy(cache, str._s);
        //    //    delete[] _s;
        //    //    _s = cache;
        //    //    _size = str._size;
        //    //    _capacity = str._capacity;
        //    //}
        //    //return *this;
        //    //2.现代写法（实际上和传统写法的效率差别不大）
        //    //string str1("abcd");
        //    //string str2;
        //    //str2 = str1;
        //    std::cout << "string operator=(const string& str)" << std::endl;
        //    if (this != &str)
        //    {
        //        string cache(str);//这里是调用了拷贝构造
        //        std::swap(_s, cache._s);
        //        std::swap(_size, cache._size);
        //        std::swap(_capacity, cache._capacity);
        //    }
        //    return *this;
        //}
        //如果写得再现代一点，还能吧拷贝的步骤省略
        string& operator=(string cache)//这里是调用了拷贝构造
        {
            std::cout << "string operator=(const string& str)" << std::endl;
            std::swap(_s, cache._s);
            std::swap(_size, cache._size);
            std::swap(_capacity, cache._capacity);
            return *this;
        }
        //2.迭代器
        iterator begin()
        {
            return _s;
        }
        iterator end()
        {
            return _s + _size;//若"abcd",size=4,_s->a,_s+4->'\0'
        }
        const_iterator begin() const
        {
            return _s;
        }
        const_iterator end() const
        {
            return _s + _size;
        }
        //3.容积
        const char* c_str() const
        {
            return _s;
        }
        const size_t size() const
        {
            return _size;
        }
        const size_t capacity() const
        {
            return _capacity;
        }
        void reserve(size_t n)//改变容量
        {
            if (n > _capacity)
            {
                _capacity = n;//改变容量
                char* cache = new char[n + 1];//创建对应容量的空间
                strcpy(cache, _s);//拷贝源字符串数据，会拷贝'\0'
                delete[] _s;//销毁源字符串
                _s = cache;//
            }
            else
            {
                std::cout << "reserve操作只能扩容" << std::endl;
            }
        }
        void resize(size_t n, char ch = '\0')
        {
            if (n <= _size)
            {
                _s[n] = '\0';
                _size = n;
            }
            else
            {
                reserve(n);
                while (_size < n)
                {
                    _s[_size] = ch;
                    _size++;
                }
                _s[_size] = '\0';
            }
        }
        void clear()
        {
            erase(0);
        }
        //3.访问
        char& operator[](size_t pos)
        {
            assert(pos <= _size);//这里设计的和库是一样的，可以访问到'\0'
            return _s[pos];
        }
        //4.修改器
        void push_back(char ch)//push_back单字符
        {
            //若"abcd",_size=4,_capacity=15,空间足够,无需扩容
            //若"aaaaabbbbbccccc",_size=15,_capacity=15,空间不够,需要扩容
            if (_size == _capacity)
            {
                reserve(_capacity * 2);//改变容量为原来的2倍
            }
            _s[_size++] = ch;
            _s[_size] = '\0';
        }
        void append(const char* s)//append字符串
        {
            size_t len = _size + strlen(s);
            if (len > _capacity)
            {
                reserve(len);//改变容量
            }
            strcpy(_s + _size, s);
            _size += strlen(s);
        }
        string& operator+=(char ch)
        {
            push_back(ch);
            return *this;
        }
        string& operator+=(const char* str)
        {
            append(str);
            return *this;
        }
        void inster(size_t pos, char ch)
        {
            //个人看法：顺序表挪动数据从越界位置的索引开始比较好（也就是下面end=_size + 1,从越界的end开始）
            //"abcdef\0",pos=2,pos->c,s[_size]='\0',"ab cdef"
            //"\0",pos=0,pos->'\0',s[_size]='\0'," \0"
            assert(pos <= _size);
            if (_size == _capacity)
            {
                reserve(_capacity * 2);
            }
            size_t end = _size + 1;
            while (end > pos)
            {
                _s[end] = _s[end - 1];
                end--;
            }
            _s[pos] = ch;
            _size++;
        }
        void inster(size_t pos, const char* s)
        {
            //"abcde\0",pos=1,pos->b,s="xxx","abcde\0"->"a   bcde\0"
            assert(pos <= _size);
            size_t len = strlen(s);
            if (len + _size > _capacity)//容量不够
            {
                reserve(len + _size);//开辟好足够的空间
            }
            size_t end = _size + len;//end=5+3=8
            while (end > pos)//8>1
            {
                _s[end] = _s[end - len];//_s[8] = _s[5]
                end--;
            }
            strncpy(_s + pos, s, len);
            _size += len;
        }
        void erase(size_t pos, size_t len = npos)//len没有指定就会全部删除
        {
            //擦除字符串的一部分，并且减少_size
            //"bacdef\0",pos=1,len=3,"b   ef\0"->"bef\0"
            assert(pos <= _size);
            if (_size == 0)//如果字符串大小为0就直接返回，没必要清理了
                return;
            if (len == npos || pos + len >= _size)//如果没有指定len就直接删除pos之后的所有字符串（或者超出范围）
            {
                _s[pos] = '\0';
                _size = pos;
                return;
            }

            //对于指定pos就如下面处理
            size_t begin = pos + len;//begin=1+3=4,_s[4]='e'
            while (begin <= _size)//begin<6
            {
                _s[begin - len] = _s[begin];//_s[1]=_s[4],'a'->'e'
                begin++;
            }
            _size -= len;
        }
        //5.比较（库里的写成全局是因为支持其他的类实例化）
        bool operator<(const string& str)
        {
            return (strcmp(_s, str._s) < 0);
        }
        bool operator==(const string& str)
        {
            return (strcmp(_s, str._s) == 0);
        }
        bool operator<=(const string& str)
        {
            return *this < str || *this == str;
        }
        bool operator>(const string& str)
        {
            return !(*this <= str);
        }
        bool operator!=(const string& str)
        {
            return !(*this == str);
        }
        bool operator>=(const string& str)
        {
            return !(*this < str);
        }
        //6.字符串运算
        size_t find(char ch, size_t pos = 0)
        {
            //"abcd\0",pos=1,pos->b,ch=d
            for (size_t i = pos; i < _size; i++)
            {
                if (_s[i] == ch)
                {
                    return i;
                }
            }
            return npos;
        }
        size_t find(const char* sub, size_t pos = 0)
        {
            //这里也可以使用kmp算法，但是对于新手来说比较复杂，并且在实践中的效率一般（哪怕STL中也没有使用这个算法）字符串匹配更好的方法是BM算法
            //这里我们就直接使用C语言提供的接口strstr()
            const char* p = strstr(_s + pos, sub);
            if (p != NULL)
            {
                return p - _s;
            }
            return npos;
        }
        string substr(size_t pos, size_t len = npos)
        {
            //"abcdef\0",pos=1,pos->b,len=4,substr="bcde"
            //1.写法1（冗余一些）
            //string str;
            //if (len == npos || pos + len >= _size)
            //{
            //    len = _size - pos;//重新计算长度
            //    str.reserve(len);//提前开好空间
            //    for (size_t i = pos; i < _size; i++)
            //    {
            //        str += _s[i];
            //    }
            //}
            //else
            //{
            //    str.reserve(len);//提前开好空间
            //    for (size_t i = pos; i < pos + len; i++)
            //    {
            //        str += _s[i];
            //    }
            //}
            //return str;
            //2.写法2（精简一些）
            string str;
            size_t end = pos + len;
            if (len == npos || pos + len >= _size)
            {
                len = _size - pos;//重新计算长度
                end = _size;
            }
            str.reserve(len);//提前开好空间
            for (size_t i = pos; i < end; i++)
            {
                str += _s[i];
            }
            return str;
        }
    private:
        char* _s;
        size_t _size;
        size_t _capacity;
    public:
        static const size_t npos = -1;
        /*
        吐槽：单纯的静态成员不可以在这里定义（也没有办法传给初始化列表），
        但是C++又搞了一个特例，const static的整形变量可以直接在这里定义。
        这是一个比较离谱的设计，记着就行，一般也不会这么写，容易坑人……因为这个特例会破坏语法规则
        */
    };
    //const size_t string::npos = -1;
    //7.流重载（并不一定要写成友元）
    std::ostream& operator<<(std::ostream& out, const string& str)
    {
        for (auto ch : str)
            out << ch;
        return out;
    }
    std::istream& operator>>(std::istream& in, string& str)
    {
        //1.这里注意下面这样的代码是解决不了问题的，因为cin遇到空格就会自动跳过
        //char ch;
        //in >> ch;
        //while (ch != ' ' && ch != '\n')
        //{
        //    str += ch;
        //    in >> ch;
        //}
        //return in;
        //2.更正写法如下：
        //str.clear();//为了避免变成尾插，还需要清楚原有的数据
        //char ch;
        //ch = in.get();//这是istream类提供的一个函数
        //while (ch != ' ' && ch != '\n')
        //{
        //    str += ch;
        //    ch = in.get();
        //}
        //return in;
        //3.不过上述扩容有些频繁，还可以在进行改进
        str.clear();//为了避免变成尾插，还需要清楚原有的数据

        char buff[128 + 1];//缓存区
        size_t i = 0;

        char ch;
        ch = in.get();//这是istream类提供的一个函数
        while (ch != ' ' && ch != '\n')
        {
            buff[i++] = ch;
            if (i == 128)
            {
                buff[i] = '\0';
                str += buff;//加载数据
                i = 0;//置空缓存区
            }
            ch = in.get();
        }
        if (i != 0)
        {
            buff[i] = '\0';
            str += buff;//加载数据
        }
        return in;
    }

    //8.测试
    //构造测试
    void test_1()
    {
        //1.库中的默认构造实现（测试）
        std::string str;
        std::cout << str.c_str() << std::endl;
        std::cout << "str.size = " << str.size() << std::endl;
        std::cout << "str.capacity = " << str.capacity() << std::endl;
        std::cout << "----" << std::endl << std::endl;

        //2.我们自己的构造实现（测试）
        //string()
        string str0;
        const char* s0 = str0.c_str();
        std::cout << s0 << std::endl;
        std::cout << "str0.size = " << str0.size() << std::endl;
        std::cout << "str0.capacity = " << str0.capacity() << std::endl;
        //string(const char* s)
        string str1("Hello, My name is Limou3434.");
        const char* s1 = str1.c_str();
        std::cout << s1 << std::endl;
        //string(const string& str)
        string str2(str1);
        const char* s2 = str2.c_str();
        std::cout << s2 << std::endl;
        //string& operator=(const string& str)
        string str3("aaaa");
        string str4("bbbb");
        string str5;
        str5 = (str4 = str3);
        const char* s3 = str3.c_str();
        const char* s4 = str4.c_str();
        const char* s5 = str5.c_str();
        std::cout << s3 << " | " << s4 << " | " << s5 << std::endl;
    }
    //迭代器测试
    void test_2()
    {
        //1.使用[]遍历访问
        string str1("Hello");
        for (int i = 0; str1[i]; i++)
        {
            std::cout << str1[i]++ << " ";
        }
        std::cout << std::endl;
        for (int i = 0; i < str1.size(); i++)
        {
            std::cout << str1[i] << " ";
        }
        std::cout << std::endl;

        //2.使用迭代器遍历
        string str2("Iamlimou");
        limou::string::iterator it = str2.begin();
        while(it < str2.end())
        {
            std::cout << (*it)++ << " ";
            it++;
        }
        std::cout << std::endl;
        it = str2.begin();
        while(it < str2.end())
        {
            std::cout << *it << " ";
            it++;
        }
        std::cout << std::endl;

        //3.使用范围for，本质为迭代器（测试）
        string str3;
        str3 = "WordWordWord";
        for (auto& ch : str3)
        {
            std::cout << ch++ << " ";
        }
        std::cout << std::endl;
        for (auto& ch : str3)
        {
            std::cout << ch << " ";
        }
        std::cout << std::endl;
    }
    //修改器测试
    void test_3()
    {
        //1.push_back()
        string str1 = "abcd";
        str1.push_back('e');
        std::cout << str1.c_str() << std::endl;

        for (int i = 0; i < 50; i++)
        {
            str1.push_back('x');
            std::cout << str1.c_str() << std::endl;
        }
        std::cout << std::endl;

        //2.append()
        str1.append("________________________________");
        std::cout << str1.c_str() << std::endl;

        //3.operator+=()
        string str2;
        str2 += '#';
        str2 += '#';
        str2 += '#';
        str2 += '#';
        str2 += '#';
        str2 += '#';
        str2 += '#';
        str2 += '#';
        str2 += '#';
        str2 += '#';
        str2 += '#';
        str2 += '#';
        str2 += '#';
        str2 += '#';
        std::cout << str2.c_str() << std::endl;
        str2 += "######################################################";
        std::cout << str2.c_str() << std::endl;

        //4.inster()
        string str4;
        for (int i = 0; i < 10; i++)
        {
            str4.inster(0, '#');
            std::cout << str4.c_str() << std::endl;
        }
        str4.inster(1, '%');
        std::cout << str4.c_str() << std::endl;
        str4.inster(str4.size(), '%');
        std::cout << str4.c_str() << std::endl;

        string str5;
        str5.inster(0, "xxx");
        std::cout << str5.c_str() << std::endl;
        str5.inster(3, "aaaaaaaaaaaaaaa");
        std::cout << str5.c_str() << std::endl;
        str5.inster(5, "*");
        std::cout << str5.c_str() << std::endl;

        //5.erase()
        string str6("abcdef");
        str6.erase(2, 3);
        std::cout << str6.c_str() << std::endl;
        str6.erase(0);
        std::cout << str6.c_str() << std::endl;

        str6.push_back('c');
        std::cout << str6.c_str() << std::endl;

        string str7("xxcdef");
        str7.erase(2, 100);
        std::cout << str7.c_str() << std::endl;
    }
    //比较测试
    void test_4()
    {
        string str1 = "a";
        string str2 = "a";
        string str3 = "b";
        string str4 = "c";
        if (str1 < str3)
        {
            std::cout << "true" << std::endl;
        }        
        if (str1 == str2)
        {
            std::cout << "true" << std::endl;
        }        
        if (str1 <= str3)
        {
            std::cout << "true" << std::endl;
        }
        if (str1 <= str2)
        {
            std::cout << "true" << std::endl;
        }
        if (str4 > str3)
        {
            std::cout << "true" << std::endl;
        }        
        if (str1 != str3)
        {
            std::cout << "true" << std::endl;
        }        
        if (str4 >= str3)
        {
            std::cout << "true" << std::endl;
        }
        if (str4 >= str4)
        {
            std::cout << "true" << std::endl;
        }
    }
    //流测试
    void test_5()
    {
        string str1("abc");
        string str2("efg");
        std::cin >> str1;
        std::cin >> str2;
        std::cout << str1 << std::endl;
        std::cout << str2 << std::endl;
    }
    //容积测试
    void test_6()
    {
        string str1("abcdefehi");
        str1.resize(3);
        std::cout << str1.c_str() << std::endl;
        str1.resize(10, 'x');
        std::cout << str1.c_str() << std::endl;

        string str2;
        str2.resize(10, 'x');
        std::cout << str2.c_str() << std::endl;
        std::cout << str2.size() << std::endl;
        std::cout << str2.capacity() << std::endl;

        str2.resize(101, '?');
        std::cout << str2.c_str() << std::endl;
        std::cout << str2.size() << std::endl;
        std::cout << str2.capacity() << std::endl;

        str2 += "abcd";
        std::cout << str2.c_str() << std::endl;
        std::cout << str2.size() << std::endl;
        std::cout << str2.capacity() << std::endl;
    }
    //运算测试
    void test_7()
    {
        string str("limou3434");
        std::cout << str.find('i') << std::endl;

        std::cout << str.find("mou") << std::endl;

        string substr = str.substr(1, 3);
        std::cout << substr.c_str() << std::endl;

        substr = str.substr(4);
        std::cout << substr.c_str() << std::endl;
    }
    //sizeof测试
    void test_8()
    {
        std::string str1;
        std::string str2 = "abcdabcdabcdabcdabcdabcd";
        std::cout << sizeof(str1) << std::endl;
        std::cout << sizeof(str2) << std::endl;
        //在库里面，会多出一个_buff[16]
        //如果字符数小于16，就会存储到buff里
        //如果字符数大于16，就会存储到_s指向的空间里
        //_buff[16]为16字节
        //_str为8字节  
        //_size为8字节
        //_capacity为8字节
        //因此体现为40字节大小的空间
        //在VS的调试监视窗口中的原始视图里可以找到buff数组
    }

}
/*
注意我们写的只是string类，而不是basic_string类模板
而不是一个类模板，因此有些地方上面的代码对于其他的实例化可能不够适用
*/

/*
另外这里再提及一个浅拷贝的知识：
深拷贝的代价太大了，那么也没有其他办法解决浅拷贝的问题呢？
    1.首先浅拷贝的主要问题如下：
        1.1.析构了两次
        1.2.一次修改影响多个对象
    2.针对上述问题有一些解决方案：
        2.1.解决问题一：这个时候我们引入一个方法：“引用计数”，
                    “引用计数”可以记录有多少个对象指向同一块空间，
                    析构的时候就减少一次计数，除非减到0才可以释放。
                    （这里只是简单提及，以后使用智能指针的时候还会再提）
        2.2.解决问题二：上述“引用计数”解决了两次析构的问题，
                    但是没有解决一次修改多次改变的问题，
                    因此又引入了“写时拷贝”，只有写的时候才进行拷贝。
                    当“引用计数”不为1时，在写入空间的时候先进行深度拷贝再写入。
                    当“引用计数”为1就直接写入空间进行修改。
                    也就是说“写时拷贝”是一种‘拖延’的方案，最终还是要进行深拷贝，
                    只不过只在写的时候进行深拷贝而已
    3.在拷贝的时候如果使用了上述方案，如果没有进行“对空间写入”，就会提高一些效率
当然上述只是简单提及一下，为以后的知识进行铺垫罢了。

另外补充一下：在有些VS版本下没有采用上述的方案，是直接使用深度拷贝的，这点可以通过下面代码来证明。
#include <iostream>
#include <string>
using namespace std;
int main()
{
    string str1("abcd");
    string str2(str1);
    cout << (void*)str1.c_str() << endl;
    cout << (void*)str2.c_str() << endl;
    str2 += 'e';
    cout << (void*)str1.c_str() << endl;
    cout << (void*)str2.c_str() << endl;
    return 0;

}
而G++下的string类是通过写时拷贝来实现的，string对象总共占去4个字节（32位），
内部只包含一个指针，该指针指向堆空间，内部包含:
    (1)空间总大小
    (2)字符串有效长度
    (3)引用计数
指向堆空间的指针（用来存储字符串）”
*/
```

