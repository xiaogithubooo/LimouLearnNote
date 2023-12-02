`C++`包装类是为了统一解决一些调用问题，在`C++`中：“函数指针回调、仿函数、`lambda`表达式”都属于可调用对象，他们都可以达到函数函数的行为，达到回调函数和事件处理的功能。

但他们各有优缺点：

1.   函数指针：符合`C/C++`一贯使用的语法，无需增加过多的语法特性，但是指针写起来过于恶心
2.   仿函数：很好用，但是用之前需要创建仿函数类，重载运算符`()`才可以使用，但是命名不好的时候不容易看出仿函数的意义，有的时为了一个小函数创建出一个类过于大刀阔斧了
3.   `lambda`表达式：书写方便，意义一眼明了，写短小的函数时非常方便，接受参数什么的很方便，但是是一个匿名函数，不容易精确书写出函数类型，甚至同一个书写方式、运作逻辑的`lambda`表达式在不同环境下都是不同函数类型的

因此就会产生一些问题：

1.   上面这`3`种方式实际上都有同一个缺陷，函数的类型都很难写，而有些时候我们需要明确知道函数的类型（例如：在使用`map`这些类模板时，需要调整对`key`的认定）
2.   明明是类似的行为，为什么不可以统一管理呢？
3.   我们几乎很难确定一个对象`ret = func(x)`是函数指针？还是仿函数？亦或者`lambda`表达式？

因此`C++`新增加了`function{};`类，这是一个很特殊的类模板。

# 1.function

实际上`function{}`包装类是一个适配器，其使用语法和以前的语法有些不一样，甚至可以说怪异的地方。

```cpp
#include<functional>
template<class Ret, class... Args> 
class function<Ret(Args...)>
{/*...*/};
//Ret 是函数的返回值类型
//Args 是函数参数列表的类型
```

因此我们可以做出一些统一的封装玩法，这就让三者可以统一起来。

```cpp
#include <iostream>
#include <string>
#include <map>
#include <functional>
using namespace std;

void swap_function(int& r1, int& r2)
{
	int tmp = r1;
	r1 = r2;
	r2 = tmp;
}

struct swap_struct
{
	void operator()(int& r1, int& r2)
	{
		int tmp = r1;
		r1 = r2;
		r2 = tmp;
	}
};

int main()
{
	int x = 1, y = 10;
	cout << "x = " << x << ", y = " << y << '\n';

	auto swap_lambda = [](int& r1, int& r2)
		{
			int tmp = r1;
			r1 = r2;
			r2 = tmp;
		};

	function<void(int&, int&)> func_1 = swap_function;
	func_1(x, y);
	cout << "x = " << x << ", y = " << y << '\n';

	function<void(int&, int&)> func_2 = swap_struct();
	func_2(x, y);
	cout << "x = " << x << ", y = " << y << '\n';

	function<void(int&, int&)> func_3 = swap_lambda;
	func_3(x, y);
	cout << "x = " << x << ", y = " << y << '\n';

	map<string, function<void(int&, int&)>> swap = {
		{ "函数指针", swap_function },
		{ "仿函数", swap_struct() },
		{ "lambda", swap_lambda }
	};

	swap["函数指针"](x, y);
	cout << "x = " << x << ", y = " << y << '\n';

	swap["仿函数"](x, y);
	cout << "x = " << x << ", y = " << y << '\n';

	swap["lambda"](x, y);
	cout << "x = " << x << ", y = " << y << '\n';

	return 0;
}
```

当然，达到统一包装的标准是参数类型必须相同，但是我们也有办法解决这个问题，我们有机会再提及。

另外，我们之前做过一道关于逆波兰表达式的题目，我们可以优化一下这道题目的代码。

```cpp
//没优化前
class Solution 
{
public:
	int evalRPN(vector<string>& tokens) 
	{
		stack<int> st;
		for (auto &str : tokens)
		{
			if (str == "+" || str == "-" || str == "*" || str == "/")
			{
				int right = st.top();
				st.pop();
				int left = st.top();
				st.pop();
				switch (str[0])
				{
					case '+':
						st.push(left + right);
						break;					
					case '-':
						st.push(left - right);
						break;					
					case '*':
						st.push(left * right);
						break;					
					case '/':
						st.push(left / right);
						break;
				}
			}
			else
			{
				st.push(stoi(str));
			}
		}
		return st.top();
	}
};
```

```cpp
//优化后
class Solution 
{
    public:
    int evalRPN(vector<string>& tokens) 
    {
        stack<int> st;
        //[capture-list](parameters)mutable->return-type{statement}
        map<string, function<int(int, int)>> func = {
            {"+", [](int x, int y)->int{return x + y;}}, 
            {"-", [](int x, int y)->int{return x - y;}}, 
            {"*", [](int x, int y)->int{return x * y;}}, 
            {"/", [](int x, int y)->int{return x / y;}}
        };
        for (auto &str : tokens)
        {
            if (func.count(str))//判断是否为操作符
            {
                int right = st.top();
                st.pop();
                int left = st.top();
                st.pop();
                st.push(func[str](left, right));
            }
            else
            {
                st.push(stoi(str));
            }
        }
        return st.top();
    }
};
```

这样的代码可拓展性更好，更加简洁明了。

另外，我们还需要了解一下成员函数的包装，这里有些特殊的处理。

```cpp
#include <iostream>
#include <string>
#include <map>
#include <functional>
using namespace std;

class Data
{
public:
	void Function(int x, double y)
	{
		cout << _data << "-" << x << "-" << y << '\n';
	}
private:
	int _data = 10;
};
int main()
{
	Data d;

	function<void(Data*, int, double)> f1 = &Data::Function; //对于非静态成员函数必须显示取地址
	f1(&d, 1, 3.1);
	//f1(&Data(), 1, 3.1);

	function<void(Data&, int, double)> f2 = &Data::Function;
	f2(d, 6, 3.8);
	//f2(Data(), 6, 3.8);

	function<void(Data, int, double)> f3 = &Data::Function;
	f3(d, 5, 1.5);
	f3(Data(), 5, 1.5);

	//function<void(const Data*, int, double)> f4 = &Data::Function;

	//function<void(const Data&, int, double)> f5 = &Data::Function;

	function<void(const Data, int, double)> f6 = &Data::Function;
	f6(d, 6, 3.14);
	f6(Data(), 6, 3.14);
	
	return 0;
}
```

>   补充：`functiuon{};`奇特的语法都是`C++`不曾有的，很多在解释语法的解释器上就做了修改，因此我们只需要学会如何使用即可，其内部底层原理我们不必过于探究。

# 2.bind

但是上面成员函数的包装中，每次都需要传递一个`Data`的对象、指针或引用，但这样太麻烦了，我们可以使用`bind()`，这个函数模板也在头文件`functional`内。

可以在用某个函数之前绑定某几个参数，然后让控制用户的参数传入。

```cpp
#include <iostream>
#include <string>
#include <map>
#include <functional>
using namespace std;

class Data
{
public: 
	void Print(float x, double y)
	{
		cout << _data << "-" << x << "-" << y << '\n';
	}
private:
	int _data = 10;
};

int Add(int x, int y, int z)
{
	return x + y + z;
}
int main()
{
	//1.普通函数的绑定
	function<int(int, int, int)> Add_1 = &Add;
	cout << Add_1(1, 2, 3) << '\n';

	function<int(int)> Add_2 = bind(&Add, 1, 2, placeholders::_1);
	cout << Add_2(3) << '\n';

	//2.成员函数的绑定
	Data d;
	function<void(Data*, float, double)> Print_1 = &Data::Print;
	Print_1(&d, 2, 3.14);

	function<void(float, double)> Print_2 = bind(&Data::Print, Data(), placeholders::_2, placeholders::_1);
	Print_2(2, 3.14);
	
	return 0;
}
```

注意这里的`_1`、`_2`是根据用户输入的参数来决定的，而不是根据原本的函数参数。

这个`bind()`还是很有用的，可以让函数的调用由使用者来控制缺省参数，而不是开发者。在调用一些繁琐的系统调用时，这个函数模板就会很有效。

因为有太多系统调用需要传递各种参数了，这些参数又不能直接设置为缺省参数，但是我们又经常需要传递，因此就只能一个一个传，但是有了这个函数模板就可以简化函数调用了。
