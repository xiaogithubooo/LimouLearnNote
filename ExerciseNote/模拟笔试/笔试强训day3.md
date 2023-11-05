# 1.编程题

## 1.1.[字符串中找出连续最长的数字串](https://www.nowcoder.com/practice/bd891093881d4ddf9e56e7cc8416562d?tpId=182&tqId=34785&ru=/exam/oj)

下面使用一种类似擂台竞争的算法：

```cpp
#include <iostream>
#include <string>
#include <cctype>
using namespace std;
int main()
{
	//1.输入数据
	string str;
	getline(cin, str);

	//2.起始下标
	int cur = 0;

	//3.竞争字符串
	string str1;
	string str2;

	for (auto it : str)
	{
		if (isdigit(it))//是数字字符
		{
			str1.push_back(it);
		}
		else
		{
			if (str1.size() > str2.size())
			{
				str2 = str1;
			}
			str1.resize(0);
		}
	}
	if (str1.size() > str2.size())
	{
		str2 = str1;
	}

	cout << str2;
	return 0;
}
```

这里需要注意函数`isdigit()`的使用。

## 1.2.[数组中出现次数超过一半的数字](https://www.nowcoder.com/practice/e8a1b01a2df14cb2b228b30ee6a92163?tpId=13&tqId=11181&ru=/exam/oj)

### 1.2.1.哈希表

```cpp
class Solution
{
public:
    /**
     * 代码中的类名、方法名、参数名已经指定，请勿修改，直接返回方法规定的值即可
     *
     *
     * @param numbers int整型vector
     * @return int整型
     */
    int MoreThanHalfNum_Solution(vector<int>& numbers)
    {
        int size2 = (int)numbers.size() / 2;
        int max = numbers[0];
        int min = numbers[0];
        for (int i = 0; i < numbers.size(); i++)
        {
            if (max < numbers[i])
            {
                max = numbers[i];
            }
            if (min > numbers[i])
            {
                min = numbers[i];
            }
        }//找到max和min

        int range = max - min + 1;

        int* countArr = new int[range];
        memset(countArr, 0, sizeof(int) * range);
        for (int i = 0; i < numbers.size(); i++)
        {
            countArr[numbers[i] - min]++;
        }

        //遍历找到符合要求的数
        for (int i = 0; i < range; i++)
        {
            if(countArr[i] > size2)
            {
                return i + min;
            }
        }
        delete[] countArr;
        return -1;
    }
};
```

不过这种解法就不符合题意了（空间复杂度）。

### 1.2.2.摩尔投票算法

要找到一个数组中的众数（出现次数最多并且次数超过数组长度一半的元素，这里的众数不是数学意义上的众数），可以使用摩尔投票算法来实现高效的解决方案。摩尔投票算法是一种线性时间复杂度的算法，其基本思想是将数组中的两个不同元素进行抵消，最终剩下的元素就是众数。

以下是使用摩尔投票算法求众数的步骤：

1.  初始化两个变量：`candidate`和`count`，`candidate`用于保存当前的候选众数，`count`用于记录当前候选众数的出现次数，初始值分别为数组的第一个元素和`1`。
2.  遍历数组，对于每个元素进行如下操作：
    *   如果当前元素与候选众数相同，则将`count`加`1`。
    *   如果当前元素与候选众数不同，则将`count`减`1`。
    *   如果`count`减到`0`，表示当前的候选众数已经被抵消完，将当前元素设为新的候选众数，并将`count`重新设为`1`。
3.  最终剩下的候选众数就是数组中的众数。

但是有一种情况可能会出现问题，如果众数是唯一的，那么一个序列里有可能会出现不存在众数的情况，例如：`{1,2,2,3,4,4,5}`就没有众数，因此最后求出结果的时候，最好是检验该数是否真的为正数（本题可以不用这么写，因为题目保证有解）。

```cpp
class Solution
{
public:
    int MoreThanHalfNum_Solution(vector<int> numbers)
    {
        if (!numbers.size()) return -1;

        int number = numbers[0];//侯选数
        int count = 1;//投票数

        int i = 1;
        while (i < numbers.size())
        {
            if (number == numbers[i])
            {
                count++;//投票
            }
            else
            {
                count--;//抵消
            }
            i++;
            if (count == 0)
            {
                number = numbers[i];//更换候选人
                count = 1;//重置票数
                i++;
            }
        }
        return number;
    }
};
```

或者这么写（不过推荐前一种写法）：

```cpp
#include <iostream>
#include <vector>
using namespace std;
class Solution
{
public:
    int MoreThanHalfNum_Solution(vector<int> numbers)
    {
        int number = numbers[0];
        int count = 1;

        for (int i = 1; i < numbers.size(); i++)
        {
            if (count != 0)
            {
                if (numbers[i] != number)
                {
                    count--;
                }
                else
                {
                    count++;
                }
            }
            else
            {
                number = numbers[i];
                count = 1;
            }
        }
        return number;
    }
};
```

# 2.选择题

## 2.1.题目一：开关语句的break

以下程序的输出结果是什么？

```cpp
#include <iostream.h> 
int main() 
{ 
    int x = 3, y = 3; 
    switch(x % 2) 
    {
    case 1: 
        switch (y) 
        { 
        case 0: 
            cout << "first"; 
        case 1: 
            cout << "second"; 
            break; 
        default: 
            cout << "hello"; 
        } 
    case 2: 
        cout << "third"; 
    } 
    return 0; 
}
```

主要是忽略了`break`语句。

