# 1.编程题

字符串中找出连续最长的数字串

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