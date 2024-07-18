>   前要：以下资料选取自谷歌对 `python` 开发风格指南，源网站文档为 [Google 开源项目风格指南](https://zh-google-styleguide.readthedocs.io/en/latest/contents/) 
>
>   如果可以的话，建议您直接阅读文档，这里我只选取了短期内一定需要遵循的编码规范（尤其是注释和命名风格）
>
>   若以后代码变得复杂，出现了一些意料之外的情况，就会拓展该部分规范的内容...
>
>   本文大部分来自于 [Google 开源项目风格指南](https://zh-google-styleguide.readthedocs.io/en/latest/contents/) 如有缺漏或误解，欢迎提出 `Issue`，如果能提交一个 `Pull Requests` 那就更好了... 

# 1.行宽设置多少合理？

最大行宽设置为 `80` 个字符（实际应用中只要避免代码块超出一个屏幕即可），但有例外:

1.  长的导入 (`import`) 语句.
2.  注释里的 `URL`、路径名以及长的标志 (`flag`).
3.  不便于换行、不包含空格、模块级的长字符串常量, 比如 `URL` 或路径名.
4.  `Pylint` 禁用注释. (例如: `# pylint: disable=invalid-name`)

如果遇到一些必须换行的代码块，不要使用 `C` 式的 `\` 反斜杠换行，应该利用 `Python` 的 [圆括号, 中括号和花括号的隐式续行 (implicit line joining)](http://docs.python.org/2/reference/lexical_analysis.html#implicit-line-joining)。如有需要, 可以在表达式外围添加一对括号。

正确：

```python
foo_bar(self, width, height, color='黑', design=None, x='foo',
        emphasis=None, highlight=0)

if (width == 0 and height == 0 and
    color == '红' and emphasis == '加粗'):

 (bridge_questions.clarification_on
  .average_airspeed_of.unladen_swallow) = '美国的还是欧洲的?'

 with (
     very_long_first_expression_function() as spam,
     very_long_second_expression_function() as beans,
     third_thing() as eggs,
 ):
   place_order(eggs, beans, spam, beans)
```

错误：

```python
if width == 0 and height == 0 and \
     color == '红' and emphasis == '加粗':

bridge_questions.clarification_on \
    .average_airspeed_of.unladen_swallow = '美国的还是欧洲的?'

with very_long_first_expression_function() as spam, \
      very_long_second_expression_function() as beans, \
      third_thing() as eggs:
  place_order(eggs, beans, spam, beans)
```

特殊情况下，如果字符串的字面量 (`literal`) 超过一行, 应该用圆括号实现隐式续行:

```python
x = ('这是一个很长很长很长很长很长很长'
     '很长很长很长很长很长的字符串')
```

必要时, 注释中的长 `URL` 可以独立成行.

```python
# 详情参见
# http://www.example.com/us/developer/documentation/api/content/v2.0/csv_file_name_extension_full_specification.html
```

>   补充：如果您使用的编辑器是 `VScode`，不妨配置一下行宽提示线，[VScode 设置行宽提示线](https://blog.csdn.net/m0_73168361/article/details/135924572?spm=1001.2014.3001.5501)...

# 2.要多使用括号提高可读性吗？

使用括号时宁缺毋滥。可以把元组 (`tuple`) 括起来, 但不强制. 不要在返回语句或条件语句中使用括号, 除非用于隐式续行或表示元组。

正确:

```python
if foo:
    bar()
while x:
    x = bar()
if x and y:
    bar()
if not x:
    bar()
# 对于包含单个元素的元组, 括号比逗号更直观.
onesie = (foo,)
return foo
return spam, beans
return (spam, beans)
for (x, y) in dict.items(): ...
```

错误:

```python
if (x):
    bar()
if not(x):
    bar()
return (foo)
```

# 3.缩进要使用空格还是制表符？

用 `4` 个空格作为缩进，不直接要使用制表符。

该规定单纯因为不同编译器对制表符的设定可能不太一样，有时会导致一些缩减错误造成代码阅读困难，进而引发别的问题...尽量遵循该规定。

使用隐式续行时, 应该把括起来的元素垂直对齐(参见 [行宽](https://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/python_style_rules/#line-length) 章节的示例), 或者添加 `4` 个空格的悬挂缩进. 右括号 (圆括号, 方括号或花括号) 可以置于表达式结尾或者另起一行. 另起一行时右括号应该和左括号所在的那一行缩进相同.

正确:

```python
# 与左括号对齐.
foo = long_function_name(var_one, var_two,
                         var_three, var_four)
meal = (spam,
        beans)

# 与字典的左括号对齐.
foo = {
    'long_dictionary_key': value1 +
                           value2,
    ...
}

# 4个空格的悬挂缩进; 首行没有元素
foo = long_function_name(
    var_one, var_two, var_three,
    var_four)
meal = (
    spam,
    beans)

# 4个空格的悬挂缩进; 首行没有元素
# 右括号另起一行.
foo = long_function_name(
    var_one, var_two, var_three,
    var_four
)
meal = (
    spam,
    beans,
)

# 字典中的4空格悬挂缩进.
foo = {
    'long_dictionary_key':
        long_dictionary_value,
    ...
}
```

错误:

```python
# 首行不能有元素.
foo = long_function_name(var_one, var_two,
    var_three, var_four)

# 禁止2个空格的悬挂缩进.
foo = long_function_name(
  var_one, var_two, var_three,
  var_four)

# 字典没有悬挂缩进.
foo = {
    'long_dictionary_key':
    long_dictionary_value,
    ...
}
```

# 4.到底要不要给序列尾部添加逗号？

在编写 `Python` 代码时，仅当 `]`, `)`, `}` 和最后一个元素不在同一行时, 推荐在序列尾部添加逗号。

```python
# 推荐的写法
my_list = [
    1,
    2,
    3,
]

# 不推荐的写法
my_list = [
    1,
    2,
    3  # 没有逗号
]
```

# 5.怎么注释和和添加文档字符串？

模块、函数、方法的文档字符串和内部注释一定要采用正确的风格.

`Python` 的文档字符串用于注释代码. 文档字符串是位于包、模块、类或函数里第一个语句的字符串. 可以用对象的 `__doc__` 成员自动提取这些字符串, 并为 `pydoc` 所用. (`pydoc` 是 `Python` 自带的文档生成工具，可以用于查看模块、类、函数等的文档信息。可以试试在您的模块上运行 `pydoc` 并观察结果). 

文档字符串一定要用三重双引号 `"""` 的格式 (依据 [PEP-257](http://www.python.org/dev/peps/pep-0257/) ). 文档字符串应该是一行概述 (整行不超过 `80` 个字符), 以句号、问号或感叹号结尾. 如果要写更多注释 (推荐), 那么概述后面必须紧接着一个空行, 然后是剩下的内容, 缩进与文档字符串的第一行的第一个引号对齐.

## 5.1.模块

文件的开头应该是文档字符串, 其中应该描述该模块内容和用法.

```python
"""模块或程序的一行概述, 以句号结尾.

留一个空行. 接下来应该写模块或程序的总体描述. 也可以选择简要描述导出的类和函数,
和/或描述使用示例.

经典的使用示例:

foo = ClassFoo()
bar = foo.FunctionBar()
"""
```

由于我们是第一次协作开发，注释一定要记得及时更新和书写全面，冗余一点在短期内都没有太多问题（但是尽量简短，一些不必要的，显而易见的代码操作就不要用注释提示了，增加阅读负担）...

## 5.2.函数和方法

这里的函数是指函数、方法、生成器 (`generator`) 和特性 (`property`).

满足下列任意特征的任何函数都必须有文档字符串:

1.  公开 `API` 的一部分
2.  长度过长
3.  逻辑不能一目了然

文档字符串应该提供充分的信息, ==让调用者无需阅读函数的代码就能调用函数. 文档字符串应该描述函数的调用语法和语义信息, 而不应该描述具体的实现细节, 除非这些细节会影响函数的用法==. 

比如, 如果函数的副作用是会修改某个传入的对象, 那就需要在文档字符串中说明. 对于微妙、重要但是与调用者无关的实现细节, 相较于在文档字符串里说明, 还是在代码中间加注释更好.

文档字符串可以是陈述句 (`"""Fetches rows from a Bigtable."""`) 或者祈使句 (`"""Fetch rows from a Bigtable."""`), 不过一个文件内的风格应当一致. 对于 `@property` 修饰的数据描述符 (`data descriptor`), 文档字符串应采用和属性 (`attribute`) 或 [函数参数](https://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/python_style_rules/#doc-function-args) 一样的风格 (`"""Bigtable 路径."""` 而非 `"""返回 Bigtable 路径."""`).

对于覆写 (`override`) 基类 (`base class`) 方法的子类方法, 可以用简单的文档字符串引导读者阅读基类方法的文档字符串, 比如 `"""参见基类.""""`. 这样是为了避免到处复制基类方法中已有的文档字符串. 然而, 如果覆写的子类方法与基类方法截然不同, 或者有更多细节需要记录 (例如有额外的的副作用), 那么子类方法的文档字符串中至少要描述这些区别.

函数的部分特征应该在以下列出特殊小节中记录. 每小节有一行标题, 标题以冒号结尾. 除标题行外, 小节的其他部分应有 `2` 个或 `4` 个空格 (同一文件内应保持一致) 的悬挂缩进. 如果函数名和函数签名 (`signature`) 可以见名知意, 以至于一行文档字符串就能恰当地描述该函数, 那么可以省略这些小节.

>   *   **Args**: (参数:)
>
>       列出所有参数名. 参数名后面是一个冒号, 然后是一个空格或者换行符, 最后是描述. 如果描述过长以至于一行超出了 `80` 字符, 则描述部分应该比参数名所在的行多 `2` 个或者 `4` 个空格 (文件内应当一致) 的悬挂缩进. 如果代码没有类型注解, 则描述中应该说明所需的类型. 如果一个函数有形如 `*foo` (可变长参数列表) 或者 `**bar` (任意关键字参数) 的参数, 那么列举参数名时应该写成 `*foo` 和 `**bar` 的这样的格式.
>
>   *   **Returns**: (“返回:”)
>
>       生成器应该用 “Yields:” (“生成:” )描述返回值的类型和意义. 如果函数仅仅返回 `None`, 这一小节可以省略. 如果文档字符串以 `Returns` (返回) 或者 `Yields` (生成) 开头 (例如 "" "返回 Bigtable 的行, 类型是字符串构成的元组." "") 且这句话已经足以描述返回值, 也可以省略这一小节. 
>
>       不要模仿 `Numpy` 风格的文档 ([例子](http://numpy.org/doc/stable/reference/generated/numpy.linalg.qr.html)). 他们在文档中记录作为返回值的元组时, 写得就像返回值是多个值且每个值都有名字 (没有提到返回的是元组). 应该这样描述此类情况: “返回: 一个元组 (mat_a, mat_b), 其中 mat_a 是…, 且 …”.
>
>       文档字符串中使用的辅助名称不需要和函数体的内部变量名一致 (因为这些名称不是 API 的一部分).
>
>   *   **Raises**: (抛出:)
>
>       列出与接口相关的所有异常和异常描述. 用类似 `Args` (参数) 小节的格式，写成异常名+冒号+空格/换行, 并添加悬挂缩进. 不要在文档中记录违反 `API` 的使用条件时会抛出的异常 (因为这会让违背 API 时出现的效果成为 `API` 的一部分, 这是矛盾的).

```python
def fetch_smalltable_rows(
    table_handle: smalltable.Table,
    keys: Sequence[bytes | str],
    require_all_keys: bool = False,
) -> Mapping[bytes, tuple[str, ...]]:
    """从 Smalltable 获取数据行.

    从 table_handle 代表的 Table 实例中检索指定键值对应的行. 如果键值是字符串,
    字符串将用 UTF-8 编码.

    参数:
        table_handle: 处于打开状态的 smalltable.Table 实例.
        keys: 一个字符串序列, 代表要获取的行的键值. 字符串将用 UTF-8 编码.
        require_all_keys: 如果为 True, 只返回那些所有键值都有对应数据的
            行.

    返回:
        一个字典, 把键值映射到行数据上. 行数据是字符串构成的元组. 例如:

        {b'Serak': ('Rigel VII', 'Preparer'),
         b'Zim': ('Irk', 'Invader'),
         b'Lrrr': ('Omicron Persei 8', 'Emperor')}

        返回的键值一定是字节串. 如果字典中没有 keys 参数中的某个键值, 说明
        表格中没有找到这一行 (且 require_all_keys 一定是 false).

    抛出:
        IOError: 访问 smalltable 时出现错误.
    """
```

以下这种在 `Args` (参数) 小节中换行的写法也是可以的:

```python
def fetch_smalltable_rows(
   table_handle: smalltable.Table,
   keys: Sequence[bytes | str],
   require_all_keys: bool = False,
) -> Mapping[bytes, tuple[str, ...]]:
   """从 Smalltable 获取数据行.

   从 table_handle 代表的 Table 实例中检索指定键值对应的行. 如果键值是字符串,
   字符串将用 UTF-8 编码.

   参数:
       table_handle:
         处于打开状态的 smalltable.Table 实例.
       keys:
         一个字符串序列, 代表要获取的行的键值. 字符串将用 UTF-8 编码.
       require_all_keys:
         如果为 True, 只返回那些所有键值都有对应数据的行.

   返回:
       一个字典, 把键值映射到行数据上. 行数据是字符串构成的元组. 例如:

       {b'Serak': ('Rigel VII', 'Preparer'),
        b'Zim': ('Irk', 'Invader'),
        b'Lrrr': ('Omicron Persei 8', 'Emperor')}

       返回的键值一定是字节串. 如果字典中没有 keys 参数中的某个键值, 说明
       表格中没有找到这一行 (且 require_all_keys 一定是 false).

   抛出:
       IOError: 访问 smalltable 时出现错误.
   """
```

## 5.3.类

类的定义下方应该有一个描述该类的文档字符串. 如果你的类包含公有属性 (`attributes`), 应该在 `Attributes` (属性) 小节中记录这些属性, 格式与函数的 `Args` (参数) 小节类似.

```python
class SampleClass(object):
   """这里是类的概述.

   这里是更多信息....
   这里是更多信息....

   属性:
       likes_spam: 布尔值, 表示我们是否喜欢午餐肉.
       eggs: 用整数记录的下蛋的数量.
   """

   def __init__(self, likes_spam = False):
       """用某某某初始化 SampleClass."""
       self.likes_spam = likes_spam
       self.eggs = 0

   def public_method(self):
       """执行某某操作."""
```

类的文档字符串开头应该是一行概述, 描述类的实例所代表的事物. 这意味着 `Exception` 的子类 (`subclass`) 应该描述这个异常代表什么, 而不是描述抛出异常时的环境. 类的文档字符串不应该有无意义的重复, 例如说这个类是一种类.

正确:

```python
class CheeseShopAddress:
"""奶酪店的地址.

...
"""

class OutOfCheeseError(Exception):
"""没有可用的奶酪."""
```

错误:

```python
class CheeseShopAddress:
"""一个描述奶酪店地址的类.

...
"""

class OutOfCheeseError(Exception):
"""在没有可用的奶酪时抛出."""
```

## 5.4.块注释和行注释

最后一种需要写注释的地方是代码中复杂的部分. 如果你可能在以后 [代码评审 (code review)](http://en.wikipedia.org/wiki/Code_review) 时要解释某段代码, 那么现在就应该给这段代码加上注释. 应该在复杂的操作开始前写上若干行注释. 对于不是一目了然的代码, 应该在行尾添加注释.

```python
# 我们用加权的字典搜索, 寻找 i 在数组中的位置. 我们基于数组中的最大值和数组
# 长度, 推断一个位置, 然后用二分搜索获得最终准确的结果.

if i & (i-1) == 0:  # 如果 i 是 0 或者 2 的整数次幂, 则为真.
```

了提高可读性, 注释的井号和代码之间应有至少 `2` 个空格, 井号和注释之间应该至少有一个空格.

除此之外, 绝不要仅仅描述代码. 应该假设读代码的人比你更懂 `Python`, 只是不知道你的代码要做什么.

```python
# 不好的注释: 现在遍历数组 b, 确保每次 i 出现时, 下一个元素是 i+1
```

## 5.5.TODO 注释

在临时、短期和不够完美的代码上添加 `TODO` (待办) 注释.

待办注释以 `TODO` (待办) 这个全部大写的词开头, 紧跟着是用括号括起来的上下文标识符 (最好是 bug 链接, 有时是你的用户名). 最好是诸如 `TODO(https://crbug.com/<bug编号>):` 这样的 `bug` 链接, 因为 `bug` 有历史追踪和评论, 而程序员可能发生变动并忘记上下文. `TODO` 后面应该解释待办的事情.

统一 `TODO` 的格式是为了方便搜索并查看详情. `TODO` 不代表注释中提到的人要做出修复问题的保证. 所以, 当你创建带有用户名的 `TODO` 时, 大部分情况下应该用你自己的用户名.

```python
# TODO(crbug.com/192795): 研究 cpufreq 的优化.
# TODO(你的用户名): 提交一个议题 (issue), 用 '*' 代表重复.
```

如果你的 `TODO` 形式类似于”将来做某事”, 请确保其中包含特别具体的日期 (“2009 年 11 月前解决”) 或者特别具体的事件 (“当所有客户端都能处理 XML 响应时, 删除这些代码”), 以便于未来的代码维护者理解.

# 6.字符串风格化是使用 + 还是 f-string？

应该用 [f-string](https://docs.python.org/zh-cn/3/reference/lexical_analysis.html#f-strings)、 `%` 运算符或 `format` 方法来格式化字符串. 即使所有参数都是字符串, 也如此. 你可以自行评判合适的选项. 可以用 `+` 实现单次拼接, 但是不要用 `+` 实现格式化.

正确:

```python
x = f'名称: {name}; 分数: {n}'
x = '%s, %s!' % (imperative, expletive)
x = '{}, {}'.format(first, second)
x = '名称: %s; 分数: %d' % (name, n)
x = '名称: %(name)s; 分数: %(score)d' % {'name':name, 'score':n}
x = '名称: {}; 分数: {}'.format(name, n)
x = a + b
```

错误:

```python
x = first + ', ' + second
x = '名称: ' + name + '; 分数: ' + str(n)
```

# 7.到底使用哪一种引号？

应该保持同一文件中字符串引号的一致性. 选择 `'` 或者 `"` 以后不要改变主意. 如果需要避免用反斜杠来转义引号, 则可以使用另一种引号.

正确:

```python
Python('为什么你要捂眼睛?')
Gollum("I'm scared of lint errors. (我害怕格式错误.)")
Narrator('"很好!" 一个开心的 Python 审稿人心想.')
```

(译者注: 注意 “I’m” 中间有一个单引号，所以这一行的外层引号可以用不同的引号.)

错误:

```python
Python("为什么你要捂眼睛?")
Gollum('格式检查器. 它在闪耀. 它要亮瞎我们.')
Gollum("伟大的格式检查器永在. 它在看. 它在看.")
```

多行字符串推荐使用 `"""` 而非 `'''`. 当且仅当项目中用 `'` 给常规字符串打引号时, 才能在文档字符串以外的多行字符串上使用 `'''`. 无论如何, 文档字符串必须使用 `"""`.

我们在项目中最好是使用 `'` 把常规字符串引起，非必要是时用（例如字符串中存在 `'` 字符）就使用 `"` 引起，多行字符串使用 `'''` 引起，只有在上述提及的文档字符串中才使用 `"""`。

# 8.导入模块应该按照怎样的格式和顺序导入？

导入语句应该各自独占一行. [typing 和 collections.abc 的导入除外](https://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/python_style_rules/#typing-imports). 例如:

正确:

```
from collections.abc import Mapping, Sequence
import os
import sys
from typing import Any, NewType
```

错误:

```
import os, sys
```

导入语句必须在文件顶部, 位于模块的注释和文档字符串之后、全局变量和全局常量之前. 导入语句应该按照如下顺序分组, 从通用到特殊:

1.  导入 `Python` 的 `__future__`. 例如:

    >   ```
    >   from __future__ import annotations
    >   ```
    >
    >   参见前文有关 `__future__` 语句的描述.

2.  导入 `Python` 的标准库. 例如:

    >   ```
    >   import sys
    >   ```

3.  导入 [第三方](https://pypi.org/) 模块和包. 例如:

    >   ```
    >   import tensorflow as tf
    >   ```

4.  导入代码仓库中的子包. 例如:

    >   ```
    >   from otherproject.ai import mind
    >   ```

# 9.多语句应不应该简化为一行？

通常每个语句应该独占一行.

不过, 如果判断语句的主体与判断条件可以挤进一行, 你可以将它们放在同一行. 特别注意这不适用于 `try` / `except`, 因为 `try` 和 `except` 不能放在同一行. 只有在 `if` 语句没有对应的 `else` 时才适用.

正确:

```python
if foo: bar(foo)
```

错误:

```python
if foo: bar(foo)
else:   baz(foo)

try:               bar(foo)
except ValueError: baz(foo)

try:
    bar(foo)
except ValueError: baz(foo)
```

# 10.如何给变量、类、函数命名？

## 10.1.字符风格

`python` 的命名风格大部分都是结合下划线和大驼峰命名法，具体如下：

>   模块名: `module_name`;
>
>   包名: `package_name`; 
>
>   类名: `ClassName`; 
>
>   方法名: `method_name`; 
>
>   异常名: `ExceptionName`; 
>
>   函数名: `function_name`, `query_proper_noun_for_thing`, `send_acronym_via_https`; 
>
>   全局常量名: `GLOBAL_CONSTANT_NAME` ; 
>
>   全局变量名: `global_var_name`; 
>
>   实例名: `instance_var_name`; 
>
>   函数参数名: `function_parameter_name`; 
>
>   局部变量名: `local_var_name`.

## 10.2.命名风格

函数名、变量名和文件名应该是 ==描述性的==, 避免缩写. 特别要避免那些对于项目之外的人有歧义或不熟悉的缩写, 也不要通过省略单词中的字母来进行缩写.

>   强调：严厉禁止使用单个字符作为名字，除非时某些及特殊情况（例如循环变量 `i`），否则不要写出类似 `x = 1`、`t = 100` 这样代码。

所有 `Python` 文件名都应该以 `.py` 为文件后缀且不能包含连字符 (`-`). 

>   强调：关于使用哪些英文字母来命名，可以借助一些 [命名网站](https://unbug.github.io/codelf/) 来帮助我们，或者合理使用一些 `AI` 工具，总之就是不要随便命名！
>
>   ![image-20240130091640857](./assets/image-20240130091640857.png)

# 11.函数长度到多少需要被拆分？

函数应该小巧且专一.

我们承认有时长函数也是合理的, 所以不硬性限制函数长度. 若一个函数超过 `40` 行, 应该考虑在不破坏程序结构的前提下拆分这个函数（拆分为若干子函数）.

即使一个长函数现在没有问题, 几个月后可能会有别人添加新的效果. 此时容易出现隐蔽的错误. 保持函数简练, 这样便于别人阅读并修改你的代码.

当你使用某些代码时, 可能发现一些冗长且复杂的函数. 要勇于修改现有的代码: 如果该函数难以使用或者存在难以调试的错误, 亦或是你想在不同场景下使用该函数的片段, 不妨考虑把函数拆分成更小、更容易管理的片段.

