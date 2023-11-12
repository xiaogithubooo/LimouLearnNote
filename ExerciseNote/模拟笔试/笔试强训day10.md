# 1.编程题

## 1.1.[井字棋](https://www.nowcoder.com/questionTerminal/e1bb714eb9924188a0d5a6df2216a3d1)

## 1.2.[密码强度等级](https://www.nowcoder.com/practice/52d382c2a7164767bca2064c1c9d5361?tpId=37&tqId=21310&ru=/exam/oj)

# 2.选择题

## 2.1.题目一：重载依据

不能作为重载函数的调用的依据是： 

1.   参数个数
2.   参数类型
3.   函数类型
4.   函数名称

>    解释：选`3`，啥都不用说了，我好蠢...

## 2.2.题目二：

以下程序的输出结果是什么？

```cpp
#include <iostream>  
using namespace std;  
int main(void)  
{  
    const int a = 10;  
    int* p = (int*)(&a);  
    *p = 20;  
    cout << "a = " << a << ", *p = "<< *p << endl;  
    return 0;  
}
```

