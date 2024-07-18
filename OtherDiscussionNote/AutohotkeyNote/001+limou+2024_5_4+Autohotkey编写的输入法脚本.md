关于柚子输入法的相关文章：

1.   https://www.jianshu.com/u/afb1c0f77461
2.   https://www.jianshu.com/p/de27b4d5f573
3.   https://www.jianshu.com/p/6bb392d96366
4.   https://www.jianshu.com/p/5df6b11b3bd0
5.   https://www.jianshu.com/p/8b5e00b70668
6.   https://www.jianshu.com/p/a18e09779da9
7.   https://www.jianshu.com/p/b885f5c53b7d
8.   https://www.jianshu.com/p/80047928599a
9.   https://www.jianshu.com/p/940e90bf9bae
10.   https://www.jianshu.com/p/027567f1870d
11.   https://www.jianshu.com/p/f2fc19db487c
12.   https://www.jianshu.com/p/c2c33ad07a34
13.   https://www.jianshu.com/p/581b616ad567

```
; 引入词库
#Include chinese_characters.ahk

; SplashImage 表示显示一张图片, 这里的语法为: SplashImage, 图片路径, [b 是否显示标题栏] xy 坐标值, 图片窗口指定的唯一标识符
SplashImage, cn1.png, b x220 y400, , , srf_icon ; 主语这里多余的逗号是必要的,

; WinSet 表示设置窗口的属性或状态, 这里的语法为: WinSet, 参数关键字, 值, 指定的窗口标识符
WinSet, Transparent, 0, srf_icon ; 设置窗口透明程度, 完全图片透明(0 为完全透明)
WinSet, ExStyle, +0x20, srf_icon ; 设置窗口拓展样式, 允许鼠标穿透(+ 表示要添加一个样式, 0x20表示具体的扩展样式, 其十六进制值对应着窗口的 WS_EX_TRANSPARENT 样式, 用于允许鼠标穿透)
WinSet, AlwaysOnTop, On, srf_icon ; 设置窗口置顶, 设置为窗口置顶(On 将窗口置顶显示, Off 取消窗口置顶)

; 切换 srf_mode 变量的状态
RShift:: ; 注意这里的换行是必要的
    srf_mode := !srf_mode
    if (srf_mode) {
        WinSet, Transparent, 180, srf_icon ; 如果 srf_mode 为真，设置窗口透明度为 180
    } else {
        WinSet, Transparent, 0, srf_icon ; 如果 srf_mode 为假, 设置窗口透明度为默认值
    }
    return

; 显示风格数组
#if srf_for_select_array0 ; srf_for_select_array0 = srf_for_select_array 子串个数
    ; (1)使用空格、逗号、句点来选择分割数组中的字符
    ; space::send %srf_for_select_array1%
    ; ,::send %srf_for_select_array2%
    ; .::send %srf_for_select_array3%
    ; (2)使用函数来选择字符, 函数内部还会做额外的清空处理
    ::space:: ; 使用户输入空格时输出空格, 而不是其他字符
    1::srf_select(1)
    2::srf_select(2)
    3::srf_select(3)
    4::srf_select(4)
#if

; 当用户有输入字符时
#if srf_all_input
    ; 定义按键 backspace 的行为
    backspace::
        srf_all_input := SubStr(srf_all_input, 1, -1) ; 将 srf_all_input 字符串的最后一个字符移除后重新赋值给 srf_all_input. SubStr() 可以从字符串中提取指定位置的子串, 第一个参数是要提取子串的原始字符串, 第二个参数是子串的起始位置, 第三个参数是子串的结束位置(-1 表示从字符串的最后一个字符开始往前数, 也就是倒数第一个字符).
        if (StrLen(srf_all_input) = 0) { ; 等价写法 "if srf_all_input =" 
            ; srf_all_input 的值就是空字符串
            ; tooltip,,,, 16
            ; srf_for_select_arrayθ =
            ; srf_all_input =
            gosub srf_ualue_off
        } else {
            ; srf_all_input 的值不是空字符串
            ; tooltip, % srf_all_input . "`n" . %srf_all_input%, A_CaretX + 10 , A_CaretY + 20, 16 ; tooltip 用于显示一个简单的提示框. 注意 "% srf_all_input" 中间的空格必须有, 整体就可以取得变量值. 而 %srf_all_input% 的用法有些奇怪, 等待查询.     A_CaretX 和 A_CaretY 是鼠标光标的坐标, 借助鼠标光标参考点设置提示框的位置. 16 是一个可选参数，用于指定提示框的样式
            ; StringSplit, srf_for_select_array, %srf_all_input%, %A_Space%, %A_Space%
            gosub srf_tooltip
        }
        return

    ; 在用户输入到一半时使用 esc 可以中断提示框内容
    esc::
        gosub srf_ualue_off
        return

    ; 在用户输入到一半时使用 enther 可以直接把用户输入的字符直接输出而不进行匹配
    enter::
        send %srf_all_input%
        gosub srf_ualue_off
        return

    ; 在用户输入到一半时使用 Rshift 可以直接把用户输入的字符直接输出而不进行匹配, 同时退回英文模式
    Rshift::
        send %srf_all_input%
        gosub srf_ualue_off
        return
#if

; 定义按键 a 的行为, 当 srf_mode 为真时生效
#if srf_mode ; 注意以下 ::字母:: 后的换行是必要的, 且 return 也是必要的, 不然如果执行第一条就会执行后续的所有指令, 类似 C 语言 switch 不加上 break 的感觉
    a::
    b::
    c::
    d::
    e::
    f::
    g::
    h::
    i::
    j::
    k::
    l::
    m::
    n::
    o::
    p::
    q::
    r::
    s::
    t::
    u::
    v::
    w::
    x::
    y::
    z::

    ; 记录拼音输入
    srf_all_input := srf_all_input . A_ThisHotkey ; A_ThisHotkey 代表最近执行热键的结果, "." 是字符串连接符

    ; 把拼音输入和匹配结果一起显示到提示框中
    ; tooltip, % srf_all_input . "`n" . %srf_all_input%, A_CaretX + 10 , A_CaretY + 20, 16 ; tooltip 用于显示一个简单的提示框. 注意 "% srf_all_input" 中间的空格必须有, 整体就可以取得变量值. 而 %srf_all_input% 的用法有些奇怪, 等待查询.     A_CaretX 和 A_CaretY 是鼠标光标的坐标, 借助鼠标光标参考点设置提示框的位置. 16 是一个可选参数，用于指定提示框的样式

    ; 分割匹配结果, StringSplit 语法为: StringSplit, 分割结果数组, 待分割字符串, 判断可以开始分割的字符, 将连续分割字符合并成一个字段的特殊分隔符
    ; StringSplit, srf_for_select_array, %srf_all_input%, %A_Space%, %A_Space%

    gosub srf_tooltip

    return
#if

; 定义一个数组选择函数
srf_select(list_num) { ; list_num 是数组序号
    global ; 确保函数内部可以访问到全局变量
    sendinput % srf_for_select_array%list_num% ; 模拟按键输入, 发送分割数组中的字符
    ; tooltip,,,, 16 ; 显示一个全空白的提示框, 四舍五入就是取消提示框的存在
    ; srf_all_input = ; 设置为空字符串, 清空用户之前输入的所有字符串
    ; srf_for_select_arrayθ = ; 设置数组大小为空, 相对于清空数组
    gosub srf_ualue_off
}

; 提炼后的 srf_tooltip 标签
srf_tooltip:
    ; 把拼音输入和匹配结果一起显示到提示框中
    tooltip, % srf_all_input . "`n" . %srf_all_input%, A_CaretX + 10 , A_CaretY + 20, 16 ; tooltip 用于显示一个简单的提示框. 注意 "% srf_all_input" 中间的空格必须有, 整体就可以取得变量值. 而 %srf_all_input% 的用法有些奇怪, 等待查询.     A_CaretX 和 A_CaretY 是鼠标光标的坐标, 借助鼠标光标参考点设置提示框的位置. 16 是一个可选参数，用于指定提示框的样式
    ; 分割匹配结果, StringSplit 语法为: StringSplit, 分割结果数组, 待分割字符串, 判断可以开始分割的字符, 将连续分割字符合并成一个字段的特殊分隔符
    StringSplit, srf_for_select_array, %srf_all_input%, %A_Space%, %A_Space%
    return

; 提炼后的 srf_ualue_off 标签
srf_ualue_off:
    tooltip,,,, 16 ; 显示一个全空白的提示框, 四舍五入就是取消提示框的存在
    srf_all_input = ; 设置为空字符串, 清空用户之前输入的所有字符串
    srf_for_select_arrayθ = ; 设置数组大小为空, 相对于清空数组

; 补充注释

; 打开网页
; ::a::
; run https://blog.csdn.net/m0_73168361?spm=1000.2115.3001.5343
; return

; 运行程序
; ::python_code::
; run "C:\Users\Limou_p350ml9\Desktop\Everything.lnk"
; return

; 发送消息
; ::c::
; send int main(){Enter}{{}{Enter}    return 0;{Enter}{}}
; return


; 在 AutoHotkey 中，`:=` 和 `=` 两者都用于赋值，但它们在一些情况下有细微的区别：
; 1. `:=` 是一种严格的赋值操作符，它会将右侧的值解释为表达式并将其赋给左侧的变量。这意味着它会自动将右侧的值转换为合适的数据类型，并且可以在表达式中使用各种操作符和函数。
;    ```autohotkey
;    x := 10
;    y := x + 5
;    MsgBox % y ; 显示 15
;    ```
; 2. `=` 通常用于设置变量的初始值，它不会对右侧的值进行表达式求值，而是直接将其作为字符串赋给左侧的变量。这意味着右侧的值不会被解释为表达式，而是作为字符串文字。
;    ```autohotkey
;    x = 10
;    y = x + 5
;    MsgBox % y ; 显示 "10 + 5"
;    ```

; 总之，`:=` 是一个更加灵活和严格的赋值操作符，而 `=` 则是一个简单的赋值操作符，通常用于静态赋值。
```

