# 1.编程题

## 1.1.[组队竞赛](https://www.nowcoder.com/questionTerminal/248ccf8b479c49a98790db17251e39bb)

1.   首先得到数据，将所有的`3*n`个数据进行排序
2.   此时我们取第一个元素`nums[begin]`和最后一个元素`nums[end]`，就可以保证取得最小数和最大数
3.   将上述两个数和`nums[end-1]`这个数组成一个队伍，这样就可以保证这个队伍的平均值的最大的
4.   依次类推，`begin++, end = end - 2`循环操作即可

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main()
{
    //1.输入数据
    int n;
    cin >> n;
    vector<int> nums;
    for (int i = 0; i < 3 * n; i++)
    {
        int number = 0;
        cin >> number;
        nums.push_back(number);
    }

    //2.排序数据
    sort(nums.begin(), nums.end());

    //3.开始组队
    long long sum = 0;
    int begin = 0;
    int end = nums.size() - 1;
    int mid = end - 1;
    while (begin < mid)
    {
        sum += nums[mid];
        begin++;
        end -= 2;
        mid = end - 1;
    }

    //4.输出数据
    cout << sum;
    return 0;
}
```

这里需要注意一下，在做在线编程题目的时候，很多人会忽略数据的范围，比如这道题目就有数据范围$1 ≤ a_i ≤ 10^9$，这一点就会导致坑杀很多人。导致思路正确，但是输出越界的奇怪错误，这道题目如果`sum`的类型不是`long`就会导致越界。

还有些细节，您会不会写`sort()`的头文件`algorithm`，这个头文件就是“算法”的意思。

并且还有一点，牛客有些编程题目是重复输入，有的时候也会坑人。

## 1.2.[删除公共字符]()

### 1.2.1.解法一：利用辅助空间

1.   创建一个辅助空间`newStr`
2.   遍历`str`的每一个字符，查看是否为要删除的字符（即`deleteStr`中的字符）。不是就尾插到`newStr`，是就不尾插，遍历下一个字符

```cpp
//思路1
#include <cstdio>
#include <iostream>
#include <string>
using namespace std;

bool IsOk(const char& ch, const string& deleteStr)//如果不符合删除条件就返回真
{
    for (auto it : deleteStr)
    {
        if (it == ch)
        {
            return false;
        }
    }
    return true;
}
int main()
{
    string str;
    getline(cin, str);

    string deleteStr;
    getline(cin, deleteStr);

    string newStr;

    int index = 0;
    while (index != str.size())
    {
        if (IsOk(str[index], deleteStr))//如果不符合删除条件就返回真
        {
            newStr += str[index];
        }
        index++;
    }

    cout << newStr;
    return 0;
}
```

### 1.2.2.解法2：利用哈希映射

1.   创建一个哈希表`int count[256]`，初始化每一个元素为`0`
2.   遍历`deleteStr`字符串中每一个`ch`，在`count[256]`中`ch`下标处的位置`count[ch]++`
3.   遍历`str`字符串中的每一个字符`ch`，查看在哈希表中`ch`的对于的`count[ch]`是否为`0`，为`0`则输出该字符，不为`0`就不输出该字符

```cpp
//思路2
#include <iostream>
#include <string>
#include <vector>
using namespace std;
int main()
{
    //1.字符串数组
    string str;
    getline(cin, str);
    string deleteStr;
    getline(cin, deleteStr);

    //2.哈希表
    vector<int> arr(256);
    arr.resize(0);

    //3.映射deleteStr
    for(auto ch : deleteStr)
    {
        arr[ch]++;
    }

    //4.输出不被输出的字符
    for (auto ch : str)
    {
        if (arr[ch] == 0)
        {
            cout << ch;
        }
    }
  
    return 0;
}
```

# 2.选择题

无错，暂时不理会。

