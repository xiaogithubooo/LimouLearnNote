# 1.编程题

## 1.1.[排序子序列](https://www.nowcoder.com/questionTerminal/2d3f6ddd82da445d804c95db22dcc471)

>   思路：注意在`if(){}`语句中嵌套`while(){}`的操作，这不是第一次遇到了。

```cpp
#include <iostream>
#include <vector>
using namespace std;
int main()
{
	//1.输入数据
	int n = 0;
	cin >> n;
	vector<int> arr;
	while (n--)
	{
		int number = 0;
		cin >> number;
		arr.push_back(number);
	}
	arr.push_back(0);//这个0是避免越界使用，不影响结果

	//2.处理子序列
	int count = 0;
	int i = 0;
	while (i < arr.size())
	{
		if (arr[i] < arr[i + 1])
		{
			while (i < arr.size() && (arr[i] <= arr[i + 1]))
			{
				i++;
			}
			count++;
			i++;
		}
		else if (arr[i] > arr[i + 1])
		{
			while (i < arr.size() && (arr[i] >= arr[i + 1]))
			{
				i++;
			}
			count++;
			i++;
		}
		else
		{
			i++;
		}
	}

	//3.输出数据
	cout << count << endl;
	return 0;
}
```

## 1.2.[倒置字符串](https://www.nowcoder.com/questionTerminal/8869d99cf1264e60a6d9eff4295e5bab)

>   思路：三段逆置法即可。

```cpp
#include <iostream>
#include <string>
using namespace std;
void _function(string& str, int x, int y)//abcd, x->a, y->'\0'
{
    y--;
    while (x < y)
    {
        swap(str[x++], str[y--]);
    }
}
int main()
{
    //1.获取句子
    string str;
    getline(cin, str);//有可能有输入" "、""、"."、"apple."

    //2.整个句子都逆置
    int begin = 0;
    int end = str.size() - 1;//例如"abcd ef ghi jklmn."的size=8，因此这里end为7
    while (begin < end)
    {
        swap(str[begin++], str[end--]);
    }

    //3.寻找空格，定位好每个单词，将每个单词都逆转
    int index = 0;
    int begin_i = 0;
    while (index < str.size())//0 < 8，不会越界
    {
        if (str[index] == ' ')
        {
            _function(str, begin_i, index);//传过去(0, 4)，注意这里是“框起一个单词”
            begin_i = index + 1;//5->e
        }
        index++;
    }
    _function(str, begin_i, index);
    cout << str;

    return 0;
}
```

# 2.选择题

## 2.1.题目一：数组存储和指针存储字符串

下面叙述错误的是什么？

```cpp
char acX[] = "abc"; 
char acY[] = { 'a','b','c' }; 
char *szX = "abc"; 
char *szY = "abc";
```

1.   `acX`与`acY`的内容可以修改 
2.   `szX`与`szY`指向同一个地址
3.   `acX`占用的内存空间比`acY`占用的大
4.   `szX`的内容修改后，`szY`的内容也会被更改

>   解释：
>
>   1.   数组存储的字符串的确都可以修改
>   2.   的确指向同一块地址
>   3.   是的，因为多了`\0`
>   4.   `szX`的内容指的是修改变量`szX`本身，这只是改变了这个指针的指向，不会影响`szY`变量的内容。
>
>   另外，如果是修改`szX`指向的内容，就要看具体的实现，有的编译器如果允许修改就会导致所有使用`szX`指向字符串的地方都会被修改，但是如果不是这么实现就不会影响，因此该行为是未定义的（这里在《C Primer plus》内的“字符串和字符函数”章节里有相关的描述）。

## 2.2.题目二：结构体对齐问题

在`32`位`cpu`上选择缺省对齐的情况下，有如下结构体定义： 

```cpp
struct A
{     
    unsigned a : 19;//32位空间，用去19bit，对齐数为4
    unsigned b : 11;//再用去11bit
    unsigned c : 4;//再申请32位空间，用去4bit，对齐数为4
    unsigned d : 29;//再申请32位空间，用去29bit，对齐数为4
    char index;//前面已经占用了12字节，这里再加上1字节，对齐数为1
};
int main()
{
    printf("%zd\n", sizeof(atruct A));//输出16
    return 0;
}
```

1.   输出`9`
2.   输出`12`
3.   输出`16`
4.   输出`20`

>   解释：已经写到代码里了，很奇怪，当时莫名其妙做错了，可能是不熟练的缘故

## 2.3.题目三：优先级问题

```cpp
int a[] = {1, 2, 3, 4}; 

int *b = a; 
*b += 2; 
*(b + 2) = 2; 
b++;

printf("%d,%d\n", *b, *(b + 2));
```

上述代码的输出结果是什么？数值的内容变成什么样了？

>   解释：答案是输出`2,4`，并且数组的内容被改变为`[3,2,2,4]`。虽然在实际工作中，使用括号没有问题，但是你架不住还是会有面试题考这种奇葩的东西，因此还是建议记下优先级和结合性比较好。
>
>   ![image-20231025172636551](./assets/image-20231025172636551.png)