# 1.栈（stack）

[栈接口](https://legacy.cplusplus.com/reference/stack/stack/)查询手册。

# 2.队列（queue）

[队列](https://legacy.cplusplus.com/reference/queue/queue/)接口查询手册。

> 和我们之前讲的`vector`、`list`的容器不一样，栈和队列内部有`适配器`。
> 
> 1. 前者`vector`、`list`都是自己直接管理数据
> 
> 2. 而后者`stack`和`queue`是在其他容器的基础上封装转化出需要的数据。

# 3.相关题目练习

## 3.1.[栈的压入、弹出序列](https://www.nowcoder.com/practice/d77d11405cc7470d82554cb392585106?tpId=13&tqId=11174&ru=/exam/oj)

```c++
class Solution
{
public:
    /*
     * 代码中的类名、方法名、参数名已经指定，请勿修改，直接返回方法规定的值即可
     *
     *
     * @param pushV int整型vector
     * @param popV int整型vector
     * @return bool布尔型
     */
    bool IsPopOrder(vector<int>& pushV, vector<int>& popV)
    {
        //0.规定pushV为入栈序列，popV为出栈序列
        //1.入栈序列先入栈s
        //2.s的栈顶数据和出栈序列比较
            //a.如果匹配，出栈序列++，出s的栈顶元素，继续比较，直到栈为空，或者不匹配，回到1步骤
            //b.如果不匹配，回到步骤1
        //3.如果一直走到出栈序列结束就返回true
        stack<int> s;
        int pushVIndex = 0, popVIndex = 0;
        s.push(pushV[pushVIndex++]);
        while (popVIndex < popV.size())
        {
            if (s.empty() || s.top() != popV[popVIndex])
            {
                if (pushVIndex > pushV.size())
                {
                    return false;
                }
                s.push(pushV[pushVIndex]);
                pushVIndex++;
            }
            else if (s.top() == popV[popVIndex])
            {
                popVIndex++;
                s.pop();
            }
        }
        return true;
    }
};
```

## 3.2.[最小栈](https://leetcode.cn/problems/min-stack/)

```c++
class MinStack
{
public:
    MinStack()
    {}
    void push(int val)
    {
        s1.push(val);
        if (!s2.empty())
        {
            if (val <= s2.top())
            {
                s2.push(val);
            }
        }
        else//空
        {
            s2.push(val);
        }
    }
    void pop()
    {
        if (s1.empty())
        {
            return;
        }
        if (s1.top() == s2.top())
        {
            s1.pop();
            s2.pop();
        }
        else
        {
            s1.pop();
        }
    }
    int top()
    {
        return s1.top();
    }
    int getMin()
    {
        return s2.top();
    }
    stack<int> s1;
    stack<int> s2;
};
```
