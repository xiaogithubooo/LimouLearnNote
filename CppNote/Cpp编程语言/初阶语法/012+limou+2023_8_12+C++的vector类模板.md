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
