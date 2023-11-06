# 1.编程题

## 1.1.[WY16 不要二](https://www.nowcoder.com/practice/1183548cd48446b38da501e58d5944eb?tpId=122&tqId=33662&ru=/exam/oj)

这题真的是简单题目？通过率`30%`左右...

但是解法确实很无聊，有两种方法可以提供思路：

1.   根据$(x_{1}-x_{2})^{2} + (y_{1} - y_{2})^{2} \neq 4$这一公式，结合坐标都是整数（因此有整数的封闭性这一数论知识），可推导出著只有$4 + 0、0 + 4、2 + 2、1 + 3、3 + 1$这几种情况，而可能的情况只有$4 + 0$和$0 + 4$两种。因此就会推导出$x_{1} = x_{2},y_{1}= y_{2} + 2$和$y_{1} = y_{2},x_{1}= x_{2} + 2$
2.   这个不就是一个圆方程么？画画图就可以总结啦！

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    int W, H;
    int count = 0;
    cin >> W >> H;

    vector<vector<int>> arr(W);
    int flag1 = 0;
    int flag2 = 0;
    int number = 1;

    for (auto& e : arr)
    {
        e.resize(H, 1);
    }

    for (int i = 0; i < W; i++)
    {
        for (int j = 0; j < H; j++)
        {
            if (arr[i][j] == 1)
            {
                count++;
                if (j + 2 < H)
                    arr[i][j + 2] = 0;
                if (i + 2 < W)
                    arr[i + 2][j] = 0;
            }
        }
    }
    cout << count;

    return 0;
}
```

## 1.2.[把字符串转换成整数](https://www.nowcoder.com/questionTerminal/1277c681251b4372bdef344468e4f26e)

这个比较简单，主要是一些特例的处理。

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;
class Solution
{
public:
    long long StrToInt(string str)
    {
        long long number = 0;   //返回的数
        int flag = 1;           //符号标识
        int index = 0;          //索引
        if (str[index] == '-')
        {
            flag = -1;
            index++;
        }
        if (str[index] == '+')
        {
            index++;
        }
        if (str[index] == '0')
        {
            return 0;
        }

        while (index != str.size())
        {
            if (str[index] <= '9' && str[index] >= '0')
            {
                number += (str[index] - '0');
                index++;
                number *= 10;
            }
            else
            {
                return 0;
            }
        }
        return flag * number / 10;
    }
};
```

类似的问题可以看看这一题，比上面难一些：[JZ67 把字符串转换成整数(atoi)](https://www.nowcoder.com/practice/d11471c3bf2d40f38b66bb12785df47f?tpId=13&tqId=11202&ru=/exam/oj)。

# 2.选择题

没有错误，暂时不写。