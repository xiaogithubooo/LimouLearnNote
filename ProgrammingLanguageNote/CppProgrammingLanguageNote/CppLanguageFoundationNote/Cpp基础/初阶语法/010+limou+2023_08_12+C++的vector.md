# 1.vector简介

`vector`说明白点就是一个顺序表，一个可以进行动态存储的数组，在学习`vector`之前，您要对顺序表有足够的了解，最好是造过“轮子”。

`vector`就像数组一样，对其中内部元素使用连续的存储位置，可以使用常规指针的偏移量来访问元素，但是和普通数组不同的是，`vector`序列容器的大小可以动态变化。

不过需要注意的是`vector`是一个类模板，而不是我们之前提到的`string`一样一个经过实例化的类。

```c++
template < class T, class Alloc = allocator<T> > class vector;//类模板声明
```

# 2.成员类型（Member types）

1. `T`是类模板的模板类型，由使用者决定

2. `Alloc`参数是有关空间配置器相关的设计，这个我们暂时不用理会，这个参数是方便使用者改写空间配置器，但是基本很少改动

# 3.成员函数（Member function）

## 3.1.构造函数vector()

### 3.1.1.default (1) 默认

```c++
explicit vector (const allocator_type& alloc = allocator_type());
```

### 3.1.2.fill (2) 填充

```c++
explicit vector (size_type n); 
vector (size_type n, const value_type& val, const allocator_type& alloc = allocator_type());
```

### 3.1.3.range (3) 范围

```c++
template <class InputIterator>
  vector (InputIterator first, InputIterator last, const allocator_type& alloc = allocator_type());
```

### 3.1.4.copy (4) 副本

```c++
vector (const vector& x);
vector (const vector& x, const allocator_type& alloc);
```

### 3.1.5.move (5) 移动 （5）

```c++
vector (vector&& x);
vector (vector&& x, const allocator_type& alloc);
```

### 3.1.6.initializer list (6) 初始值设定项列表 （6）

```c++
vector (initializer_list<value_type> il, const allocator_type& alloc = allocator_type());
```

在有使用过`string`类对象的基础上，我们加快一些进度，下面是构造函数的使用例子：

```c++
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    //vector (const allocator_type& alloc = allocator_type());
    vector<int> v1;//(1)默认创建一个空的vector容器
    //vector (size_type n);
    vector<double> v2(10);//(2)创建一个大小位10的vector容器

    //vector(size_type n, const value_type & val, const allocator_type & alloc = allocator_type());
    vector<char> v3(10, 'c');//(2)创建一个大小位10的vector容器

    //<class InputIterator> 
    //vector (InputIterator first, InputIterator last, const allocator_type& alloc = allocator_type());
    vector<char> v4(v3.begin() + 1, v3.end() - 1);//(3)
    string str("abcdef");
    vector<char> vec(str.begin() + 1, str.end() - 1);//(3)

    //vector(const vector & x);
    vector<char> v5(v3);//(4)

    //vector (vector&& x);
    vector<char> v6(move(v3));//(5)

    //vector (initializer_list<value_type> il, const allocator_type& alloc = allocator_type());
    vector<float> v7{1, 3.1, 2.3, 4.5};//(6)

    return 0;
}
```

## 3.2.析构函数\~vecter()

就是销毁`vecter`容器对象。

## 3.3.赋值重载

### 3.3.1.copy (1) 副本

```c++
vector& operator= (const vector& x);
```

### 3.3.2.move (2) 移动

```c++
vector& operator= (vector&& x);
```

### 3.3.3.initializer list (3) 初始值设定项列表

```c++
vector& operator= (initializer_list<value_type> il);
```

下面也是使用一段代码来深入了解赋值重载的应用：

```c++
int main()
{
    vector<int> v1{ 1, 2, 5, 0, 9, 4 };
    vector<int> v2;
    v2 = v1;
    vector<int>::iterator it = v2.begin();//(1)
    while (it != v2.end())
    {
        cout << *it << " ";
        it++;
    }
    vector<int> v3{ 1, 3, 4 };
    vector<int> v4;
    v4 = move(v3);//(2)

    vector<double> v5;
    v5 = { 3.14, 3.2, 4.5, 9.0, 2.1 };//(3)

    return 0;
}
```

# 4.迭代器（Iterators）

## 4.1.begin()

```c++
iterator begin() noexcept;
const_iterator begin() const noexcept;
```

## 4.2.end()

```c++
iterator end() noexcept;
const_iterator end() const noexcept;
```

## 4.3.rbegin()

```c++
reverse_iterator rbegin() noexcept;
const_reverse_iterator rbegin() const noexcept;
```

## 4.4.rend()

```c++
reverse_iterator rend() noexcept;
const_reverse_iterator rend() const noexcept;
```

同样使用一些例子来演示一下：

```c++
int main()()
{
    vector<int> v1 = { 1, 5, 2, 7, 9 };
    vector<int>::iterator it = v1.begin();
    while (it != v1.end())
    {
        cout << (*(it++))++ << " ";
    }
    cout << endl;
    for (auto in : v1)
    {
        cout << in << " ";
    }
    cout << endl;

    vector<float> v2 = { 1.2, 3.0, 4.3, 0.4 };
    auto rit = v2.rbegin();
    while (rit != v2.rend())
    {
        cout << *rit << " ";
        rit++;
    }
}
```

除此之外还有一些常量迭代器（`cbegin()`、`cend()、``crbegin()`、`crend()`），这里就不一一介绍使用了，和之前讲解的`string`类的迭代器类似。

# 5.容积（Capacity）

## 5.1.size()与capacity()与max_size()

```c++
size_type size() const noexcept;
size_type capacity() const noexcept;
size_type max_size() const noexcept;//这个函数是被写死了，没啥用，之前string类里也有提到
```

## 5.3.resize()与reserve()与shrink_to_fit()

```c++
void resize (size_type n);void resize (size_type n, const value_type& val);
void reserve (size_type n);
void shrink_to_fit();//缩小容量来适应对象的实际大小，不过不建议使用，这是异地缩容，以时间换空间
```

## 5.4.empty()

```c++
bool empty() const noexcept;
```

判空，没什么好说的。

# 6.元素访问（Element access）

## 6.1.operator\[\]()与at()

```c++
reference operator[] (size_type n);
const_reference operator[] (size_type n) const;

reference at (size_type n);
const_reference at (size_type n) const;
```

不过在使用`[]`的时候需要注意，`[]`内部有`assert()`检查，不得超过`size()`的值，因此和数组有些不同的是，哪怕提前开辟好空间，也没有办法直接使用`[]`访问。

```c++
//错误写法
int main()
{
    vector<int> v;
    v.reserve(10);
    for (int i = 0; i < 10; i++)
    {
        v[i] = i;
    }
    return 0;
}
```

```c++
//正确写法
int main()
{
    vector<int> v;
    v.reserve(10);
    for (int i = 0; i < 10; i++)
    {
        v.push_back(i);
    }
    for (int i = 0; i < 10; i++)
    {
        cout << v[i] << " ";
    }
    return 0;
}
```

或者调用`resize()`：

```c++
int main()
{
    vector<int> v;
    v.resize(10);
    for (int i = 0; i < size(); i++)
    {
        v[i] = i;
        cout << v[i] << " ";
    }
    return 0;
}
```

## 6.2.front()与back()

```c++
reference front();
const_reference front() const;

reference back();
const_reference back() const;
```

返回首尾元素的引用。

## 6.3.data()

```c++
value_type* data() noexcept;
const value_type* data() const noexcept;
```

返回指向`vector`内部用于存储其拥有元素的内存数组的直接指针。

# 7.修改器（Modifiers）

## 7.1.assign()

## 7.2.push_back()和pop_back()

```c++
void push_back (const value_type& val);
void push_back (value_type&& val);
```

## 7.4.insert()

### 7.4.1.single element (1) 单元素

```c++
iterator insert (const_iterator position, const value_type& val);
```

### 7.4.2.fill (2) 填充

```c++
iterator insert (const_iterator position, size_type n, const value_type& val);
```

### 7.4.3.range (3) 范围

```c++
template <class InputIterator>
iterator insert (const_iterator position, InputIterator first, InputIterator last);
```

### 7.4.4.move (4) 移动

```c++
iterator insert (const_iterator position, value_type&& val);
```

### 7.4.5.initializer list (5) 初始值设定项列表

```c++
iterator insert (const_iterator position, initializer_list<value_type> il);
```

## 7.5.erase()和clear()

```c++
iterator erase (const_iterator position);//擦除一个元素
iterator erase (const_iterator first, const_iterator last);//擦除范围内的元素

void clear() noexcept;//删除所有的元素，使得size()值位0，但是不清楚capacity()的值
```

我们借此测试一下`vector`的扩容机制：

```c++
int main()
{
    vector<int> v;
    for (int i = 0; i < 100; i++)
    {
        v.push_back(1);
        cout << "size = " << v.size() << " " << "capacity = " << v.capacity() << endl;
    }
}//在VS2022下大概是1.5倍对齐，如果是Linux的G++有大概是2倍，当然要根据不同平台和编译器来决定
```

> 补充：我们可以看到`vector`类模板没有设计头插和头删的接口，因为这两个接口的效率很低，并且实在是需要如此操作，可以使用`insert()`来做到。

## 7.6.swap()

```c++
void swap (vector& x);
```

`vector`还自己写了一个`swap()`，同样也是对比全局的`std::swap()`更加高效一些。

## 7.7.emplace()和emplace_back()

这两个暂时不用理会，前者使用起来和`insert()`类似，后者和`push_back`类似。在某些场景下效率高一些，需要使用的时候再来查询文档把。

# 8.配置器（Allocator）

以后再提及

# 9.非成员函数重载（Non-menber function overloads）

主要是关系运算符的重载：

```c++
template <class T, class Alloc>
  bool operator== (const vector<T,Alloc>& lhs, const vector<T,Alloc>& rhs);

template <class T, class Alloc>
  bool operator!= (const vector<T,Alloc>& lhs, const vector<T,Alloc>& rhs);

template <class T, class Alloc>
  bool operator<  (const vector<T,Alloc>& lhs, const vector<T,Alloc>& rhs);

template <class T, class Alloc>
  bool operator<= (const vector<T,Alloc>& lhs, const vector<T,Alloc>& rhs);

template <class T, class Alloc>
  bool operator>  (const vector<T,Alloc>& lhs, const vector<T,Alloc>& rhs);

template <class T, class Alloc>
  bool operator>= (const vector<T,Alloc>& lhs, const vector<T,Alloc>& rhs);
```

还有一个交换内容用的`swap()`:

```c++
template <class T, class Alloc>
  void swap (vector<T,Alloc>& x, vector<T,Alloc>& y);
```

# 10.相关练习题目

## 10.1.[二叉树的前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/)

```c++
class Solution
{
public:
    void _preorderTraversal(TreeNode* root, vector<int>& v)
    {
        if (root == nullptr)
        {
            return;
        }
        v.push_back(root->val);
        _preorderTraversal(root->left, v);
        _preorderTraversal(root->right, v);
    }
    vector<int> preorderTraversal(TreeNode* root)
    {
        vector<int> v;
        _preorderTraversal(root, v);
        return v;
    }
};
```

## 10.2.[只出现一次的数字](https://leetcode.cn/problems/single-number/)

```c++
class Solution
{
public:
    int singleNumber(vector<int>& nums)
    {
        int number = 0;
        for(auto in : nums)
        {
            number ^= in;
        }
        return number;
    }
};
```

## 10.3.[杨辉三角](https://leetcode.cn/problems/pascals-triangle/)

```c++
class Solution
{
public:
    vector<vector<int>> generate(int numRows)//numRows=5
    {
        vector<vector<int>> vs(numRows);//该容器，每一个元素都是一个容器
        vs[0].resize(1);
        vs[0][0] = 1;//1
        for (int i = 0; i < numRows - 1; i++)//vs[0]->vs[4]，每一次都要由本层算出下一层
        {
            for (int j = 0; j <= i; j++)//vs[i][0]->vs[i][i]
            {
                vs[i + 1].resize(i + 2);//开辟好下一层的空间，并且宝藏首尾都是元素1

                vs[i + 1][0] = 1;//下一层的首尾都为1
                vs[i + 1][i + 1] = 1;

                if (j != 0 && j != i + 1)//保证不是下一层的首位的情况下
                {
                    vs[i + 1][j] = vs[i][j - 1] + vs[i][j];
                }
            }
        }
        return vs;
    }
};
```

> 补充1：另外我们发现`vector`没有`find()`系列的函数，这是为什么呢？因为在以后要介绍的算法库中就有相关的接口，即`std::find()`。
> 
> 而`string`类也可以使用这个，但是它依旧有一个自己的`fund()`，一是因为出现比较早，二是这个`std::fund()`不够满足`string`类的需求（比如查找子串）
> 
> ```c++
> int main()
> {
>     vector<int> v = { 1, 3, 3, 4, 5 };
>     auto it = find(v.begin(), v.end(), 4);
>     if (it != v.end())
>         cout << "找到了！" << * it << endl;
>     else
>         cout << "没有找到！" << endl;
>     return 0;
> }
> ```
> 
> 注意2：实际上不仅没有`find()`系列，很多的容器也没有提供流插入和流提取，直接打印这些容器比较少（打印`string`比较多）

## 10.4.[电话号码的字母组合](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)

```c++
class Solution
{
    const char* numToStr_Arr[10] = { "","","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz" };//“数字->字母”的映射表
public:
    void _combine(string digits, int i, string combineStr, vector<string>& ret)
    {
        if(i == digits.size())
        {
            ret.push_back(combineStr);
            return;//拼好了
        }
        int num = digits[i] - '0';//取得数字'2'
        string str = numToStr_Arr[num];//str="abc"
        for(auto ch : str)//分别取得'a'、'b'、'c'
        {
            _combine(digits, i + 1, combineStr + ch, ret);//_combine(digits, 1, 'a', ret)
        }
    }
    vector<string> letterCombinations(const string digits)//digits="258"
    {
        //digits是数字序列
        //ret是结果顺序表
        vector<string> ret;
        if(digits.empty())
        {
            return ret;
        }
        string combineStr;
        _combine(digits, 0, combineStr, ret);
        return ret;
    }
};
```



下面代码是有关于`vector`的一些基础实现，和库里的函数可能有较大的不同，但是基本可以实现`vector`的很多功能。

```c++
#include <iostream>
#include <cassert>
#include <string>
/*
看源代码不要上来就一行一行看，先看文档再进行使用，再整理框架（梳理类、变量、接口），然后“连蒙带猜”验证细节
*/
namespace limou
{
    template <typename T>
    class vector
    {
    public:
        typedef T* iterator;
        typedef const T* const_iterator;
    public:
        //1.Menber function
        vector()
            : _start(nullptr)
            , _finish(nullptr)
            , _end_of_storage(nullptr)
        {}
        template <typename InputIterator>//这里之所以又嵌套一个类模板的原因是：能够使用多种种类的迭代器，而不当当是T类型的迭代器
        vector(InputIterator first, InputIterator last)
            : _start(nullptr)
            , _finish(nullptr)
            , _end_of_storage(nullptr)
        {
            while (first != last)
            {
                push_back(*first);
                ++first;
            }
        }
        vector(const vector<T>& v)
            : _start(nullptr)
            , _finish(nullptr)
            , _end_of_storage(nullptr)
        {
            reserve(v.capacity());
            for (auto& e : v)
            {
                push_back(e);
            }
        }
        vector(size_t n, const T& val = T())//构造一个匿名对象，并且自动初始化
        {
            reserve(n);
            for (size_t i = 0; i < n; i++)
            {
                push_back(val);
            }
        }
        vector(int n, const T& val = T())//这个函数是专门给部分类型准备的（比如int）不重载就会调用成迭代器初始化
        {
            reserve(n);
            for (int i = 0; i < n; i++)
            {
                push_back(val);
            }
        }
        vector<T>& operator=(vector tmp)//先拷贝一份，另外这里提一嘴，在类里面写成vector和vector<T>是一样的，当然我们建议写全
        {
            swap(tmp);
            return *this;
        }
        ~vector()
        {
            delete[] _start;
        }
        //2.Iterator
        iterator begin()
        {
            return _start;
        }
        iterator end()
        {
            return _finish;
        }
        const_iterator begin() const
        {
            return _start;
        }
        const_iterator end() const
        {
            return _finish;
        }
        //3.Capacity
        size_t size()
        {
            return _finish - _start;
        }
        size_t capacity() const
        {
            return _end_of_storage - _start;
        }
        //3.1.使用memcpy()会引发深浅拷贝的问题，因此不可以使用这个函数
        //void reserve(size_t n)
        //{
        //    if (n > capacity())//如果指定的数字大于现有容量就扩容
        //    {
        //        T* tmp = new T[n];
        //        size_t sz = size();
        //        if (_start)//如果不是空容器
        //        {
        //            memcpy(tmp, _start, sizeof(T) * sz);
        //            delete[] _start;//这里有可能因为浅拷贝而出事
        //        }
        //        _start = tmp;//由于这里更换成了tmp
        //        _finish = tmp + sz;//而这里原本的_finish是指向别的空间指针，所以必须要修改
        //        _end_of_storage = tmp + n;//同理这里也做修改
        //    }
        //}
        //3.2.拷贝数据如果使用拷贝构造
        void reserve(size_t n)
        {
            if (n > capacity())//如果指定的数字大于现有容量就扩容
            {
                T* tmp = new T[n];
                size_t sz = size();
                if (_start)//如果不是空容器
                {
                    //memcpy(tmp, _start, sizeof(T) * sz);
                    for (size_t i = 0; i < sz; i++)
                    {
                        tmp[i] = _start[i];
                        //这样子做可以自动调用不同类型的深拷贝（赋值运算重载），
                        //这个深拷贝有可能是我们写的也有可能是库里的……
                    }
                    delete[] _start;
                }
                _start = tmp;//由于这里更换成了tmp
                _finish = _start + sz;//而这里原本的_finish是指向别的空间指针，所以必须要修改
                _end_of_storage = _start + n;//同理这里也做修改
            }
        }
        //3.3.当然，还有一种方法是使用引用计数，不过这个方法我们暂时不讲（这个地方如果是类似string类型使用引用计数效率就会有很大的提高）
        //3.4.另外，库中的实现使用了拷贝构造，因为人家使用的是定位new，因此可以调用，而我们自己是直接使用new的，已经存在两个对象了，只能使用赋值重载
        void resize(size_t n, const T& val = T())
            //这里给了默认匿名参数T()，
            //注意匿名对象具有常属性，
            //这也是为什么在C++之后支持“int i = int(3);”
            //这种语法就是因为：
            //在类模板出现后没有办法确认是内置类型，还是自定义类型
            //并且还会给与自定义类型默认值
        {
            if (n <= size())
            {
                _finish = _start + n;
                return;
            }
            reserve(n);
            while (_finish < _start + n)
            {
                *_finish = val;
                _finish++;
            }
        }
        //4.Element access
        T& operator[](size_t pos)
        {
            assert(pos < size());
            return _start[pos];
        }
        const T& operator[](size_t pos) const
        {
            assert(pos < size());
            return _start[pos];
        }
        //5.Modifiers
        //注意insert()和erase()在使用过后迭代器都会失效，在库中就可以利用返回值来解决这个问题，我们自己实现的就没有实现insert()的返回值
        void push_back(const T& x)
        {
            //if (_finish == _end_of_storage)//如果容量不足
            //{
            //    reserve(capacity() == 0 ? 4 : capacity() * 2);//2倍容量
            //}
            //*_finish = x;
            //_finish++;
            insert(end(), x);
        }
        void insert(iterator pos, const T& x)
        {
            //检验是否合法
            assert(pos >= _start);
            assert(pos <= _finish);
            if (_finish == _end_of_storage)//如果容量不足
            {
                size_t len = pos - _start;
                reserve(capacity() == 0 ? 4 : capacity() * 2);//2倍容量
                pos = _start + len;
                //上面这里还解决了迭代器失效的问题。
                //由于扩容导致的问题，pos指向旧空间的某个位置时，
                //旧空间没了，没能指向新空间，因此这里必须进行更新。

                //但是由于这里没有办法使用引用（传过来的pos迭代器是一个值），
                //因此本函数insert()使用过后迭代器就会失效，只能保证在本函数内有效。
            }

            iterator end = _finish - 1;
            //1,2,3,4,5
           //^       ^
            while (end >= pos)
            {
                *(end + 1) = *end;
                end--;
            }
            *pos = x;
            _finish++;
        }
        //1.第一种erase的书写解决不了迭代器失效的问题
        //void erase(iterator pos)
        //{
        //    //检验是否合法
        //    assert(pos >= _start);
        //    assert(pos <= _finish);
        //    //{1,2,3,4,5}
        //    //pos->3,it->4,{1,2,4,5}
        //    //{1},pos->1,it=_finish
        //    iterator it = pos + 1;
        //    while (it < _finish)
        //    {
        //        *(it - 1) = *it;
        //        it++;
        //    }
        //    --_finish;
        //}
        //2.第二种可以解决迭代器失效的问题，返回的是原pos的后一个数据的迭代器，也就是删除后pos的位置
        iterator erase(iterator pos)
        {
            //检验是否合法
            assert(pos >= _start);
            assert(pos <= _finish);
            //{1,2,3,4,5}
            //pos->3,it->4,{1,2,4,5}
            //{1},pos->1,it=_finish
            iterator it = pos + 1;
            while (it < _finish)
            {
                *(it - 1) = *it;
                it++;
            }
            --_finish;
            return pos;
        }
        //6.other
        void swap(vector<int>& v)
        {
            std::swap(_start, v._start);
            std::swap(_finish, v._finish);
            std::swap(_end_of_storage, v._end_of_storage);
        }
    private:
        //三个迭代器成员变量
        iterator _start;//指向“存储空间”或者“存储”开始的位置
        iterator _finish;//指向“存储”结束的位置
        iterator _end_of_storage;//指向“存储空间”结束的位置
    };
    void test_1()
    {
        vector<int> v1;

        for (int i = 0; i < 10; i++)
        {
            v1.push_back(i);
            std::cout << v1[i]++ << " ";
        }
        std::cout << std::endl;

        vector<int>::iterator it = v1.begin();
        while (it != v1.end())
        {
            std::cout << (*it++)++ << " ";
        }
        std::cout << std::endl;

        for (auto in : v1)
        {
            std::cout << in << " ";
        }

        const vector<float> v2;

        //for (int i = 0; i < 10; i++)
        //{
        //    v2.push_back((float)i + 0.1);
        //    std::cout << v2[i]++ << " ";
        //}
    }
    void test_2()
    {
        vector<int> v1;
        v1.resize(5);
        for (auto in : v1)
        {
            std::cout << in << " ";
        }std::cout << std::endl;

        vector<int*> v2;
        v2.resize(5);
        for (auto p : v2)
        {
            std::cout << (void*)p << " ";
        }std::cout << std::endl;

        vector<std::string> v3;
        v3.resize(5);
        for (auto str3 : v3)
        {
            std::cout << "空字符=[" << str3 << "]" << " ";
        }std::cout << std::endl;

        vector<std::string> v4;
        v4.resize(5, "abcd");
        for (auto str4 : v4)
        {
            std::cout << "字符=[" << str4 << "]" << " ";
        }std::cout << std::endl;
    }
    void test_3()
    {
        vector<int> v1;
        v1.insert(v1.begin(), 1);
        v1.insert(v1.begin() + 1, 2);
        v1.insert(v1.begin() + 2, 3);
        for (auto in : v1)
        {
            std::cout << in << " ";
        }
        std::cout << std::endl;
    }
    void test_4()
    {
        vector<int> v1;
        v1.push_back(1);//内部使用了一次insert()因此迭代器失效
        std::cout << v1[v1.size() - 1] << " ";
        v1.push_back(2);//但是又重新使用insert()的时候重新计算了迭代器，使用后迭代器又失效了
        std::cout << v1[v1.size() - 1] << " ";
        v1.push_back(3);
        std::cout << v1[v1.size() - 1] << " ";
        std::cout << std::endl;

        vector<int> v2;
        v2.insert(v2.begin(), 1);//内部使用了一次insert()因此迭代器失效
        std::cout << v2[v2.size() - 1] << " ";
        v2.insert(v2.begin(), 2);//但是又重新使用insert()的时候重新计算了迭代器，使用后迭代器又失效了
        std::cout << v2[v2.size() - 2] << " ";
        v2.insert(v2.begin(), 3);
        std::cout << v2[v2.size() - 3] << " ";
        std::cout << std::endl;

        //错误示例（因为迭代器失效）
        vector<int> v3;
        v3.insert(v3.begin(), 4);
        v3.insert(v3.begin(), 3);
        v3.insert(v3.begin(), 2);
        vector<int>::iterator it = v3.begin();
        v3.insert(it, 1);//这个语句过后迭代器失效了
        v3.insert(it, 0);
        v3.insert(it, 0);
        for (int i = 0; i < 7; i++)
        {
            std::cout << v3[i] << " ";
        }std::cout << std::endl;
    }
    void test_5()
    {
        ////1.使用erase()的时候一般不会出现迭代器失效的问题
        //vector<int> v;
        //v.push_back(0);
        //std::cout << v[0] << std::endl;
        //v.erase(v.begin());
        //v.push_back(1);
        //v.push_back(2);
        //v.push_back(3);
        //v.push_back(4);
        //vector<int>::iterator it = v.begin();
        //while (it != v.end())
        //{
        //    std::cout << *it << " ";
        //    it++;
        //}

        //2.但是有一种情况会很危险
        //假设我们要删除一组数据中的偶数
        //可以试一下数据
        //{1,2,3,4,5}正常
        //{1,2,3,4,5,6}奔溃
        //{2,2,3,4,5}结果不对
        vector<int> vbug;
        vbug.push_back(1);
        vbug.push_back(2);
        vbug.push_back(3);
        vbug.push_back(4);
        vbug.push_back(5);
        vbug.push_back(6);
        vbug.push_back(7);
        vbug.push_back(8);
        vbug.push_back(9);
        vbug.push_back(10);

        auto it = vbug.begin();

        //1.错误使用
        //while (it != vbug.end())
        //{
        //    if (*it % 2 == 0)
        //    {
        //        vbug.erase(it);
        //    }
        //    //对于{1,2,3,4,5,6,7,8,9,10}
        //    //当最后一步只剩下{1,3,5,7,9,10}的时候
        //    //it->10,_finish->10后面的空间，因此在erase的时候，删除10的步骤就是_finish--
        //    //但是循环条件处的end()指向的位置还是原本的_finish位置
        //    it++;
        //    //而上面的一步又使得it++,it->旧的_finish
        //    //而回到循环条件的时候end()重新进行了计算（更新为新的_finish--）
        //    //此时it和end()错位，*it访问了野指针
        //  //导致迭代器失效
        //}

        //for (auto e : vbug)
        //{
        //    std::cout << e << " ";
        //}
        //std::cout << std::endl;

        //2.debug后
        while (it != vbug.end())
        {
            if (*it % 2 == 0)
            {
                vbug.erase(it);
            }

            if (it == vbug.end())
                break;
            it++;
        }

        for (auto e : vbug)
        {
            std::cout << e << " ";
        }
        std::cout << std::endl;

        //3.使用总结
        //但是实际上如果数据存在连续的偶数，即使是debug后的版本也会出现问题（上面代码能过只是巧合）
        //因此在后期就会引入一些智能指针的解决方案
        //因此我们认为使用erase()后会导致之前的迭代器失效
     }
    //关于迭代器失效的其中一个解决方案就是修改返回值的类型
    void test_6()
    {
        vector<int> v;
        v.push_back(1);
        v.push_back(2);
        v.push_back(2);
        v.push_back(3);
        v.push_back(4);
        v.push_back(5);
        v.push_back(6);
        v.push_back(6);

        vector<int>::iterator it = v.begin();
        while (it != v.end())
        {
            if (*it % 2 == 0)
            {
                it = v.erase(it);
            }
            else
            {
                it++;
            }
        }
        for (int i = 0; i < v.size(); i++)
        {
            std::cout << v[i] << " ";
        }
    }
    void test_7()
    {
        vector<std::string> v1;
        v1.push_back("1111111111111111111111");
        v1.push_back("1111111111111111111111");
        v1.push_back("1111111111111111111111");
        v1.push_back("1111111111111111111111");
        v1.push_back("1111111111111111111111");
        for (auto e : v1)
        {
            std::cout << e << " ";
        }
        std::cout << std::endl;
    }
    void test_8()
    {
        vector<int> v1;
        v1.push_back(1);
        v1.push_back(2);
        v1.push_back(3);
        v1.push_back(4);
        v1.push_back(5);
        vector<int> v2(v1);
        for (auto& e : v1)
        {
            std::cout << e << " ";
        }
        std::cout << std::endl;
        for (auto& e : v2)
        {
            std::cout << e << " ";
        }
        std::cout << std::endl;

        vector<int> v3;
        v3 = v2;
        for (auto& e : v3)
        {
            std::cout << e << " ";
        }
        std::cout << std::endl;
    }
    void test_9()
    {
        vector<int> v1;
        v1.push_back(1);
        v1.push_back(2);
        v1.push_back(3);
        v1.push_back(4);
        v1.push_back(5);
        std::string str("abcdef");

        vector<int> v2(v1.begin(), v1.end());
        for (int i = 0; i < v2.size(); i++)
        {
            std::cout << v2[i] << " ";
        }std::cout << std::endl;

        vector<int> v3(str.begin() + 1, str.end() - 1);
        for (int j = 0; j < v3.size(); j++)
        {
            std::cout << v3[j] << " ";
        }std::cout << std::endl;
    }
    void test_10()
    {
        vector<int> v1(10, 1);
        vector<std::string> v2(5, "xxxx");
        for (int i = 0; i < v1.size(); i++)
        {
            std::cout << v1[i] << " ";
        }
        std::cout << std::endl;
        for (int j = 0; j < v2.size(); j++)
        {
            std::cout << v2[j] << " ";
        }
        std::cout << std::endl;
    }

}
/*
vector没有流重载，因为很奇怪，您无法知道按照什么样的格式进行输出和输入
*/
```

