# 1.编程题

## 1.1.[WY22 Fibonacci数列](https://www.nowcoder.com/practice/18ecd0ecf5ef4fe9ba3f17f8d00d2d66?tpId=122&tqId=33668&ru=/exam/oj)

这题也很简单，简单做一下把。

```cpp
 #include <iostream>
 #include <vector>
 int Fibonacci(const long& number, std::vector<int>& ret)
 {
    ret.push_back(0);
    ret.push_back(1);
    while (ret[ret.size() - 1] < number)
    {
        ret.push_back(ret[ret.size() - 2] + ret[ret.size() - 1]);
    }
    int num1 = ret[ret.size() - 1] - number;
    int num2 = number - ret[ret.size() - 2];
    return num1 < num2 ? num1 : num2;
 }
 
int main()
 {
    //1.取得数据
    long number = 0;
    std::cin >> number;
 
    //2.根据数据生成Fibonacci表
    std::vector<int> ret;
    int k = Fibonacci(number, ret);
 
    //3.输出k
    std::cout << k;
    return 0;
 }
```

## 1.2.[合法括号序列判断](https://www.nowcoder.com/questionTerminal/d8acfa0619814b2d98f12c071aef20d4)

这题也还行，简单做一下吧，主要是使用栈。

```cpp
class Parenthesis
{
public:
    bool chkParenthesis(std::string A, int n)
    {
        int i = 0;
        std::stack<char> s;
        while (i < (int)A.size())
        {
            if (A[i] == '(')
            {
                s.push(A[i]);
            }
            else if (A[i] == ')')
            {
                if (!s.empty())
                {
                    s.pop();
                }
                else//s.empty() == 0
                {
                    return false;
                }
            }
            else
            {
                return false;
            }
            i++;
        }
        return true;
    }
};
```

# 2.选择题

## 2.1.必须构造的成员变量

```cpp
class A { 
/*...*/
private: 
     int a; 
public: 
     const int b;//必须在构造期间被初始化
     float*& c;//必须在构造期间被初始化
     static const char* d;//类外初始化即可
     static double* e;//类外初始化即可
};
```

