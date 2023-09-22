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


