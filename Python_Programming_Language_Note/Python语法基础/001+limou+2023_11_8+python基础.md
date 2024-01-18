# 1.数据类型

`python` 中有三种基本数据类型，分别是整数、浮点数、字符串，不同的数据类型可以创建不同的变量。

## 1.1.整数

整数值，待补充...

## 1.2.浮点数

浮点小数，待补充...

## 1.3.字符串

`python` 的字符操作非常方便和多样，是值得重点学习的部分，`python` 的字符冲一般使用 `''`、`""` 框起。

## 1.5.变量作用域

使用关键字 `global` 可以告知 `python` 这是一个全局变量（注意不是创建），后面提及函数的时候还会再提及这个关键字的作用。

# 2.数据转化

使用 `str()`、`int()`、`float()` 来进行转化，需要注意转化的合理性。

# 3.操作符

| 操作符 | 操作说明 | 例子                                                  |
| ------ | -------- | ----------------------------------------------------- |
| `+`    | 加法     | `2+3 = 5`                                             |
| `-`    | 减法     | `2-3 = -1`                                            |
| `*`    | 乘法     | `2*3 = 6`                                             |
| `/`    | 除法     | `18/4 = 2`                                            |
| `**`   | 指数     | `2**3 = 8`                                            |
| `%`    | 取模     | `22%8 = 6`                                            |
| `==`   | 判等     | `42 == 42.0`（注意这两者是相等的，返回结果为 `True`） |

# 4.注释

`python` 的注释只要有三种：一种行级注释、两种块级注释

```python
# 三种注释
# 行级注释，可以注释一行

'''
块级
注释 1
'''

"""
块
级
注
释
2
"""
```

# 5.控制流

和 `Java` 类似，`pyhton` 的控制流依靠布尔语句来控制，并且也有常见的比较操作符：`>`、`<`、`==`、`!=`、`>=`、`<=`。同时，`python` 还有两个和 `C` 语言的 `&&`、`||`、`!` 类似的布尔操作符：`and`、`or`、`not`。

结合布尔值、比较操作符、布尔操作符可以创建出多种布尔表达式，这些表达式通常用于控制程序的执行流...

## 5.1.条件语句

```python
# 使用 if 语句
if 条件1(布尔语句1):
    # 某些需要被执行的代码 1
elif 条件2(布尔语句2):
    # 某些需要被执行的代码 2
else:
    # 某些需要被执行的代码 3
```

## 5.2.循环语句

```python
# 使用 while 语句
while 条件(布尔语句):
    # 某些需要被执行的代码块
```

```python
# 使用 for 语句
for 循环变量 in range(start, end, step): # [start, end)
    # 某些需要被执行的代码块    
```

>   补充：循环语句都可以使用 `break`、`continue` 语句。

# 6.函数

## 6.1.导入模块和包

使用函数之前，先学会使用导入模块，这和 `C/C++` 导入头文件、`Java` 导入包是类似的，尽管某些细节有些许不同...

`Python` 中的任何 `.py` 文件都可以称为一个 **模块**（`module`），模块可以用来组织函数和类等，在其他程序中引入这些模块中的功能，有利于代码的管理与复用。

```python
# 导入模块的语法格式
import 模块1名称, 模块2名称, 模块3名称
模块1名称.模块1内函数名称()

import 模块4名称 as 模块名称缩写


form 模块名称5 import 模块5内函数名称
模块5内函数名称()
```

而在 `python` 中用于管理模块文件的文件夹叫做 **包**（`package`），一个文件夹要成为一个包，需要包含一个名为 `__init__.py` 的文件，该文件可以为空，作为该文件夹为包的标识。

待补充...

## 6.2.定义函数

```python
# 定义函数的语法形式
def functionName(变元1, 变元2, 变元3=缺省值):
    """函数说明"""
    # 某些代码块
    return 返回值/[return None]
    
functionName(参数1, 参数2) # 函数调用
functionName(变元2=参数2, 变元1=参数1) # 函数调用
```

对于无返回值的函数，默认返回 `None`（并且是隐式返回）。

调用栈，待补充...

注意函数和全局变量的结合使用：

```python
# 全局变量结合函数的使用
def function(num):
    global number # 3.告知 python 该变量为全局变量
    print(number) # 4.打印 10
    number = 0 # 5.修改全局变量为 0
    print(number) # 6.打印为 0

number = 10 # 1.number 的初始值
print(number) # 2.打印 10
function(number)
print(number) # 7.经过 function() 的修改，打印 0

""" 输出结果
10
10
0
0
"""
```

## 6.3.输出输出函数

### 6.3.1.input()



### 6.3.2.Print()

-   参数 `end` 可以替换待输出字符串的最末尾有效字符（默认为换行字符）

-   参数 `sep` 可以替换待输出字符串们的分割字符（默认为空格字符）

# 7.字符串

## 7.1.字符串操作

### 7.1.1.字符串连接

使用 `+=` 操作符可以将两个字符串连接起来，前后操作数均为字符串。

```python
# 字符串连接
str1 = 'limou'
str2 = '3434'
str1 += str2
print(str1)

""" 输出结果
limou3434
"""
```

### 7.1.2.字符串复制

使用 `*=` 可以复制字符串，左操作数为字符串，右

```python
# 字符串复制
str = 'limou3434'
str *= 3
print(str)

""" 输出结果
limou3434limou3434limou3434
"""
```

### 7.1.3.字符串大小

获取字符串的大小可以使用 `len()`。

### 7.1.4.字符串获取

1.   获取单个字符

     ```python
     # 字符串获取（获取字符）
     str = 'limou3434'
     print(str[2])
     
     """ 输出结果
     m
     """
     ```

2.   获取子字符串

     (1)`find()/rfind()`：没查找到则返回 `-1`

     (2)`index(查找子串 sub, start, end)`：没查找到则抛出异常

     ```python
     # 字符串获取（获取字符串）
     str = 'limou3434'
     print(str.find('3434', 0)) # 从 0 下标开始寻找子串 '3434'
     print(str.find('3434', len(str)-2)) # 从 7 下标开始寻找子串 '3434'
     print(str.rindex('mou', 1, 5)) # 从下标 1 到 5 开始寻找子串 'mou'
     
     """ 输出结果
     5
     -1
     2
     """
     ```

## 7.2.正则表达式

# 8.标准容器

## 8.1.列表

## 8.2.集合

## 8.3.元组

## 8.4.字典

## 8.4.字节数组



# 9.文件

## 9.1.普通文件操作

## 9.2.Excel 操作

## 9.3.Word 操作

## 9.4.CSV 操作

## 9.5.JSON 操作

# 10.程序调试

## 10.1.异常

```python
# 异常的语法形式
```

```python
# 使用异常
def function(number):
    try:
        return 10 / number
    except ZeroDivisionError:
        print("Error: Incalid argument")

print(function(10))
print(function(3))
print(function(0))
print(function(1))
```

## 10.2.断言

## 10.3.日志

## 10.4.调试

# 11.web

# 12.图形操作

# 13.时间

# 14.多线程

# 15.随机数

## 15.1.所需模块

随机数需要用的的模块为==random==，该模块用于生成随机数或随机选择序列中的元素。内部提供了多种函数，可生成不同类型随机数，如：整数、浮点数、随机序列、随机字符串、洗牌、抽样等。

## 15.2.方法概览

1.   整数随机数

     `randint(a, b)`：返回 `[a,b]` 区间内一个随机整数

2.   浮点随机数

     (1)`random()`：返回 `[0, 1.0]` 之间的一个随机浮点数

     (2)`uniform(a, b)`：返回 `[a,b]` 区间内的一个随机浮点数

3.   列表随机数

     (1)`shuffle(seq)`：将序列 `seq` 中的元素进行随机排序

     (2)`choice(seq)`：从序列 `seq` 中随机返回一个元素

     (3)`sample(seq, k)`：从序列 `seq` 中随机选择 `k` 个元素作为列表返回
     

>   注意：上述的序列指如下序列
>
>   1.  字符串（`str`）
>   2.  列表（`list`）
>   3.  元组（`tuple`）
>   4.  集合（`set`）
>   5.  字典（`dict`）
>   6.  字节数组（`bytearray`）
>   7.  字节数组（`bytes`）

```python
# 取得随机数
import random

print(random.randint(1, 10))

print(random.random())
print(random.uniform(1, 10))

arr = [1, 2, 3, 4, 5]
random.shuffle(arr)
print(arr)
print(random.choice(arr))
print(random.sample(arr, 3))
```

# 16.命令行

sys.exit()

# 17.编码规范

1.   The Zen of Python, by Tim Peters（《Python 之禅》，作者: 蒂姆·彼得斯）
2.   Beautiful is better than ugly.（美总比丑好。）
3.   Explicit is better than implicit.（显式优于隐式。）
4.   Simple is better than complex.（简单总比复杂好。）
5.   Complex is better than complicated.（复杂总比复杂好。）
6.   Flat is better than nested.（平坦的比嵌套的好。）
7.   Sparse is better than dense.（稀疏比密集好。）
8.   Readability counts.（可读性。）
9.   Special cases aren't special enough to break the rules.（特殊情况没有特殊到违反规则的程度。）
10.   Although practicality beats purity.（尽管实用胜过纯粹。）
11.   Errors should never pass silently.（错误不应该无声地传递。）
12.   Unless explicitly silenced.（除非被明确噤声。）
13.   In the face of ambiguity, refuse the temptation to guess.（面对模棱两可，拒绝猜测的诱惑。）
14.   There should be one-- and preferably only one --obvious way to do it.（应该有一种——最好只有一种——明显的方法来做到这一点。）
15.   Although that way may not be obvious at first unless you're Dutch.（虽然这种方法一开始可能并不明显，除非你是荷兰人。）
16.   Now is better than never.（现在总比不做好。）
17.   Although never is often better than right now.（虽然永远不要比现在更好。）
18.   If the implementation is hard to explain, it's a bad idea.（如果实现很难解释，那就是一个坏主意。）
19.   If the implementation is easy to explain, it may be a good idea.（如果实现很容易解释，这可能是个好主意。）
20.   Namespaces are one honking great idea -- let's do more of those!（名称空间是一个很棒的想法——让我们做更多这样的事情吧！）

# 18.实践

后续补充...