# 1.deque双端队列

## 1.1.deque简介

### 1.1.1.deque名称来源

`deque`虽然是叫“双端队列（双端是指头插和尾插）”，但是实际上是`vector+list`，既支持`vector`的功能也支持`list`的功能。

顺序表的优点和缺点都是空间连续，链表的优点和缺点是空间不连续。

使用一个“中控数组”，存储其他多端数组，并且从中控数组的中间开始存放数组指针。

头插就在中间位置的前一个位置放置一个指向新数组的指针，尾插就在中将位置的后一个位置放置一个指向新数组的指针。

### 1.1.2.deque优劣所在

`deque`的优点：

1. 相比`vector`扩容代价低

2. 头插头删，尾插尾删效率高

3. 支持随机访问，通过每个固定数组，直接跳跃中控数组的指针就可以

`deque`的缺点：

1. 中间插入删除比较困难（选择非固定数组可以解决这个问题，但是随机访问就会变得困难，因此`STL`还是使用了固定数组，牺牲了中间插入删除）

2. 还有一个缺点，就是太过平衡了，效率处在`vector`和`list`直接，过于平衡了，没把`vector`和`list`的优点发展到极致

有些大量头尾插入和删除就很适合用`deque`，因此更适合做`vector`和`list`的底层适配器。

> 注意：在有些时候，比如使用`vector`排序、使用`list`排序亦或是使用`deque`排序，很多情况下还不如将数据同一拷贝到`vector`排序然后拷贝到其他容器中，这一步骤的消耗没有我们想象中的那么大。

## 1.2.deque接口

这是`deque`的一份说明文档：[deque - C++ Reference (cplusplus.com)](https://legacy.cplusplus.com/reference/deque/deque/)，或者您可以看我省略大部分信息后的简易版本。

```c++
template < class T, class Alloc = allocator<T> > class deque
{

};
```

## 1.3.deque实现

有关`deque`的实现我已经写好在另外一篇文章《[deque的模拟实现](./)》，其中您可以留意一下`deque`的迭代器，这应该是目前我们模拟实现`STL`最为复杂的迭代器。

----

这里写上需要转移的部分：

1. `cur`当前指向数据的位置

2. `first`和`last`指向`buff`数组的开始和结束

3. `node`反向指向中控数组

`*it`就是`*cur`

`++it`就是`++cur`，但是如果超出`buff`数组就会根据`node`来返回中控数组指向下一个`buff`的地址。

----

# 2.priority_queue优先级队列

## 2.1.priority_queue简介

默认的容器是`vector`，也可以是`deque`，即要求可以随机访问的迭代器。这个队列不符合先进先出，而是让符合优先级高的先出，优先级低的后出。

其底层默认是大堆，认为大的数优先级高。

如果需要变成小堆就可以使用仿函数（也叫“函数对象”，仿函数不是回调函数，但是原理有点像，是一种函数对象，不是函数）

```c++
#include <functional>
#include <queue>
priority_queue<int> pq1;//默认大堆
priority_queue<int, vector<int>, greater<int>> pq2;//小堆，实际上这里的接口设计的有点问题，没有写好
```

## 2.2.priority_queue接口

[priority_queue - C++ Reference (cplusplus.com)](https://legacy.cplusplus.com/reference/queue/priority_queue/)

## 2.3.priority_queue实现

```c+
priority_queue
```
