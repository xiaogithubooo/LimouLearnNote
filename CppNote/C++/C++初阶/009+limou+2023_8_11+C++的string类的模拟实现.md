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
