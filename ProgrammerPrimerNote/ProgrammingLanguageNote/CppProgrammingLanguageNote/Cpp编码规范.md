>   https://zh-google-styleguide.readthedocs.io/en/latest/google-cpp-styleguide/index.html

> 如果您实在懒得看这些规则类，可以看下面这份简易的代码以了解风格。
>
> ```shell
> # 项目目录
> project/
> ├── .git # git 存储仓库有管理
> ├── .gitignore # git 忽略文件管理
> ├── include/ # 存放头文件的目录
> │   ├── module_1/    # 模块 1 的头文件目录
> │   │   ├── header_1.h
> │   │   └── header_2.h
> │   ├── module_2/    # 模块 2 的头文件目录
> │   │   ├── header_1.h
> │   │   └── header_2.h
> │   └── common.hpp  # 公共头文件，如宏定义、常量等
> ├── src/ # 存放源文件的目录
> │   ├── module_1/    # 模块 1 的源文件目录
> │   │   ├── source1.cpp
> │   │   └── source2.cpp
> │   ├── module_2/    # 模块 2 的源文件目录
> │   │   ├── source_1.cpp
> │   │   └── source_2.cpp
> │   └── main.cpp    # 主程序入口源文件
> ├── tests/ # 单元测试代码
> ├── bin/ # 可以直接使用的可执行文件
> ├── build/ # 构建过程中的临时文件
> ├── doc/ # 帮助文档
> ├── README.md # 项目手册
> ├── CMakeLists.txt 或 makefile  # CMake 配置文件或者其他构建工具配置文件
> └── 协议 # 根据软件定位进行开源或闭源(开源则填写开源证书)
> ```
>
> ```cpp
> // header_1.h
> #ifndef PROJECT_MODULE_1_HEADER_1_H_ // 使用头文件防护符的宏
> #define PROJECT_MODULE_1_HEADER_1_H_
> 
> namespace { // 匿名命名空间(类型声明, 全局变量, 静态函数)
>  using Integer = int;
>  void GlobalFunction();
> } // namespace end
> 
> namespace project::module_1 { // 命名空间与子命名空间做分离而不做嵌套, 还不用显示定义 project 命名空间
> // 声明枚举
> enum Color {
>  LIGHT_RED,    // 默认值为 0
>  LIGHT_GREEN,  // 默认值为 1
>  LIGHT_BLUE    // 默认值为 2
> };
> 
> // 声明结构体
> struct DataInfo {
>  int a_data_1; // 声明结构体内成员变量
>  float a_data_2;
> 
>  DataInfo() { 
>      // ...
>  }
> };
> 
> // 声明类
> class DataManger {
> public:
>  DataManger(); // 构造函数
>  DataInfo getDataInfo(DataInfo data_info_1, DataInfo data_info_2); // 声明类内成员方法
> 
> protected:
>  // ...
> 
> private:
>  DataInfo data_info_1_; // 声明类内成员变量
>  DataInfo data_info_2_; // 声明类内成员变量
> };
> 
> // 声明常量
> constexpr int MY_VALUE = 1;
> 
> // 声明函数
> void MyFunction();
> 
> // using 别名
> using PropertiesMap = hash_map<UrlTableProperties *, string>;
> 
> } // namespace project end
> 
> # endif // PROJECT_MODULE_1_HEADER_1_H_
> ```
>
> ```cpp
> // source_1.cc
> #include "header_1.h"
> project::module_1::DataManger::DataManger(
>  	DataInfo data_info_1,
>  	DataInfo data_info_2)
>  : data_info_1_(data_info_1)
>  , data_info_2_(data_info_2) {
>  // ...  
> }
> 
> project::module_1::DataManger::getDataInfo(
>  	int x, int y, int z, // 这里假设 x y z 是具有某种结构的参数
>  	int i) {
>  // TODO(名字-邮件-定制时间->解决时间)
>  if (...) { return 0; }
>  while (...) { continue; }
>  for (...) {
>      // ...
>  }
> 
>  if (
>      	statement1 &&
>      	statement2 &&
>      	statement3) {
>  	// ...
>  }
> }
> ```

# 1.命名规则

## 1.1.通用命名规则

命名规则不论是否合理，只有规则一致性才是命名约定的最核心力量。

- 命名由单词构成，保证不缩写所有的单词，除非它真的广而人知
- 命名都需要保证一致遵循所有的命名规则

## 1.2.文件命名规则

- 文件名全部采用小写，并且使用 `_` 进行分割
- 文件名应该避免和 `/usr/include` 下的文件名重复
- 源文件后缀应使用 `.cc`，头文件后缀应使用 `.h`，并且对应大驼峰的类名

>   补充：这里的文件专门指 `C++` 运行过程中有可能使用的文件，不包含其他文件。

>   补充：前期为了编码方便，小文件可以先使用 `.cpp` 作为过渡，先把代码进行实现，临近结项的时候再使用一些工具进行头文件分离。

## 1.3.类型命名规则

-   类名、结构体名、枚举结构体名、类型模版参数均使用大驼峰
-   `typedef/using` 也都使用大驼峰

## 1.4.变量命名规则

- 普通变量命名，用 `_` 分割小写单词
- 结构成员命名，用 `_` 分割小写单词，但是在结尾不加 `_` 表示为结构成员
- 类内成员命名，用 `_` 分割小写单词，但是在结尾加上 `_` 表示为类内成员

## 1.5.常量命名规则

-   声明为 `constexpr` 或 `const` 的变量，使用全大写单词，并且使用 `_` 来进行分割
-   宏常量使用全大写单词，并且使用 `_` 来进行分割（当然宏能不用就不用，不过作为头文件防护符的宏是必须的，其格式有些特殊，应该使用 `项目_路径_文件名_H_` 的命名方式）
-   枚举常量使用全大写单词，并且使用 `_` 来进行分割

## 1.6.函数命名规则

-   普通函数使用大驼峰命名
-   类内方法使用小驼峰命名

## 1.7.命名空间规则

-   项目文件夹的名字作为根命名空间的名字
-   模块文件夹的名字作为子命名空间的名字

# 2.注释规则

-   由于本文章主要面向个人开发，所以初次开发统一使用简单的 `//` 注释，结项使用 `Doxygen` 注释，并且方便生成文档。而关于 `Doxygen` 的注释风格，待补充...
-   不用在 `.h` 和 `.cc` 中来回复制注释，这是无效的，尽可能把注释写在 `.h` 处，如果 `.cc` 中需要特别注意某些细节，那么就进行简单的注释即可
-   永远不要用自然语言翻译代码作为注释，要假定阅读代码的人 `C++` 比您的要高明
-   `TODO` 注释采用 `TODO(名字-邮件-定制时间->解决时间)` 格式

# 3.头文件规则

-   使用头文件防护符，其格式为 `#ifndef 项目_路径_文件名_H_`
-   头文件需要导入对应实现中的所有头文件，达到自给自足的目的，重复包含问题可以被防护符解决
-   只把 `10` 行以下的小函数定义为内联，并且对应的实现直接写入头文件中，包括类内的内联函数，不要过度依赖编译器的内联判定，并且不要对虚函数和递归函数进行内联声明
-   包含的头文件顺序推荐为 `C 语言系统库头文件, C++ 标准库头文件, 其他库的头文件, 本项目的头文件` 每一组都使用空行进行分割

# 4.代码格式规则

## 4.1.行长度格式规则

-   我不认为一行 `80` 字符对于现代显示器有什么优良的可读性提升，让那些用老设备显示器甚至手机查看代码的人见鬼去吧！您只要保证不要长的过于离谱就行

## 4.2.字符集格式规则

-   强制您使用 `ASCII` 字符，并且使用 `UTF-8` 编码编写程序，但是除去一些特殊需求

## 4.3.代码缩进格式规则

-   我推荐使用 `4` 空格

## 4.4.参数缩进格式规则

-   参数如果需要进行缩进，可以使用 `8` 空格在前，然后直接进行换行
-   如果参数本身具有一定的结构，可以在换行的时候把一些参数控制在同一行

## 4.5.列表初始化格式规则

-   如果迫不得已需要进行换行，应尽可能保证每一行为一个列表

## 4.6.控制流格式规则

-   在 `()` 中不推荐在最左和最右靠近括号的地方带上空格，但内部的其他地方允许
-   当语句简单只有一句才允许使用一行 `if、while、for` 语句（其中 `if` 要保证没有使用 `else`）
-   在使用 `if-else, while, for, case` 中无论内部代码多小，一定要使用 `{}`
-   空循环体应使用 `continue` 增强语意义

## 4.7.指针变量格式规则

-   在声明指针变量或参数时，星号与类型或变量名紧挨都可以，但是我推荐您靠近类型

## 4.8.逻辑与或格式规则

-   如果有大量的逻辑与和逻辑或，可以考虑在写完一个逻辑表达式后，在最末尾添加逻辑运算符然后再进行换行，换行规则类似函数参数

## 4.9.预处理指令格式规则

-   预处理不要有任何缩进，并且不要使用空格分割 `#`

## 4.10.访问限定符格式规则

-   `public` 放在最前面，然后是 `protected`，最后是 `private`
-   访问限定符关键词后不要保留空行，直接开始编写程序
-   除第一个 `public` 外，其他访问限定符关键词前要空一行

## 4.11.初始化列表格式

-   如果一定要换行，直接开始换行后，使用 `:` 和 `,` 开始做行首
-   初始化列表应该放在函数实现的源文件中

## 4.12.命名空间格式规则

-   命名空间不要进行缩进，哪怕是有嵌套的命名空间
-   命名空间内部的代码也不要因为命名空间发生缩进
-   被命名空间所包含的代码需要和命名空间的 `{}` 保持一个空行的距离

## 4.13.水平留白格式规则

-   函数声明、函数定义、函数调用中 `()` 不使用空格进行分割，但是 `()` 和 `{}` 间允许
-   在 `if-else, while, for` 和 `()` 的中间位置添加空格进行分割，在 `()` 和 `{}` 的中间位置添加空格进行分割
-   除去一元操作符，其他操作符必须和操作数之间必须保留一个空格进行分割
-   模板中 `<>` 不要和模板类型使用空格进行分割

## 4.14.垂直留白格式规则

-   函数内部实现不要使用空行进行分割
-   函数定义之间使用一行空行进行分割


# 5.作用域规则

## 5.1.命名空间规则

-   禁止使用内联命名空间，内联命名空间会自动把其中的标识符置入外层作用域，导致混淆
-   禁止使用 `using` 打开命名空间内所有的符号
-   允许使用 `using` 对某些长命名进行缩略
-   命名空间与子命名空间做分离而不做嵌套
-   使用匿名命名空间，匿名命名空间内部的 `全局变量, 静态函数, 类型声明` 仅在定义它们的翻译单元中可见，效果可以完全替代语义多重的 `static`

## 5.2.局部变量规则

-   局部变量最好在的定义时就做初始化，或者在定义之后很快进行使用
-   尽量避免使用全局变量和静态全局变量

# 6.类规则

-   不用的成员函数就禁用，直到出现问题
-   只使用 `public` 继承
-   禁止私用多继承
-   子类数据成员必须是私有的，需要时给外界提供 `ste()/get()`
-   谨慎重载运算符

>   还有一些规则待补充...

# 7.函数规则

-   推荐使用返回值而不使用输出型参数，因为语义更好
-   虚函数不允许使用默认参数
-   在可以使用函数重载的情况下不要使用默认参数

>   还有一些规则待补充...

# 8.指针规则

-   能使用智能指针就不要使用普通指针进行 `new` 管理

>   还有一些规则待补充...

# 9.其他规则

## 右值引用

## 变长数组

## 友元

## 异常

## RTTI

## 类型转换

-   使用 `xxx_cast<>()` 之类的转换而不使用 `()` 进行转换

## 前置后置

-   优先使用前置 `++/--`

## const

-   尽可能使用 `const`

## 空指针

-   使用 `nullptr` 替代 `NULL`

## sizeof

-   使用 `sizeof(value)` 好过 `sizeof(type)`，因为变量名不常修改，但类型有可能

## auto

-   类型名又臭又长时，可以使用 `auto` 进行简化

## 列表初始化

-   允许使用列表初始化

## Lambda

# 10.boost 规则

*   [Call Traits](http://www.boost.org/doc/libs/1_58_0/libs/utility/call_traits.htm) : `boost/call_traits.hpp`
*   [Compressed Pair](http://www.boost.org/libs/utility/compressed_pair.htm) : `boost/compressed_pair.hpp`
*   [ : `boost/graph`, except serialization (`adj_list_serialize.hpp`) and parallel/distributed algorithms and data structures(`boost/graph/parallel/*` and `boost/graph/distributed/*`)
*   [Property Map](http://www.boost.org/libs/property_map/) : `boost/property_map.hpp`
*   The part of [Iterator](http://www.boost.org/libs/iterator/) that deals with defining iterators: `boost/iterator/iterator_adaptor.hpp`, `boost/iterator/iterator_facade.hpp`, and `boost/function_output_iterator.hpp`
*   The part of [Polygon](http://www.boost.org/libs/polygon/) that deals with Voronoi diagram construction and doesn’t depend on the rest of Polygon: `boost/polygon/voronoi_builder.hpp`, `boost/polygon/voronoi_diagram.hpp`, and `boost/polygon/voronoi_geometry_type.hpp`
*   [Bimap](http://www.boost.org/libs/bimap/) : `boost/bimap`
*   [Statistical Distributions and Functions](http://www.boost.org/libs/math/doc/html/dist.html) : `boost/math/distributions`
*   [Multi-index](http://www.boost.org/libs/multi_index/) : `boost/multi_index`
*   [Heap](http://www.boost.org/libs/heap/) : `boost/heap`
*   The flat containers from [Container](http://www.boost.org/libs/container/): `boost/container/flat_map`, and `boost/container/flat_set`



>   补充：以下库可以用，但由于如今已经被 `C++11` 标准库取代，不再鼓励。
>
>   *   [Pointer Container](http://www.boost.org/libs/ptr_container/) : `boost/ptr_container`, 改用 [std::unique_ptr](http://en.cppreference.com/w/cpp/memory/unique_ptr)
>   *   [Array](http://www.boost.org/libs/array/) : `boost/array.hpp`, 改用 [std::array](http://en.cppreference.com/w/cpp/container/array)

## 11.平台规则

## Linux

## Windows

