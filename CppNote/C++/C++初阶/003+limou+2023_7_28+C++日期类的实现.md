# 1.日期类接口概要（接口）

本次主要是来书写一个`C++`日期类的实现，下面我会给出一个日期类的大概框架，以及具体要实现的接口函数，您可以先试着自己实现一下，然后再来看我的具体代码实现。

```c
#include <iostream>
#include <cassert>
using namespace std;
class Date
{
public:
    //1.全缺省的构造函数（使用了初始化列表）
    Date(int year, int month, int day);
    //2.拷贝构造函数
    //d2(d1)
    Date(const Date& d);
    //3.赋值运算符重载函数
    //d2 = d3 --> d2.operator=(&d2, d3)
    Date& operator=(const Date& d);
    //4.析构函数
    ~Date();
    //5.符号重载函数
    //获取某年某月的天数
    int GetMonthDay(int year, int month);
    //日期+=天数重载
    Date& operator+=(int day);
    //日期+天数运算符重载
    Date operator+(int day);
    //日期-=天数运算符重载
    Date& operator-=(int day);
    //日期-天数运算符重载
    Date operator-(int day);
    //前置++运算符重载
    Date& operator++();
    //后置++运算符重载
    Date operator++(int);
    //前置--运算符重载
    Date& operator--();
    //后置--运算符重载
    Date operator--(int);
    //>运算符重载
    bool operator>(const Date& d);
    //==运算符重载
    bool operator==(const Date& d);
    //!=运算符重载
    bool operator!=(const Date& d);
    //>=运算符重载
    bool operator>=(const Date& d);
    //<运算符重载
    bool operator<(const Date& d);
    //<=运算符重载
    bool operator<=(const Date& d);
    //日期-日期=返回天数运算符重载
    int operator-(const Date& d);
    //6.打印日期类函数
    void Print(void);
private:
    int _year;
    int _month;
    int _day;
};
```

# 2.日期类详细解析（细节）

## 2.1.构造和析构函数

### 2.1.1.构造函数

首先我们需要书写一个构造函数`Date()`，这个构造函数能够在没有指定年月日的情况下，拥有默认/缺省值`1900年1月1日`。

```c++
#include <iostream>
#include <cassert>
using namespace std;
class Date
{
public:
    //1.全缺省的构造函数（使用了初始化列表）
    Date(int year = 1900, int month = 1, int day = 1)
        : _year(year),
        _month(month),
        _day(day)//这里也可以不使用初始化列表，如果您尚未了解过该语法可以注释掉“ _year(year),_month(month),_day(day)”这一部分
    {}
private:
    int _year;
    int _month;
    int _day;
}
```

并且我们需要可以帮助我们检查日期合法性的函数，因此我们书写一个`GetMonthDay()`来获取在不同年份以及不同月份的请款下正确的天数是多少。为什么不直接检查用户给予我们的值是否正确，而是要获取正确的天数呢？因为这个获取正确天数的函数`GetMonthDay()`在后续还有些许应用。

```c++
#include <iostream>
#include <cassert>
using namespace std;
class Date
{
public:
    //获取某年某月的天数
    int GetMonthDay(int year, int month)
    {
        const static int monthDays[13] = { 0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31 };//这个静态的编写是因为GetMonthDay有可能会被频繁调用，那么monthDays数组就会不断的被创建，虽然这点消耗挺小，但是如果加上静态关键字，这个数组就会一直存在，不会被反复创建，节省资源。
        /*
        日志：
        一个年份是闰年的条件如下：
        1.如果该年份能够被4整除，但不能被100整除，则为闰年。例如：2008年、2012年等。
        2.如果该年份能够被400整除，则也是闰年。例如：2000年、2400年等。
        */
        if (month == 2
            && ((year % 4 == 0) && ((year % 100 != 0) || (year % 400 == 0)))
            )
        {
            return 29;
        }
        return monthDays[month];
    }
    //1.全缺省的构造函数（使用了初始化列表）
    Date(int year = 1900, int month = 1, int day = 1)
        : _year(year),
        _month(month),
        _day(day)//这里也可以不使用初始化列表，如果您尚未了解过该语法可以注释掉“ _year(year),_month(month),_day(day)”这一部分
           if ((_month < 1) || (_month > 12) 
                || _day < 1 || (GetMonthDay(_year, _month) < _day))
        {
                  cout << "非法日期：";
                Print();
              cout << endl;
                  exit(-1);
        }
private:
    int _year;
    int _month;
    int _day;
}
```

### 2.1.2.析构函数

关于日期类，由于成员变量里面没有使用堆资源，因此其实不需要我们编写析构函数。但是为了凸显析构的存在，达到训练的目的，可以让析构执行一些打印工作。

> 注意：这里只是演示析构会被编译器自动调用，实际使用析构是不会这么用的）。

```c++
//4.析构函数
~Date()
{
    cout << "析构函数在这个日期类里面没有太大用处:~Data()" << endl;
}
```

实际使用的日期类过程中也可以注释掉这个自定义的析构函数，编译器帮我们生成的析构函数已经够“用”了。

实际上就算是编译器生成的析构函数也没做什么，因为没什么资源（例如：在堆空间上申请的资源）可以析构的。

编译器默认的析构函数不会对内置类型做什么，而日期类的成员变量也没有自定义类型，因此也不会调用自定义类型的析构函数。

## 2.2.日期对象打印函数

我们还需要检验一下我们的构造函数工作得怎么样了，因此需要编写一个打印函数打印日期对象内部的成员变量。

```c++
//6.打印日期类函数void Print(void)
{
    cout << _year << "-" << _month << "-" << _day << endl;
}
```

## 2.3.拷贝构造和赋值运算符重载函数

接下来就要考虑两种情况：拷贝和构造。

1. 拷贝构造主要解决了：
   
   ```c++
   Date d1(2023, 7, 29);
   Date d2(d1);//将d2初始化为d1
   ```

2. 而赋值运算符重载则解决了：
   
   ```c++
   Date d3(2023, 1, 2);
   Date d4(2023, 3, 9);
   d3 = d4;//将对象d4的内容赋值给d3
   ```

### 2.3.1.拷贝构造函数

由于拷贝构造函数不像构造和析构函数，它对内置类型是会自动进行拷贝处理的。

也就是说，我们其实也无需自定义拷贝构造函数，使用默认的即可，但是您也可以试着像下面一样写一下 。

```c++
//2.拷贝构造函数
//d2(d1)
Date(const Date& d)//实际可以不写这个拷贝构造，只用编译器默认生成的就可以了，需要注意这里要使用引用
{
    _year = d._year;
    _month = d._month;
    _day = d._day;
}
```

### 2.3.2.赋值运算符重载

赋值运算符重载也是可以不用书写的，编译器也同样会默认生成一个赋值重载。

这也是因为日期类没有自定义类型的成员，只需要浅拷贝即可，但是您也可以如下面代码写出来：

```c++
//3.赋值运算符重载函数
//d2 = d3 --> d2.operator=(&d2, d3)
void operator=(const Date& d)
{
    _year = d._year;
    _month = d._month;
    _day = d._day;
}
```

注意这里的形式参数`const Date& d`其实也不是一定要使用引用，因为前面已经使用了编译器默认的拷贝构造函数，并不会引发无穷递归的问题。

但是最好加上引用，可以减少一些拷贝的消耗。

而使用`const`可以保护`d`对象在函数内不被更改，并且又能使用引用提高效率。

另外如果您对类的`this`指针还不够熟悉，这里显式的写下编译器处理后的代码：

```c++
//3.赋值运算符重载函数
//d2 = d3 --> d2.operator=(&d2, d3)
void operator=(const Date& d)
{
    this->_year = d._year;
    this->_month = d._month;
    this->_day = d._day;
}
```

另外还有两个点需要注意，上述代码还没有办法实现连续赋值：`d1 = d2 = d3`的情况，因此我们还需要进行修改：

```c++
//3.赋值运算符重载函数
//d2 = d3 --> d2.operator=(&d2, d3)
Date& operator=(const Date& d)
{
    _year = d._year;
    _month = d._month;
    _day = d._day;
    return *this;
}
```

以及如果出现自己赋值给自己的情况，上面的代码还可以再优化一些：

```c++
//3.赋值运算符重载函数
//d2 = d3 --> d2.operator=(&d2, d3)
Date& operator=(const Date& d)
{
    if (this == &d) 
    {    
        return *this;//处理自我赋值的情况
    }
    _year = d._year;
    _month = d._month;
    _day = d._day;
    return *this;
}
```

或者写成下面这样更加简洁：

```c++
//3.赋值运算符重载函数
//d2 = d3 --> d2.operator=(&d2, d3)
Date& operator=(const Date& d)
{
    if (this != &d) 
    {    
        _year = d._year;
        _month = d._month;
        _day = d._day;
    }
    return *this;//也可以处理自我赋值的情况
}
```

最后，一定要注意拷贝构造和赋值重载的区别，前者重点在于构造，后者在于赋值。

## 2.4.算术运算符重载函数

接下来我们来实现一些合理的算术运算符号。

### 2.4.1.+=、+符号

有的时候我们希望一个日期加上一个数字得到一个新的数字。例如：`2023年9月1日 + 1 = 2023年9月2日`。我们可以通过重载`+=`、`+`来实现这一思路。

```c++
//日期+=天数重载
Date& operator+=(int day)
{
    _day += day;//先不管3721，直接加上就行
    int days = GetMonthDay(_year, _month);//获取这个月的天数
    while (_day > days)//如果天数是不正确的就进入循环
    {
        _month++;//天数多了就加月份
        _day -= days;//并且把天数减小一些
        if (_month == 13)//如果月份超过了就加年份
        {
            _year++;
            _month = 1;
        }
        days = GetMonthDay(_year, _month);//重新获取正确的天数
    }
    return *this;
}
//日期+天数运算符重载
Date operator+(int day)
{
    Date cache(*this);//拷贝一份原有的*this
    return cache += day;
}
```

我们可以复用`+=`来实现`+`的功能，实际上反过来也是可以的，例如下面的写法：

```c++
//日期+=天数重载
Date operator+(int day)
{
    Date cache(*this);//复制一份+左边的操作数
    cache._day += day;
    int days = GetMonthDay(cache._year, cache._month);//获取这个月的天数
    while (cache._day > days)//如果天数是不正确的就进入循环
    {
        cache._month++;//天数多了就加月份
        cache._day -= days;//并且把天数减小一些
        if (cache._month == 13)//如果月份超过了就加年份
        {
            (cache._year)++;
            (cache._month) = 1;
        }
        days = GetMonthDay(_year, _month);//重新获取正确的天数
    }
    return cache;
}
Date& operator+=(int day)
{
    *this = *this + day;
    return *this;
}
//日期+天数运算符重载
```

那么哪一种写法好呢？第一种好，为什么呢？

1. 因为第一种的`+`有两次拷贝构造（一次在`cache`、一次在返回值）第二种的`+`也有两次拷贝构造（一次在`cache`，一次在返回值）。

2. 但是第一种的`+=`没有调用拷贝构造，第二种的`+=`调用了两次拷贝和一次赋值（因为`+=`内部调用了重载的`+`就又调用了两次拷贝构造）。

### 2.4.2.-=、-符号

这里我们直接复用`-=`给`-`使用，为什么不反过来复用的理由和上面类似，这里不再赘述。

```c++
//日期-=天数运算符重载
Date& operator-=(int day)
{
    _day -= day;//先不管3721，直接减去就行
    while (_day <= 0)//如果天数是不正确的就进入循环
    {
        _month--;//减去月份
        if (_month == 0)//如果月份为0了就减去年份，并且重置月份
        {
            _year--;
            _month = 12;
        }
        _day += GetMonthDay(_year, _month);//重新获取正确的天数
    }
    return *this;
}
//日期-天数运算符重载
Date operator-(int day)
{
    Date cache(*this);//拷贝一份原有的*this
    return cache -= day;
}
```

### 2.4.3.+=和-=的优化

这里还有一个问题，如果“加上负数”或者“减去负数”怎么办？会产生一些小`bug`，我们可以通过一些调整来优化：

```c++
//日期+=天数重载
Date& operator+=(int day)
{
    if (day < 0)
    {
        return *this -= (-day);
    }
    _day += day;//先不管3721，直接加上就行
    int days = GetMonthDay(_year, _month);//获取这个月的天数
    while (_day > days)//如果天数是不正确的就进入循环
    {
        _month++;//天数多了就加月份
        _day -= days;//并且把天数减小一些
        if (_month == 13)//如果月份超过了就加年份
        {
            _year++;
            _month = 1;
        }
        days = GetMonthDay(_year, _month);//重新获取正确的天数
    }
    return *this;
}
//日期+天数运算符重载
Date operator+(int day)
{
    Date cache(*this);//拷贝一份原有的*this
    return cache += day;
}
//日期-=天数运算符重载
Date& operator-=(int day)
{
    if (day < 0)
    {
        return *this += (-day);
    }
    _day -= day;//先不管3721，直接减去就行
    while (_day <= 0)//如果天数是不正确的就进入循环
    {
        _month--;//减去月份
        if (_month == 0)//如果月份为0了就减去年份，并且重置月份
        {
            _year--;
            _month = 12;
        }
        _day += GetMonthDay(_year, _month);//重新获取正确的天数
    }
    return *this;
}
//日期-天数运算符重载
Date operator-(int day)
{
    Date cache(*this);//拷贝一份原有的*this
    return cache -= day;
}
```

### 2.4.4.++、--的实现

这两个运算符要注意一个问题：“如何区分前置和后者？”`C++`为了解决这个问题专门做了区分，书写“运算符重载”的时候，前置符号正常书写即可，但后置符号需要在重载函数中填入`int`来和前置符号进行区分。

```c++
//前置++运算符重载
Date& operator++()//展开为Date& operator++(const Date* this)
{
    return (*this) += 1;
}
//后置++运算符重载
Date operator++(int)//展开为Date& operator++(const Date* this, int x)，这里的x会自动传值为0或者其他数，只要编译器能够识别即可，反正接受了也没在函数内使用没有影响，只是做一个区分
{
    Date cache(*this);//拷贝一份原有的*this
    *this += 1;
    return cache;
}
//前置--运算符重载
Date& operator--()
{
    return (*this -= 1);
}
//后置--运算符重载
Date operator--(int)
{
    Date cache(*this);//拷贝一份原有的*this
    *this -= 1;
    return cache;
}
```

因此实际上有些语言为了避免这种问题，直接去掉了`++`和`--`运算符。

### 2.4.5.日期-日期的实现

有的时候也需要计算两个日期之间的天数。

如果直接计算日期减去日期会很麻烦，我们可以让小的那个日期递增到大的哪个日期，并且使用一个变量充当计数器来记录天数。

```c++
//日期-日期=返回天数运算符重载
int operator-(const Date& d)
{
    //以下方法一、二是本质是没什么区别的，但是方法一返回的是正数，方法二返回的数带有符号
    //1.方法一
    //Date cache(d);
    //int flag = 0;
    //while ((*this) > cache)
    //{
    //    ++cache;
    //    ++flag;
    //}
    //while ((*this) < cache)
    //{
    //    --cache;
    //    ++flag;
    //}
    //return flag;

    //2.方法二
    Date max = *this;
    Date min = d;
    int flag = 1;
    if (*this < d)
    {
        max = d;
        min = *this;
        flag = -1;
    }
    int n = 0;
    while (min != max)
    {
        min++;
        n++;
    }
    return n * flag;
}
```

## 2.5.逻辑运算符重载函数

接下来我们来实现一些合理的逻辑运算符号。例如：判断两个日期是否相等，哪一个日期是比较大的（`2023年7月1日`比`2022年8月9日`大）。

这里简单看一下即可：

```c++
//>运算符重载
bool operator>(const Date& d)
{
    if (_year > d._year)
    {
        return true;
    }
    else if (_year == d._year)
    {
        if (_month > d._month)
        {
            return true;
        }
        else if (_month == d._month)
        {
            if (_day > d._day)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }
    return false;
}
//==运算符重载
bool operator==(const Date& d)
{
    if (_year == d._year && _month == d._month && _day == d._day)
    {
        return true;
    }
    return false;
}
//!=运算符重载
bool operator != (const Date& d)
{
    if (*this == d)
    {
        return false;
    }
    return true;
}
//>=运算符重载
bool operator >= (const Date& d)
{
    if ((*this) > d || (*this) == d)
        return true;
    return false;
}
//<运算符重载
bool operator < (const Date& d)
{
    if ((*this) >= d)
        return false;
    return true;
}
//<=运算符重载
bool operator <= (const Date& d)
{
    if ((*this) > d)
        return false;
    return true;
}
```

# 3.日期类具体实现（完整）

```c++
#include <iostream>
#include <cassert>
using namespace std;
class Date
{
public:
    //获取某年某月的天数
    int GetMonthDay(int year, int month)
    {
        const static int monthDays[13] = { 0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31 };//这个静态的编写是因为GetMonthDay有可能会被频繁调用，那么monthDays数组就会不断的被创建，虽然这点消耗挺小，但是如果加上静态关键字，这个数组就会一直存在，不会被反复创建，节省资源。
        /*
        一个年份是闰年的条件如下：
        1.如果该年份能够被4整除，但不能被100整除，则为闰年。例如：2008年、2012年等。
        2.如果该年份能够被400整除，则也是闰年。例如：2000年、2400年等。
        */
        if (month == 2
            && ((year % 4 == 0) && ((year % 100 != 0) || (year % 400 == 0)))
            )
        {
            return 29;
        }
        return monthDays[month];
    }
    //1.全缺省的构造函数（使用了初始化列表）
    Date(int year = 1900, int month = 1, int day = 1)
        : _year(year),
        _month(month),
        _day(day)
    {
        if ((_month < 1) || (_month > 12) 
            || _day < 1 || (GetMonthDay(_year, _month) < _day))//检查日期是否非法
        {
            cout << "非法日期：";
            Print();
            cout << endl;
            //exit(-1);
        }
    }
    //2.拷贝构造函数
    //d2(d1)
    //Date(const Date& d)//实际上不用自己写
    //{
        //_year = d._year;
        //_month = d._month;
        //_day = d._day;
    //}
    //3.赋值运算符重载函数
    //d2 = d3 --> d2.operator=(&d2, d3)
    //Date& operator=(const Date& d)
    //{
    //    if (this != &d) 
    //    {
    //        _year = d._year;
    //        _month = d._month;
    //        _day = d._day;
    //    }
    //    return *this;//处理自我赋值的情况
    //}
    //4.析构函数
    //~Date()
    //{
    //    cout << "析构函数在这个日期类里面没有太大用处:~Data()" << endl;
    //}
    //5.符号重载函数
    //日期+=天数重载
    Date& operator+=(int day)
    {
        if (day < 0)
        {
            return *this -= (-day);
        }
        _day += day;//先不管3721，直接加上就行
        int days = GetMonthDay(_year, _month);//获取这个月的天数
        while (_day > days)//如果天数是不正确的就进入循环
        {
            _month++;//天数多了就加月份
            _day -= days;//并且把天数减小一些
            if (_month == 13)//如果月份超过了就加年份
            {
                _year++;
                _month = 1;
            }
            days = GetMonthDay(_year, _month);//重新获取正确的天数
        }
        return *this;
    }
    //日期+天数运算符重载
    Date operator+(int day)
    {
        Date cache(*this);//拷贝一份原有的*this
        return cache += day;
    }
    //日期-=天数运算符重载
    Date& operator-=(int day)
    {
        if (day < 0)
        {
            return *this += (-day);
        }
        _day -= day;//先不管3721，直接减去就行
        while (_day <= 0)//如果天数是不正确的就进入循环
        {
            _month--;//减去月份
            if (_month == 0)//如果月份为0了就减去年份，并且重置月份
            {
                _year--;
                _month = 12;
            }
            _day += GetMonthDay(_year, _month);//重新获取正确的天数
        }
        return *this;
    }
    //日期-天数运算符重载
    Date operator-(int day)
    {
        Date cache(*this);//拷贝一份原有的*this
        return cache -= day;
    }
    //前置++运算符重载
    Date& operator++()
    {
        return (*this) += 1;
    }
    //后置++运算符重载
    Date operator++(int)//展开为Date& operator++(const Date* this, int x)，这里的x会自动传值为0或者其他数，只要编译器能够识别即可，反正接受了也没在函数内使用没有影响，只是做一个区分
    {
        Date cache(*this);//拷贝一份原有的*this
        *this += 1;
        return cache;
    }
    //前置--运算符重载
    Date& operator--()
    {
        return (*this -= 1);
    }
    //后置--运算符重载
    Date operator--(int)
    {
        Date cache(*this);//拷贝一份原有的*this
        *this -= 1;
        return cache;
    }
    //>运算符重载
    bool operator>(const Date& d)
    {
        if (_year > d._year)
        {
            return true;
        }
        else if (_year == d._year)
        {
            if (_month > d._month)
            {
                return true;
            }
            else if (_month == d._month)
            {
                if (_day > d._day)
                {
                    return true;
                }
                else
                {
                    return false;
                }
            }
        }
        return false;
    }
    //==运算符重载
    bool operator==(const Date& d)
    {
        if (_year == d._year && _month == d._month && _day == d._day)
        {
            return true;
        }
        return false;
    }
    //!=运算符重载
    bool operator != (const Date& d)
    {
        if (*this == d)
        {
            return false;
        }
        return true;
    }
    //>=运算符重载
    bool operator >= (const Date& d)
    {
        if ((*this) > d || (*this) == d)
            return true;
        return false;
    }
    //<运算符重载
    bool operator < (const Date& d)
    {
        if ((*this) >= d)
            return false;
        return true;
    }
    //<=运算符重载
    bool operator <= (const Date& d)
    {
        if ((*this) > d)
            return false;
        return true;
    }
    //日期-日期=返回天数运算符重载
    int operator-(const Date& d)
    {
        //以下方法一、二是本质是没什么区别的，但是方法一返回的是正数，方法二返回的数带有符号
        //1.方法一
        //Date cache(d);
        //int flag = 0;
        //while ((*this) > cache)
        //{
        //    ++cache;
        //    ++flag;
        //}
        //while ((*this) < cache)
        //{
        //    --cache;
        //    ++flag;
        //}
        //return flag;
        //2.方法二
        Date max = *this;
        Date min = d;
        int flag = 1;
        if (*this < d)
        {
            max = d;
            min = *this;
            flag = -1;
        }
        int n = 0;
        while (min != max)
        {
            min++;
            n++;
        }
        return n * flag;
    }
    //6.打印日期类函数
    void Print(void)
    {
        cout << _year << "-" << _month << "-" << _day << endl;
    }

private:
    int _year;
    int _month;
    int _day;
};
```

# 4.日期类具体实现（精简）

这里省略了很多重复的注释，并且给出了一个完整的日期类。

```c++
#include <iostream>
#include <cassert>
using namespace std;
class Date
{
public:
    //获取某年某月的天数
    int GetMonthDay(int year, int month)
    {
        const static int monthDays[13] = { 0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31 };
        if (month == 2
            && ((year % 4 == 0) && ((year % 100 != 0) || (year % 400 == 0)))
            )
        {
            return 29;
        }
        return monthDays[month];
    }
    //1.全缺省的构造函数（使用了初始化列表）
    Date(int year = 1900, int month = 1, int day = 1)
        : _year(year),
        _month(month),
        _day(day)
    {
        if ((_month < 1) || (_month > 12)
            || _day < 1 || (GetMonthDay(_year, _month) < _day))
        {
            cout << "非法日期：";
            Print();
            cout << endl;
            //exit(-1);
        }
    }
    //2.运算符重载函数
    Date& operator+=(int day)
    {
        if (day < 0)
        {
            return *this -= (-day);
        }
        _day += day;
        int days = GetMonthDay(_year, _month);
        while (_day > days)
        {
            _month++;
            _day -= days;
            if (_month == 13)
            {
                _year++;
                _month = 1;
            }
            days = GetMonthDay(_year, _month);
        }
        return *this;
    }
    //日期+天数运算符重载
    Date operator+(int day)
    {
        Date cache(*this);
        return cache += day;
    }
    //日期-=天数运算符重载
    Date& operator-=(int day)
    {
        if (day < 0)
        {
            return *this += (-day);
        }
        _day -= day;
        while (_day <= 0)
        {
            _month--;
            if (_month == 0)
            {
                _year--;
                _month = 12;
            }
            _day += GetMonthDay(_year, _month);
        }
        return *this;
    }
    //日期-天数运算符重载
    Date operator-(int day)
    {
        Date cache(*this);
        return cache -= day;
    }
    //前置++运算符重载
    Date& operator++()
    {
        return (*this) += 1;
    }
    //后置++运算符重载
    Date operator++(int)
    {
        Date cache(*this);
        *this += 1;
        return cache;
    }
    //前置--运算符重载
    Date& operator--()
    {
        return (*this -= 1);
    }
    //后置--运算符重载
    Date operator--(int)
    {
        Date cache(*this);
        *this -= 1;
        return cache;
    }
    //>运算符重载
    bool operator>(const Date& d)
    {
        if (_year > d._year)
        {
            return true;
        }
        else if (_year == d._year)
        {
            if (_month > d._month)
            {
                return true;
            }
            else if (_month == d._month)
            {
                if (_day > d._day)
                {
                    return true;
                }
                else
                {
                    return false;
                }
            }
        }
        return false;
    }
    //==运算符重载
    bool operator==(const Date& d)
    {
        if (_year == d._year && _month == d._month && _day == d._day)
        {
            return true;
        }
        return false;
    }
    //!=运算符重载
    bool operator != (const Date& d)
    {
        if (*this == d)
        {
            return false;
        }
        return true;
    }
    //>=运算符重载
    bool operator >= (const Date& d)
    {
        if ((*this) > d || (*this) == d)
            return true;
        return false;
    }
    //<运算符重载
    bool operator < (const Date& d)
    {
        if ((*this) >= d)
            return false;
        return true;
    }
    //<=运算符重载
    bool operator <= (const Date& d)
    {
        if ((*this) > d)
            return false;
        return true;
    }
    //日期-日期=返回天数运算符重载
    int operator-(const Date& d)
    {
        Date max = *this;
        Date min = d;
        int flag = 1;
        if (*this < d)
        {
            max = d;
            min = *this;
            flag = -1;
        }
        int n = 0;
        while (min != max)
        {
            min++;
            n++;
        }
        return n * flag;
    }
    //6.打印日期类函数
    void Print(void)
    {
        cout << _year << "-" << _month << "-" << _day << endl;
    }
private:
    int _year;
    int _month;
    int _day;
};
```

另外，我们也可以给只读成员函数加上`const`，上述代码在碰到`const`的`Date`对象时，调用成员函数可能会出现权限放大的问题，您可以自行修改一下……

# 5.日期类测试样例（测试）

```c++
void test(void)
{
    //1.检测合法性是否成立
    cout << "1.检测合法性是否成立" << endl;
    Date d0(2023, 2, 31);
    //2.检测构造函数的运行
    cout << "2.检测构造函数的运行" << endl;
    Date d1;
    cout << "Data d1, d1:";
    d1.Print();
    cout << endl;

    //3.检测拷贝构造函数和赋值重载函数
    cout << "3.检测拷贝构造函数和赋值重载函数" << endl;
    Date d2(d1);
    cout << "Data d2(d1), d2:";
    d2.Print();
    Date d3(2000, 1, 1);
    cout << "Data d3(2000, 1, 1), d3:";
    d3.Print();
    d2 = d3;
    cout << "d2 = d3" << endl;
    cout << "d2 = ";
    d2.Print();
    cout << "d3 = ";
    d3.Print();
    cout << endl;

    //4.检测逻辑运算符
    cout << "4.检测逻辑运算符" << endl;
    int a = (d1 == d2);
    cout << "int a = (d1 == d2), a:";
    cout << a << endl;
    int b = (d1 != d3);
    cout << "int b = (d1 != d3), b:";
    cout << b << endl  << endl;

    //5.检测算术运算符
    cout << "5.检测算术运算符" << endl;
    d1 = d3;
    cout << "d1 = d3, d1:";
    d1.Print();

    int days = d1.GetMonthDay(2016, 1);
    cout << "days = d1.GetMonthDay(2016, 1), days = " << days << endl;

    d1 += 3653;
    cout << "d1 += 3653, d1:";
    d1.Print();

    d2 = d1 + 3653;
    cout << "d2 = d1 + 3653, d1:";
    d1.Print();
    cout << "d2 = d1 + 3653, d2:";
    d2.Print();

    d1 -= 3653;
    cout << "d1 -= 3653, d1:";
    d1.Print();

    d2 = d1 - 33456;
    cout << "d2 = d1 - 33456, d1:";
    d1.Print();
    cout << "d2 = d1 - 33456, d2:";
    d2.Print();

    cout << endl;
    Date d4(2023, 7, 22);

    cout << "(++d4).Print(), ++d4之前:";
    (++d4).Print();
    cout << "(++d4).Print(), ++d4之后:";
    d4.Print();

    cout << "d4++.Print(), d4++之前:";
    (d4++).Print();
    cout << "d4++.Print(), d4++之后:";
    d4.Print();

    cout << "(--d4).Print(), --d4之前:";
    (--d4).Print();
    cout << "(--d4).Print(), --d4之后:";
    d4.Print();

    cout << "(d4--).Print(), d4--之前:";
    (d4--).Print();
    cout << "(d4--).Print(), d4--之后:";
    d4.Print();
    cout << endl;

    //6.检测逻辑运算符
    cout << "6.检测逻辑运算符" << endl;
    Date d5(2016, 10, 6);
    Date d6(2016, 3, 4);
    Date d7(2010, 3, 4);
    Date d8(2010, 3, 4);
    cout << "d5 = ";
    d5.Print();
    cout << "d6 = ";
    d6.Print();
    cout << "d7 = ";
    d7.Print();
    cout << "d8 = ";
    d8.Print();
    cout << endl;

    if (d5 > d7)
    {
        cout << "d5 > d7" << endl;
    }
    if (d7 >= d8)
    {
        cout << "d7 >= d8" << endl;
    }
    if (d5 >= d8)
    {
        cout << "d5 >= d8" << endl;
    }
    if (d6 < d5)
    {
        cout << "d6 < d5" << endl;
    }
    if (d7 <= d8)
    {
        cout << "d7 <= d8" << endl;
    }
    if (d7 <= d6)
    {
        cout << "d7 <= d6" << endl;
    }
    cout << endl;

    //7.检测日期-日期
    cout << "7.检测日期-日期" << endl;

    Date d9(2023, 7, 27);
    Date d10(2003, 1, 1);
    cout << "d9 = ";
    d9.Print();
    cout << "d10 = ";
    d10.Print();
    cout << "d9 - d10 = " << d9 - d10 << endl;
    cout << "d10 - d9 = " << d10 - d9 << endl;
    cout << endl;

    Date d11(2016, 10, 6);
    Date d12(2023, 7, 22);
    cout << "d11 = ";
    d11.Print();
    cout << "d12 = ";
    d12.Print();
    cout << "d12 - d11 = " << d12 - d11 << endl;
    cout << "d11 - d12 = " << d11 - d12 << endl;
    cout << endl;

    Date d13(2015, 10, 6);
    Date d14(2015, 10, 6);
    cout << "d13 = ";
    d13.Print();
    cout << "d14 = ";
    d14.Print();
    cout << "d13 - d14 = " << d13 - d14 << endl;
    cout << "d14 - d13 = " << d14 - d13 << endl;
    cout << endl;

}
int main()
{
    test();
    return 0;
}
```

# 6.补充：流运算符重载问题

由于当前我们还没有学过`I/O流`，所以这一部分我单独拉了出来，您可以稍微看一下。

如果我们需要`Date`也可以实现下面的代码怎么办呢？

```c++
class Date
{/*...*/};
int main()
{
    Date d1;
    Date d2;
    std::cin >> d1 >> d2;
    std::cout << d1 << d2 << std::endl;
    return 0;
}
```

这个时候我们就需要对`<<`和`>>`进行重载，但是首先我们需要了解`cin`和`cout`的本质。

两者实际上分别是`istream`和`otream`类构建出来的对象，因此在对`<<`和`>>`重载的时候需要注意这两个类。

一般情况下流运算符也只能写成全局函数，如果写成成员函数会显得很怪异：

```c++
//流重载函数（尝试内置在类中）
istream& operator>>(istream& in)
{
    in >> _year >> _month >> _day;
    return in;
}
ostream& operator<<(ostream& out)
{
    out << _year << "-" << _month << "-" << _day << endl;
    return out;
}
//调用函数的时候就只能像下面这样调用
Date d1;
Date d2;
d1 >> (d2 >> cin);//变成d1.operator>>( (d2.operator>>(cin) )
d1 << (d2 << cout);//变成d1.operator<<( (d2.operator<<(cout) )
```

和普通的流运算符的使用有很大的不同，本质是因为`this`指针没办法改动位置，只能接受一个指向对象的指针，因此只能像下面这样书写代码：

```c++
//流重载函数（全局函数）
istream& operator>>(istream& in, Date& d)
{
    in >> d._year >> d._month >> d._day;
    return in;
}
ostream& operator<<(ostream& out, const Date& d)
{
    out << d._year << "-" << d._month << "-" << d._day;
    return out;
}
```

但是这样又有一个问题了，这两个重载的函数没有办法访问类私有变量（`private: _year;_month;_day;`），这个时候就需要在类里面加上友元。

```c++
//友元声明
class Date
{
    friend istream& operator>>(istream& in, Date& d);
    friend ostream& operator<<(ostream& out, const Date& d);
    /*...*/
}
```

为什么`C++`的要设定`cout`和`cin`，而不再使用`prinf()`等函数呢？就是为了更好得支持自定义类型，`C`的`printf()`还不够通用，尤其是后期涉及到范式编程的时候，这个问题就会更加凸显...
