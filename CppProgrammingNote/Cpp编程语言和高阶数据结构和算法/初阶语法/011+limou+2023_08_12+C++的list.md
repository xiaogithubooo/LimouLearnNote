# 1.list的基础认知

`list`实际上是一个带头双向链表的容器，允许在序列内的任何位置执行时间复杂度为`O(1)`插入和删除操作。

```c++
template < class T, class Alloc = allocator<T> > class list;
```

# 2.成员函数（Member functions）

## 2.1.构造函数

## 2.2.析构函数

## 2.3.拷贝构造和赋值重载

# 3.迭代器（Iterators）

# 4.容积（Capacity）

## 4.1.empty()

测试容器是否为空。

## 4.2.size()

返回大小。

## 4.3.max_size

返回最大大小。

# 5.元素访问（Element access）

## 5.1.front()

访问第一个元素。

## 5.2.back()

访问最后一个元素。

# 6.修改器（Modifiers）

## 6.1.assign()

将新内容分配给容器。

## 6.2.emplace_front()

在开头构造和插入元素。

## 6.3.push_front()

在开头插入元素。

## 6.4.pop_front()

删除第一个元素。

## 6.5.emplace_back()

在末尾构造和插入元素。

##  6.6.push_back()

在末尾添加元素。

## 6.7.pop_back()

删除最后一个元素。

## 6.8.emplace()

构造和插入元素。

## 6.9.insert()

插入元素。

## 6.10.erase()

擦除元素。

## 6.11.swap()

交换内容。

## 6.12.resize()

更改大小。

## 6.13.clear()

内容清晰。

# 7.操作（Operations）

## 7.1.splice()

将元素直接从一个列表拼接到另一个列表。

## 7.2.remove()

删除具有特定值的元素。

## 7.3.remove_if()和remove_if()

删除满足条件的元素。

## 7.4.unique()

在有序的链表中去除掉重复的节点。

## 7.5.merge()

将两个有序链表归并在一起（取小的节点尾插）。

## 7.6.sort()

这个排序区别与算法内部的排序有所区分，并且链表不可以直接使用算法库里的`sort()`，这涉及到迭代器的分类。`list`的成员函数`sort()`底层是归并排序。并且该函数在大数据量的情况下效率也一般，只是使用上比较方便（在实践中直接对链表排序也是挺少见的，更常见的做法是：将`list`中的数据拷贝到`vector`里，在`vector`中进行排序，再拷贝回去，而拷贝数据的代价并不算很大）。

> 在功能上，迭代器分为：1.正向 2.反向
> 
> 在性质上，迭代器分为：
> 
> 1. 单向（只支持++，比如哈希表、单链表）
> 
> 2. 双向（支持++/--，比如双向链表、红黑树（map和set））
> 
> 3. 随机（++/--/+/-，vector、string、deque）

## 7.7.reverse()



# 8.感测器（Observers）

## 8.1.get_allocator()

## 8.2.get_allocator()

# 9.非成员函数重载（Non-member function overloads）



> 补充：在`list`的实现中，由于是链表，所以使用`[]`效率很低，因此并没有重载这一个操作符。





`list`的类模板模拟实现

```c++
#pragma once
#include <iostream>
#include <string>
#include <vector>
using std::cout; 
using std::cin;
using std::endl;
namespace limou
{
    //1.Node节点结构体
    template <typename T>
    struct list_node
    {
        T _data;
        list_node<T>* _next;
        list_node<T>* _prev;
        //构造函数
        list_node(const T& x = T())//注意不能给0，因为不一定是int，这个内置类型有了模板之后就升级了，也有构造函数了
            : _data(x)
            , _next(nullptr)
            , _prev(nullptr)
        {}
    };

    //2.迭代器结构体（合二为一）

    //2.1.可修改迭代器
    //template<typename T>
    //struct __list_iterator
    //{
    //    typedef list_node<T> Node;
    //    typedef __list_iterator<T> iterator;
    //    Node* _node;
    //
    //    //构造函数
    //    __list_iterator(Node* x)
    //        : _node(x)
    //    {}
    //
    //    iterator& operator++()
    //    {
    //        _node = _node->_next;
    //        return *this;
    //    }
    //    iterator operator++(int)
    //    {
    //        iterator tmp(*this);
    //        _node = _node->_next;
    //        return tmp;
    //    }
    //    iterator& operator--()
    //    {
    //        _node = _node->_prev;
    //        return *this;
    //    }
    //    iterator operator--(int)
    //    {
    //        iterator tmp(*this);
    //        _node = _node->_prev;
    //        return tmp;
    //    }
    //    T& operator*()
    //    {
    //        return _node->_data;
    //    }
    //    T* operator->()//这个"->"符重载是比较奇怪的
    //    {
    //        return &_node->_data;
    //        
    //        //假设某个自定义类A(内部又成员_a)使用了list<A> li来存储A类型对象，
    //        //list<A>::iterator it = li.begin()
    //        //而我们解引用迭代器是为了得到“节点内部的A类型内部的_a”
    //        //cout << *(li)._a << endl;    //没问题（这里使用了两次解引用）
    //        //cout << li->_a << endl;    //出现问题（必须重载"->"）
    //        //cout << li.operator->()->_a << endl;//由于可读性不好，编译器特殊化后，省略了一个"->"
    //    }
    //    bool operator!=(const iterator& s)
    //    {
    //        return _node != s._node;
    //    }
    //    bool operator==(const iterator& s)
    //    {
    //        return _node == s._node;
    //    }
    //};
    ////2.2.只可读迭代器
    //template<typename T>
    //struct __list_const_iterator
    //{
    //    //需要注意的是，常量迭代器不是在迭代器类型前加上const就可以了，否则迭代器本身没有办法进行迭代修改，进而进行读取
    //    typedef list_node<T> Node;
    //    typedef __list_const_iterator<T> const_iterator;
    //    Node* _node;
    //
    //    //构造函数
    //    __list_const_iterator(Node* x) 
    //        : _node(x)
    //    {}
    //
    //    const_iterator& operator++()
    //    {
    //        _node = _node->_next;
    //        return *this;
    //    }
    //    const_iterator operator++(int)
    //    {
    //        const_iterator tmp(*this);
    //        _node = _node->_next;
    //        return tmp;
    //    }
    //    const_iterator& operator--()
    //    {
    //        _node = _node->_prev;
    //        return *this;
    //    }
    //    const_iterator operator--(int)
    //    {
    //        const_iterator tmp(*this);
    //        _node = _node->_prev;
    //        return tmp;
    //    }
    //    const T& operator*()
    //    {
    //        return _node->_data;
    //    }
    //    const T* operator->()//这个"->"符重载是比较奇怪的
    //    {
    //        return &_node->_data;
    //        //假设某个自定义类A(内部又成员_a)使用了list<A> li来存储A类型对象，
    //        //list<A>::iterator it = li.begin()
    //        //而我们解引用迭代器是为了得到“节点内部的A类型内部的_a”
    //        //cout << *(li)._a << endl;    //没问题（这里使用了两次解引用）
    //        //cout << li->_a << endl;    //出现问题（必须重载"->"，但是只有一次解引用了）
    //        //cout << li.operator->()->_a << endl;//由于可读性不好，编译器特殊化后，省略了一个"->"
    //    }
    //    bool operator!=(const const_iterator& s)
    //    {
    //        return _node != s._node;
    //    }
    //    bool operator==(const const_iterator& s)
    //    {
    //        return _node == s._node;
    //    }
    //};
    template<typename T, typename Ref, typename Ptr>
    struct __list_iterator
    {
        typedef list_node<T> Node;
        typedef __list_iterator<T, Ref, Ptr> self;
        Node* _node;

        //构造函数
        __list_iterator(Node* x)
            : _node(x)
        {}

        self& operator++()
        {
            _node = _node->_next;
            return *this;
        }
        self operator++(int)
        {
            self tmp(*this);
            _node = _node->_next;
            return tmp;
        }
        self& operator--()
        {
            _node = _node->_prev;
            return *this;
        }
        self operator--(int)
        {
            self tmp(*this);
            _node = _node->_prev;
            return tmp;
        }
        Ref operator*()
        {
            return _node->_data;
        }
        Ptr operator->()
        {
            return &_node->_data;
        }
        bool operator!=(const self& s)
        {
            return _node != s._node;
        }
        bool operator==(const self& s)
        {
            return _node == s._node;
        }
    };

    //3.list容器
    template <typename T>
    class list
    {
        typedef list_node<T> Node;//节点结构体被重命名
    public:
        typedef __list_iterator<T, T&, T*> iterator;//迭代器结构体被重命名
        typedef __list_iterator<T, const T&, const T*> const_iterator;//迭代器结构体被重命名
        //typedef __list_const_iterator<T> const_iterator;//迭代器结构体被重命名
        void empty_init()//空初始化函数，由于后面有很多该操作，故专门写成一个函数
        {
            _head = new Node;
            _head->_next = _head;
            _head->_prev = _head;
            _head->_data = T();

            _size = 0;
        }
        //3.1.Member functions
        list()//初始化好头节点
        {
            empty_init();
        }
        list(list<T>& li)
        {
            empty_init();
            for (auto e : li)
            {
                push_back(e);
            }
        }
        list<T>& operator=(list<T> tmp)//拷贝构造一份
        {
            //1.传统写法
            //if (this != &lt)
            //{
            //    clear();
            //    for (auto e : lt)
            //    {
            //        push_back(e);
            //    }
            //}
            //return *this;
            //2.现代写法
            swap(tmp);
            return *this;
        }
        //3.2.Iterators
        iterator begin()
        {
            return _head->_next;
        }
        iterator end()//实际上是返回哨兵位节点
        {
            return _head;
        }
        const_iterator begin() const
        {
            return _head->_next;
        }
        const_iterator end() const//实际上是返回哨兵位节点
        {
            return _head;
        }
        //3.3.Capacity
        size_t size()
        {
            return _size;
        }
        //3.4.Element access

        //3.5.Modifiers
        iterator inster(iterator pos, const T& x)
        {
            //在pos前插入
            Node* newNode = new Node(x);

            Node* cur = pos._node;
            Node* prev = cur->_prev;

            prev->_next = newNode;
            newNode->_prev = prev;
            newNode->_next = cur;
            cur->_prev = newNode;

            _size++;
            return iterator(newNode);
        }
        iterator erase(iterator pos)
        {
            Node* cur = pos._node;
            Node* prev = cur->_prev;
            Node* next = cur->_next;

            delete cur;
            prev->_next = next;
            next->_prev = prev;
            _size--;

            return iterator(next);
        }
        void push_back(const T& x)
        {
            inster(end(), x);
        }
        void pop_back()
        {
            erase(--end());
        }
        void push_front(const T& x)
        {
            inster(begin(), x);
        }
        void pop_front()
        {
            erase(begin());
        }
        void clear()
        {
            iterator it = begin();
            while (it != end())
            {
                it = erase(it);
            }
        }
        //3.6.Operations

        //3.7.Observers

        //3.8.Non-member function overloads
        void swap(list<T>& li)
        {
            std::swap(_head, li._head);
            std::swap(_size, li._size);
        }
    private:
        Node* _head;
        size_t _size;//方便事先size()，避免效率底下
    };
    void test_1()
    {
        list<int> l1;
        l1.push_back(1);
        l1.push_back(2);
        l1.push_back(3);
        l1.push_back(4);
        l1.push_back(5);

        list<int>::iterator it = l1.begin();
        while (it != l1.end())
        {
            cout << *it << " ";
            (*it) += 50;
            ++it;
        }
        cout << endl;

        for (auto in : l1)
        {
            cout << in << " ";
        }
        cout << endl;
        //迭代器的使用体现了封装的优势
    }
    void test_2()
    {
        list<int> l1;
        l1.push_back(1);
        l1.push_back(2);
        l1.push_back(3);
        l1.push_front(1);
        l1.push_front(2);
        l1.push_front(3);
        for (auto in : l1)
        {
            cout << in << " ";
        }
        cout << endl;
        l1.pop_back();
        l1.pop_front();
        for (auto in : l1)
        {
            cout << in << " ";
        }
        cout << endl;
        l1.clear();
        for (auto in : l1)
        {
            cout << in << " ";
        }
    }
    void test_3()
    {
        list<double> l1;
        l1.push_back(1.2);
        l1.push_back(5.0);
        l1.push_back(1.3);
        l1.push_back(0.89);
        l1.push_back(-0.12);
        for (auto e : l1)
        {
            cout << e << " ";
        }
        cout << l1.size() << endl;

        list<double> l2(l1);
        for (auto e : l1)
        {
            cout << e << " ";
        }
        cout << l2.size() << endl;

        list<double> l3;
        l3 = l2;
        for (auto e : l3)
        {
            cout << e << " ";
        }
        cout << l3.size() << endl;
    }
    void test_4()
    {
        list<int> l1;
        l1.push_back(1);
        l1.push_back(2);
        l1.push_back(3);
        l1.push_back(4);
        l1.push_back(5);
        const list<int> l2 = l1;
        list<int>::const_iterator it = l2.begin();
        while (it != l2.end())
        {
            cout << *it << " ";
            it++;
        }
        cout << endl;
    }
    void test_5()
    {
        class A
        {
        public:
            int _a = 1;
        };
        list<A> li;
        A a1;
        A a2;
        A a3;
        li.push_back(a1);
        li.push_back(a2);
        li.push_back(a3);

        list<A>:: iterator it = li.begin();

        cout << (*it)._a << endl;

        cout << it->_a << endl;//it.operator->()->_a
    }

    template<typename T>
    void print_list(const list<T>& li)
    {
        typename list<T>::const_iterator it = li.begin();//这里必须加上typename，不然该函数在编译期间无法确认实例化（静态成员变量？类型？）
        //因此上述的关键字typename就是告诉编译器这是一个类型，等list<T>实例化了再去类里面取

        while (it != li.end())
        {
            cout << *it << " ";
            it++;
        }
        cout << endl;
    }
    template<typename Container>
    void print_container(const Container& con)//针对所有容器
    {
        typename Container::const_iterator it = con.begin();//这里必须加上typename，不然该函数在编译期间无法确认实例化（静态成员变量？类型？）
        //因此上述的关键字typename就是告诉编译器这是一个类型，等list<T>实例化了再去类里面取

        while (it != con.end())
        {
            cout << *it << " ";
            it++;
        }
        cout << endl;
    }
    void test_6()
    {
        list<int> l1;
        l1.push_back(1);
        l1.push_back(2);
        l1.push_back(3);
        print_list(l1);

        list<std::string> l2;
        l2.push_back("aaaa");
        l2.push_back("abbb");
        l2.push_back("cccc");
        l2.push_back("dddd");
        print_list(l2);

        std::vector<std::string> v;
        v.push_back("1111");
        v.push_back("2222");
        v.push_back("3333");
        v.push_back("4444");
        print_container(v);//这个函数的强大体现了泛型编程
    }

}

```

