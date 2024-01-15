# 1.数据类型

`python` 中有三种基本数据类型，分别是整数、浮点数、字符串，不同的数据类型可以创建不同的变量。

## 1.1.整数

整数值，待补充...

## 1.2.浮点数

浮点小数，待补充...

## 1.3.字符串

`python` 的字符操作非常方便和多样，是值得重点学习的部分，`python` 的字符冲一般使用 `''` 框起。

### 1.3.1.字符串连接

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

### 1.3.2.字符串复制

```python
# 字符串复制
str = 'limou3434'
str *= 3
print(str)

""" 输出结果
limou3434limou3434limou3434
"""
```

### 1.3.3.字符串大小

获取字符串的大小可以使用 `len()`。

### 1.3.4.字符串获取

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

`python` 的注释只要有三种：

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

# 5.编码理念

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

# 6.实践

后续补充...