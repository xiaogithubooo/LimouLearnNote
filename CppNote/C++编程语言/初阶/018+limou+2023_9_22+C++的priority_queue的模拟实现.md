```c++
#pragma once
#include <vector>
#include <deque>
#include <iostream>
namespace limou
{
    //1.仿函数（也可以自己在main()内部实现，符合自己的要求）
    template<typename T>
    struct less//仿函数（传递仿函数类型就可以做到：给模板传递类型后，只需要在内部创建对象（甚至匿名对象）就可以携带很多的成员函数，这比单纯使用函数指针来说厉害多了）
    {
        bool operator()(const T& x, const T& y)
        {
            return x < y;//另外这里也可以使用对象比较（只要有重载）
        }
    };
    template<typename T>
    struct greater//仿函数
    {
        bool operator()(const T& x, const T& y)
        {
            return x > y;
        }
    };
    //2.类模板
    template<typename T, typename Container = std::vector<T>, typename Comapre = less<T>>//默认大堆
    class PriorityQueue
    {
    private:
        Comapre com;
        void adjust_up(int child_i)
        {
            int parent_i = (child_i - 1) / 2;
            while (child_i > 0)
            {
                if (com(_con[parent_i], _con[child_i]))//如果是匿名对象就可以写成Compre()(_con[parent_i], _con[child_i])，虽然奇怪但是也有很多人使用
                {
                    std::swap(_con[parent_i], _con[child_i]);
                    child_i = parent_i;
                    parent_i = (child_i - 1) / 2;
                }
                else
                {
                    break;
                }
            }
        }
        void adjust_down(size_t parent_i)
        {
            size_t child_i = parent_i * 2 + 1;//直接先求出左孩子，注意右孩子不存在的情况
            while (child_i < _con.size())
            {
                if (child_i + 1 < _con.size() && com(_con[child_i], _con[child_i + 1]))//选出最大孩子的下标
                {
                    child_i++;
                }
                if (com(_con[parent_i], _con[child_i]))
                {
                    std::swap(_con[child_i], _con[parent_i]);
                    parent_i = child_i;
                    child_i = parent_i * 2 + 1;
                }
                else
                {
                    break;
                }
            }
        }
    public:
        void push(const T& val)
        {
            _con.push_back(val);//插入数组的末尾
            adjust_up(_con.size() - 1);//使用向上调整算法
        }
        void pop(void)
        {
            std::swap(_con[0], _con[_con.size() - 1]);//交换数组首位元素
            _con.pop_back();//删除最后一个元素
            adjust_down(0);//使用向下调整算法
        }
        const T& top()
        {
            return _con[0];
        }
        size_t size()
        {
            return _con.size();
        }
        bool empty()
        {
            return _con.empty();
        }
    private:
        Container _con;
    };
}
```


