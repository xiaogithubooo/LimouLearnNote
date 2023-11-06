# 1.编程题

## 1.1.[进制转换](https://www.nowcoder.com/practice/ac61207721a34b74b06597fe6eb67c52?tpId=182&tqId=34617&ru=/exam/oj)

这题很简单，如果潦草写的话就是：

```cpp
#include <iostream>
#include <string>
#include <algorithm>
#include <cmath>

using namespace std;

//1.设置数码
const char arr[16] = { '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F' };

int main()
{
	//2.输入数据
	int M = 0;
	int N = 0;
	cin >> M >> N;//输入数字和数制
	string number;//最终结果存储的逆序字符串
	int flag = 1;

	//3.提前处理负数和零的情况
	if (M < 0)
	{
		flag = -1;
		M = abs(M);
	}
	else if (M == 0)
	{
		number = "0";
	}

	//4.进制转换
	while (M)
	{
		number += arr[M % N];//映射插入字符
		M /= N;
	}

	//5.输出数据
	if (flag == -1) number += "-";
	reverse(number.begin(), number.end());
	cout << number;
    return 0;
}
```

为什么利用这种方法可以转化为任意进制呢？这里的数学原理以后补充。

## 1.2.[计算糖果](https://www.nowcoder.com/practice/02d8d42b197646a5bbd0a98785bb3a34?tpId=122&tqId=33679&ru=/exam/oj)

这一题比较坑的情况就是会出现无解的情况，需要在最后代入判断正误即可。

```cpp
#include <iostream>
using namespace std;

int main()
{
	//1.输入数据
	int AsB = 0, BsC = 0, AaB = 0, BaC = 0;
	cin >> AsB >> BsC >> AaB >> BaC;

	//2.根据数据得出结果
	int A = (AsB + AaB) / 2;
	int B = (BsC + BaC) / 2;
	int C = (BaC - BsC) / 2;

	//3.判断正确性
	if (A - B == AsB && B - C == BsC && A + B == AaB && B + C == BaC)
	{
		cout << A << " " << B << " " << C;
	}
	else
	{
		cout << "No";
	}

	return 0;
}
```

# 2.选择题

## 2.1.题目一：双数组地址确定另外一个地址

二维数组`X`按行顺序存储，其中每个元素占`1`个存储单元（指一个字节）。

1.   `X[4][4]`的存储地址为`Oxf8b82140`
2.   `X[9][9]`的存储地址为`Oxf8b8221c`

则`X[7][7]`的存储地址为多少？

>   解释：这题还怪恶心的，遇到很多次了。
>
>   怎么解决呢？
>
>   1.   假设数组的起始位置为`start`，总共有`M`行和`N`列
>
>   2.   可得`&x[4][4] = start + 4*N + 4 = Oxf8b82140`
>
>        可得`&x[9][9] = start + 9*N + 9 = Oxf8b8221c`
>
>        可得`&x[7][7] = start + 7*N + 7 = ?`
>
>   3.   因此解一元方程组即可
>
>   当然这里直接解方程可能有点困难（因为混杂了十进制和十六进制，还有十六进制的除法），还有没有其他方法呢？
>
>   1.   将第一个元素和第二个元素对齐：`&x[4][9] = &x[4][4] + 5 = Oxf8b82145`
>   2.   对齐后得出一行的元素个数：指针差值为元素个数，则`&x[9][9] - &x[4][9] = 5*N`，也就是`5`行内每一行的元素个数位为`43`
>   3.   用第一个元素结合求出的行个数求得结果：`&x[4][9] + 3*43 - 2 = Oxf8b82145 + 127 = 0xf8b821c4`

## 2.2.题目二：与运算操作

求函数返回值，输入`x=9999`，求输出结果。

```cpp
int func(int x)
{
	int count = 0;
	while (x)
	{
		count++;
		x = x & (x - 1);//与运算 
	}
	return count;
}
```

>   解释：根据比特位得观察，一个数有多少个比特位是`1`，该程序就会运行多少次。因此对于本题`(99)10=(0010 0111 0000 1111)2`中，结果输出`8`。另外这里`9999`数字比较大，使用`1248`法得数会有点难，干脆直接除`2`取余。
