1.Vim 基础使用
==== ==== ==== ==

1. 安装：在 `Centos 7` 环境下安装 `vim` 可以使用 `sudo yum install vim`，其他的环境可能不太一样。

2. 编辑：`vim` 文件名”创建文件并且点击 `i` 进行编辑，若是没有目标文件则创建，有则进入 `vim` 的编辑状态。

3. 退出：`esc+:+wq` 这里 `w` 是保存，`q` 是退出（加 `!` 是强制执行），连用就是保存文件并且退出 vim。

# 2.Vim 普通模式（Normal Mode）

`vim` 一进入就是普通模式（默认），按 `i` 进入插入模式，按 `:` 进入底行模式（实际上 `vim` 还有其他模式）。

## 2.1.复制粘贴命令

1. 单行复制：`yy` 命令可以复制整行文本，然后使用 `p` 命令即可

2. 多行复制：`数字m+yy` 命令可以复制光标所在行后 `m` 行的文本，然后使用 `p` 命令即可

3. 单字符复制：`vy` 命令只复制光标所在位置的字符，然后使用 `p` 命令即可

4. 多字符复制：`数字m vy` 命令复制光标所在位置后 `m` 个字符，然后使用 `p` 命令即可

## 2.2.剪切粘贴命令

1. 单行剪切：`dd` 命令可以剪切整行文本，然后使用 `p` 命令即可

2. 多行剪切：`数字m+dd` 命令可以剪切光标所在行后 `m` 行的文本，然后使用 `p` 命令即可

3. 单字符剪切：`x` 命令只剪切光标所在位置的字符，然后使用 `p` 命令即可（这个键位可以替代删除操作）如果使用大写就可以向前剪切（代替 `delete` 键）。

4. 多字符剪切：`数字m x` 命令剪切光标所在位置后 `m` 个的字符，然后使用 `p` 命令即可

## 2.3.文本粘贴命令

粘贴命令是 `p` 但是，除了粘贴一次，还可以使用 `数字m + p` 粘贴 `m` 次

## 2.4.撤销回退命令

1. 撤销操作：`u` 命令是“撤销”命令

2. 回退操作：`[ctrl+r]` 是撤销掉撤销命令，即“回退”命令

## 2.5.光标移动命令

1. 文档体首尾：`gg` 直达文本的第一个字符，`G` 直达文本的最后一个字符

2. 文本行首位：`^` 可以定位到文本行的开头，`$` 可以定位到文本行的末尾

3. 行定位：`数字n+G` 可以定位到第 n 行。

4. 单词首尾：`w` 和 `b` 可以一个单词为单位左右移动（`w` 和 `b` 都是单词开头，`e` 是单词结尾）

5. `h、j、k、l` 对应方向键（也可以使用方向键，不过更推荐前者，更加原生态，并且一定适用在很多场景，有些操作使用单独分离出来的方向键可能会失败。）。

> 补充：其实可以选择打开文件的时候就跳转到某一行，例如：`vim main.cpp +7` 打开的时候光标就会定位到第 `7` 行。

## 2.6.大小书写命令

`~` 将光标所在字符大写转小写，小写转大写，使用后会自动向右挪动光标（我们可以自己挪动光标，选择要转化的字符）。

## 2.7.替换文本命令

1. 选择替换：`R` 命令用作替换文本，接下来输入的内容会把光标所在文本替换/覆盖，光标可以被外我们随意挪动（作用类似 `insert` 键，实际上这就是打开了替换模式，这个模式用完后也需要使用 `esc` 退出）

2. 重复替换：`r` 命令只替换一个字符（也可以加上 `数字m`，`m r` 再输入单个字符可以连续替换成相同的字符）

# 3.Vim 插入模式（Insert Mode）

使用 `i` 进入 `vim` 的插入模式就可以编写文本，按 `[esc]` 又回到命令模式。 

> 补充：
> 
> 1. `i` 是在当前光标位置插入字符
> 
> 2. `a` 是在当前光标的下一个位置插入字符
> 
> 3. `o` 是在当前光标的下一空行位置插入字符

```bash
:s 表示substitute，也就是替换， 格式为以下
:[range]s[ubstitute]/{pattern}/{string}/[flags] [count]
range 表示区间 % 用于表示全文,  2,3 表示从第2行开始到第3行
{pattern} 表示字符串匹配规则，要匹配什么样的字符串 ， 比如^a 表示以a字符起始的字符串
{string} 表示要将匹配到的字符串替换为的新的string字符串
[flags]  s_flags中，g比较常用，通常使用g表示全部替换，默认如果不给的话，表示只替换一次
[count]  表示在一行中匹配多少次，很少会用到....
如果想要将文件中所有 nihao 替换为 hello 则命令为：  %s/nihao/hello/g

:p 用于打印指定区间的行
:[range]p[rint] [flags]

:i 在指定行上方添加文本
:{range}i[nsert][!]
```

# 4.Vim 底行模式（Command Line Mode）

使用 `:` 起头输入对于的命令，按 `esc` 回到命令模式。

## 4.1.保存文件

`:wq` 这里不再进行说明，前面有提及。

## 4.2.打开行号

1. `:set nu` 指令调出行号

2. `set nonu` 取消行号。

## 4.3.分屏操作

1. 打开分屏：`:vsplit 文件名` 指令为多屏操作（或者使用 `:vs`）

2. 切换分屏：`[ctrl+ww]` 可以快速切换分屏，`[ctrl+w+方向键]` 也可以达到切屏的效果

3. 取消分屏：`:only` 取消所有分屏（只保留当前窗口），`[ctrl+w+c]` 取消单个分屏（或者使用 `:close`）

4. 分割分屏：`:split` 是水平分割屏幕，`:vsplit` 是竖直分割屏幕。

> 补充：不过我个人不喜欢使用这个，更加喜欢使用 `tmux(终端复用器)`，其分屏操作更加灵活。

## 4.4.编译代码

1. 编译代码：`:!gcc 文件名` 可在 `vim` 内编译代码，然后继续使用 `vim`

2. 运行代码：`:!./a.out` 可以输出代码运行结果，然后继续使用 `vim`

> 注意：这里的 `!` 命令是不退出 `vim` 并且执行后续对应的指令，可以用这个命令来运行命令行指令，和前面的强制执行不同。

## 4.5.查找内容

1. 使用 `:/关键字` 来从上往下查找关键字，按 `n` 继续查找下一个。

2. 使用 `:?关键字` 来从下往上查找关键字，按 `n` 继续查找上一个。

3. 使用 `#` 可以查找和光标当前所处的相同关键字，并且对该关键字进行高亮，按 `n` 逆向查找

# 5.Vim 替换模式（Replace Mode）

就是之前提到的 `R`。

# 6.Vim 视图模式（Visual Mode）

`[ctrl+v]` 就会切换到视图模式，然后移动光标位置，输入 `I` 和 `//`，然后输入 `[esc]` 即可注释，利用这个视图模式可以达到快速注释的目的（注意光标是前闭后开的）。

去除注释在使用 `[ctrl+v]` 然后选中注释的区域，输入 `d` 即可删除。

# 7.Vim 其他模式

另外，如果想要查看自己 `Vim` 的其他模式，可以在底行模式使用 `:help vim-modes`

<img src="./assets/f2a96494-8b59-46c5-a140-b55896c5ee9d.png" title="" alt="f2a96494-8b59-46c5-a140-b55896c5ee9d" style="zoom:50%;">

我的这里有 `7` 种基本模式，不同 `Vim` 版本可能不太一样，剩下的模式您可以去研究一下...

# 8.Vim 相关配置

## 8.1.手动配置

`Vim` 的配置分为两种情况：

1. `vim` 配置选项可在 `vim` 命令行模式下使用 `:set 某配置选项` 激活配置选项（暂时有效）

2. 也可以通过 `set 某配置选项` 的格式保在配置文件中被 `vim` 加载执行（长期有效）

在使用 `vim` 的时候最好是配置好配置文件，而 `vim` 的配置文件一般所在地为：家目录 `~` 下的 `.vimrc` 文件，如果没有可以自己创建。

接下来进行配置，如果之前已经有相关配置了，可以像拷贝一份原有配置，避免配置失败。

`vim` 的配置文件 (例如 `~/.vimrc`) 其实是多个配置选项的集合。每个 `vim` 配置项都有对应的关闭选项：

```bash
set 某配置选项 #即可打开配置选项
set no某配置选项 #即可关闭配置选项
```

例如：`set nu` 表示显示行号，而 `set nonu` 则表示不显示行号。

知道这一配置思想后，相关的配置项有很多，您可以上网搜一下，配置个性化的 `vim`。下面是我的 `vim` 配置，您可以拷贝进您的 `.vimrc` 文件中：

```shell
"""""""""""""""""""""""""""""""""""
    """=>全局配置<="""
    """""""""""""""""""""""""""""""""""
    "关闭vi兼容模式"
    set nocompatible

    "设置历史记录步数"
    set history=1000

    "开启相关插件"
    "侦测文件类型"
    filetype on
    "载入文件类型插件"
    filetype plugin on
    "为特定文件类型载入相关缩进文件"
    filetype indent on

    "当文件在外部被修改时，自动更新该文件"
    set autoread

    "激活鼠标的使用"
    set mouse=a
    set selection=exclusive
    set selectmode=mouse,key

    "保存全局变量"
    set viminfo+=!

    "带有如下符号的单词不要被换行分割"
    set iskeyword+=_,$,@,%,#,-

    "通过使用: commands命令，告诉我们文件的哪一行被改变过"
    set report=0

    "被分割的窗口间显示空白，便于阅读"
    set fillchars=vert:\ ,stl:\ ,stlnc:\

    """""""""""""""""""""""""""""""""
    """=>字体和颜色<="""
    """""""""""""""""""""""""""""""""
    "自动开启语法高亮"
    syntax enable

    "设置字体"
    "set guifont=dejaVu\ Sans\ MONO\ 10
    set guifont=Courier_New:h10:cANSI

    "设置颜色"
    "colorscheme desert

    "高亮显示当前行"
    set cursorline
    hi cursorline guibg=#00ff00
    hi CursorColumn guibg=#00ff00

    "高亮显示普通txt文件（需要txt.vim脚本）"
    au BufRead,BufNewFile *  setfiletype txt

    """""""""""""""""""""""""""""""
    """=>代码折叠功能<="""
    """""""""""""""""""""""""""""""
    "激活折叠功能"
    set foldenable
    "set nofen（这个是关闭折叠功能）"

    "设置按照语法方式折叠（可简写set fdm=XX）"
    "有6种折叠方法：
    "manual   手工定义折叠"
    "indent   更多的缩进表示更高级别的折叠"
    "expr     用表达式来定义折叠"
    "syntax   用语法高亮来定义折叠"
    "diff     对没有更改的文本进行折叠"
    "marker   对文中的标志进行折叠"
    set foldmethod=manual
    "set fdl=0（这个是不选用任何折叠方法）"

    "设置折叠区域的宽度"
    "如果不为0，则在屏幕左侧显示一个折叠标识列
    "分别用“-”和“+”来表示打开和关闭的折叠
    set foldcolumn=0

    "设置折叠层数为3"
    setlocal foldlevel=3

    "设置为自动关闭折叠"
    set foldclose=all

    "用空格键来代替zo和zc快捷键实现开关折叠"
    "zo O-pen a fold (打开折叠)
    "zc C-lose a fold (关闭折叠)
    "zf F-old creation (创建折叠)
    "nnoremap <space> @=((foldclosed(line('.')) < 0) ? 'zc' : 'zo')<CR>

    """""""""""""""""""""""""""""""""""
    """=>文字处理<="""
    """""""""""""""""""""""""""""""""""
    "使用空格来替换Tab"
    set expandtab

    "设置所有的Tab和缩进为4个空格"
    set tabstop=4

    "设定<<和>>命令移动时的宽度为4"
    set shiftwidth=4

    "使得按退格键时可以一次删除4个空格"
    set softtabstop=4
    set smarttab

    "缩进，自动缩进（继承前一行的缩进）"
    "set autoindent 命令打开自动缩进，是下面配置的缩写
    "可使用autoindent命令的简写，即“:set ai”和“:set noai”
    "还可以使用“:set ai sw=4”在一个命令中打开缩进并设置缩进级别
    set ai
    set cindent

    "智能缩进"
    set si

    "自动换行”
    set wrap

    "设置软宽度"
    set sw=4

    "行内替换"
    set gdefault

    """"""""""""""""""""""""""""""""""
    """=>Vim 界面<="""
    """"""""""""""""""""""""""""""""""
    "增强模式中的命令行自动完成操作"
    set wildmenu

    "显示标尺"
    set ruler

    "设置命令行的高度"
    set cmdheight=1

    "显示行数"
    set nu

    "不要图形按钮"
    set go=

    "在执行宏命令时，不进行显示重绘；在宏命令执行完成后，一次性重绘，以便提高性能"
    set lz

    "使回格键（backspace）正常处理indent, eol, start等"
    set backspace=eol,start,indent

    "允许空格键和光标键跨越行边界"
    set whichwrap+=<,>,h,l

    "设置魔术"
    set magic

    "关闭遇到错误时的声音提示"
    "关闭错误信息响铃"
    set noerrorbells

    "关闭使用可视响铃代替呼叫"
    set novisualbell

    "高亮显示匹配的括号([{和}])"
    set showmatch

    "匹配括号高亮的时间（单位是十分之一秒）"
    set mat=2

    "光标移动到buffer的顶部和底部时保持3行距离"
    set scrolloff=3

    "搜索逐字符高亮"
    set hlsearch
    set incsearch

    "搜索时不区分大小写"
    "还可以使用简写（“:set ic”和“:set noic”）"
    set ignorecase

    "用浅色高亮显示当前行"
    autocmd InsertLeave * se nocul
    autocmd InsertEnter * se cul

    "输入的命令显示出来，看的清楚"
    set showcmd

    """"""""""""""""""""""""""""""""""""
    """=>编码设置<="""
    """"""""""""""""""""""""""""""""""""
    "设置编码"
    set encoding=utf-8
    set fencs=utf-8,ucs-bom,shift-jis,gb18030,gbk,gb2312,cp936

    "设置文件编码"
    set fileencodings=utf-8

    "设置终端编码"
    set termencoding=utf-8

    "设置语言编码"
    set langmenu=zh_CN.UTF-8
    set helplang=cn

    """""""""""""""""""""""""""""
    """=>其他设置<="""
    """""""""""""""""""""""""""""
    "开启新行时使用智能自动缩进"
    set smartindent
    set cin
    set showmatch

    "在处理未保存或只读文件的时候，弹出确认"
    set confirm

    "隐藏工具栏"
    set guioptions-=T

    "隐藏菜单栏"
    set guioptions-=m

    "置空错误铃声的终端代码"
    set vb t_vb=

    "显示状态栏（默认值为1，表示无法显示状态栏）"
    set laststatus=2

    "状态行显示的内容"
    set statusline=%F%m%r%h%w\ [FORMAT=%{&ff}]\ [TYPE=%Y]\ [POS=%l,%v][%p%%]\ %{strftime(\"%d/%m/%y\ -\ %H:%M\")}

    "粘贴不换行问题的解决方法"
    set pastetoggle=<F9>

    "设置背景颜色"
    set background=dark

    "文件类型自动检测，代码智能补全"
    set completeopt=longest,preview,menu

    "共享剪切板"
    set clipboard+=unnamed

    "从不备份"
    set nobackup
    set noswapfile

    "自动保存"
    set autowrite

    "显示中文帮助"
    if version >= 603
            set helplang=cn
                set encoding=utf-8
    endif

    "设置高亮相关项"
    highlight Search ctermbg=black ctermfg=white guifg=white guibg=black

    """"""""""""""""""""""""""""""""
    """=>在shell脚本开头自动增加解释器以及作者等版权信息<="""
    """"""""""""""""""""""""""""""""
    "新建.py,.cc,.sh,.java文件，自动插入文件头"
    autocmd BufNewFile *.py,*.cc,*.sh,*.java exec ":call SetTitle()"
    "定义函数SetTitle，自动插入文件头"
    func SetTitle()
        if expand ("%:e") == 'sh'
            call setline(1, "#!/bin/bash ")
            call setline(2, "#Author: bert ")
            call setline(3, "#Blog: https://blog.51cto.com/zpf666 ")
            call setline(4, "#Time: ".strftime("%F %T "))
            call setline(5, "#Name: ".expand("%"))
            call setline(6, "#Version: V1.0 ")
            call setline(7, "#Description: This is a production script.")
        endif
    endfunc
```

如果需要安装插件最好还是创建一个文件夹 `vim`，在里面放置更多的配置文件（包括 `.vimrc`），但是不太推荐使用部分比较冗余的插件。

## 8.2.自动配置

关于 `vim` 的配置，也可以使用下面这个一键安装命令（只适用 `Centos 7`）：`curl -sLf https://gitee.com/HGtz2222/VimForCpp/raw/master/install.sh -o ./install.sh && bash ./install.sh` 然后输入 `root` 密码即可。

 ![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAxkAAAFGCAYAAAAGpFL0AAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAAEnQAABJ0Ad5mH3gAAI45SURBVHhe7Z31ox21uoZpoYJrsXOgQHEvcnGKe3EvB7cWd3eKe/Fi5SDF3bU4FCte3Ivef2Fun/R+i6zsjK01a3ftvd8fnnZPkskaySTfm3xJpllvzrmTftNMm0zTa9qk1//zn//8J5lmmmmEEEIIIYQQojyDZ5gt6TNN38kH00lkCCGEEEIIIZrnoC22T6bvO/Pkgz4SGUIIIYQQQojmWXGBQUmf3tNPPpDIEEIIIYQQQlRAv2l6T/5jMr16S2QIIYQQQgghmqd3r8n/TDeZyf9LZAghhBBCCCGap/fkf6adLDB695LIEEIIIYQQQjRP7369k16T/3DkiIzvv/82+d///cvx4osvRNPEOPTQQ9w5iy22WDR+9dVXd/ESN1OHyy67tPZe4aqrroimaxfKXO+GG27o0my99dbR+J7Gzz//WHtuTz31RDRNCN/6XHPNGY3rDFQ/CCGEEF2SaZK+00yXTDtN/pwMRMYTTzzuGv1ll10mmiZGdxAZK6ywfHL99dcl77zzljPUvvvum+SFF55P9tprz2S66aaLnmNw37/99qu7R+7Vj2tVvjF692b+Tcdw8hkyZIiDvNpdZJS53nYSGfPPP39ywgnHu3cei+8MVl11VVdWPvrow1IiY8CAAdG4zqAr1A9CCCGE6MA0SZ/JIqP3NFMERp7IuOOO/0bjssgTGb169Ur69euXTDvttNH4dmDvvfdyQuC6665NTj/9tOTss89KXnllnLsvwmLnGI888nAyaVJcDLQqX2OLLTZPHnroweSnn35I/v77z+T9999LDjzwgGhaIK92Fxk+edfbTiJjlVVWcdfSDsYyZS5LZMw222zJhRdekHz88QR3zb/88lPy1ltvJJdccnE0fSvpCvWDEEIIIUJ69Z4sMqadLDKKjWS0QmR0BWKjABg9jDpgvA8YMFeHeNhppx2Tr76a6AxhnkEoBlqVL2Ak/vnn78kVV1zuxMb666+fjBx5TvLXX3+4HvUwPZCXREZr6Coig/L3zDNPJZ9++nGy7bbbOnExdOhWyeGHH5aMG/dy9BwhhBBCiHp6T5dM06sXf1QiMgYNWiS5556xzvXn66+/nGzUnpscffRRzrgKRcZjjz3qwo3Y784444wujnwwjLiGfffdJznmmKOTH3/8PnnzzdeTJZZYou6cOeecM7n88suSzz//NPn990nJBx+8l5xyyslJ377sbP5PujQDlOsgPG10wOfWW292aeeZZ54OcbPMMksyceLn7nrPOefswnlCs/nut9++bpRjyJB168KBZ0fc7LPP3iGOvPJERtHnayy00MBk1KirnNGKe9eHH77vesTnnXfeWpr+/fsnJ554QvLss0+7csP1jR//tgujF9vPzyfveu0db7/9dsnJJ5/krvmHH75Lxo69O/psy9wbvf3nnTfS3Q/X++WXE92oEWLOT0e55RrS4Df99J1BlshYccUV3HUNGzbMHeMuZdfIqIKftih9+vRJvvnmq8nP54EOceTJiAnv3g9vVf0ghBBCiM5gsqhg+doqRMYcc8yRfPHFZ841B4Nu+PDhrpHH+MIQCEXGcsstm6y99lrOfSfPiMBAxbXo5ZdfcoYqBsk+++zt8r799jG19P369U1efXWcGwW49NJLnLE9ZsytLg/+9/NuRGRgWC644ILJ8ssvlxx33LFupOCGG67vkA4uuujC5PXXX3U9w3kio8p8p59+eje3A3Hnn2cGIiKFczbaaKO6eCA8y2gv83xhqaWWcteCuw0G+Z57/ic56qgjkzfeeK3uuTNfAUP9xhtvSA44YH83J+Xqq0e5UZdYvkbe9do75vcwWkeMGOFEBNf/5JOP16Ute2/33jvWlcUzzzwj2W23XZPDDjs0ufPOO5Izzji9Lt1KK63kyjn5kRcucRwbGOB++s4gS2Sst9567joZxeDYFxnNwIga5TocnVtttdXc74VufK2oH4QQQgjRaUwWGb0nG58FNuPLExkICxr8rbbashZGbzk9x4SnuUthIOcZEcxX4HjYsN3dMe4/HLPSEL3Nln6PPfZw8ccee0wtDG66abQLZ+KrhTUiMjAgiQMMJgzmWO8uvcEYyExO5jhPZFSZ7w47bD/ZYP/FiQmO6bG/++67XM8875BeXkYLcLmycwzyyjLayzxfwJD944/fnHjyw5nU7hubjFbEXMMw9smX0ZAwDvKu197xCy88V+eaNnr0jS587rnnroWVuTdGXnhPo0ZdWZcW0ibst8JdijLCpOyYUCEuVoYgS2TQWUBHAR0E3DOGexUiA8HA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dx1XJhdddNG6cOr4v//+M9OQjcHz32efvaNQV0IsDrLeHcKKToIQrv/ZZ59Ovv76yygnnHB8ND+DPChLjcC5sTzTwJb46acfXLt0zz1j3d9bb711NG0W1Eu8M/6PxcMpp5ycTJjwQTSuDJQDfmuRRRaOxpdhv/32dWUqzIv39+ijj9SFCdENKCYyVl555eSPP35zBi/HBx98kPvoMA7DtEVJqzBjXHjhBe73aEhieRl8wD///GOUP//83RGLA86N5QmnnnqKqxgwdE1kIDpeeWVcHT/++L3DDytScTzyyMPOSDznnLPdM/ahQaLRD8OBHpZYftddd23y4osvOL7//lvX0NjxYYcdmkw77bSThcJz7j588ir7W265KRk58hzXyB955BFJ796UmWlc/ldeebnrCYwxceIXmQ0dFS6///zzzzohWYYqRqfKghibc845o9x1152OWBxkCblll10mWXvtterAaBw9+sbaMX8T5qcBzo3l6WPl7OabR3d491nQixfLDxAZfD/hfZ522qlOtIThq6yyisuzO4qM+eef35XzFVZYPhpfFoQ53z6dBAbP2upojDxfZJx11pkJo37zzTdfLawsZlBttdWW0fipyfLLL5d8881XtbqE5/z2228mf/31R115Dfnll5+iHWKcT/105513JJ988lGtvrLnh/Dm3NCwNtJ6tukU+uijD2udTvD00086ge+HhXBOVocSnTt0KPCOwToYzjzzjGS77baLihbaI/6P5WcwihkTs8Y777yV/Pbbr8kll1zcIY5zY3mG0OacffZZrh2lPaXtAOoJwii7pImdGyMmMh588P66Np2OMPL2w2CnnXasyyuPqkQGdf9rr73i7Jj111/fiSADsUX77IcBnYGxvIToIhQTGYAxh5HO8OYRRxzuPrrOcGFZY43VnRGL4YaLTiyNgSD57rtvXMXjQyVMxRKGG5zDubE8DUQIjRmNEPe+xRabu2flYyMufhhDtrH8DHrJeK64ACCmfIPi9tvHuN8CDEM/DtJE3m677epEANAIv/TSi7VjXCyo0MnzoIMOdAYq4RwjJmP5hSBcuF475v1kjVTQm1REZOC6NdNMMyULLrhgIaaWyw0ijnuOQWMMsTjg3Fiehv/u4MsvJyaPPfZo7Zi/CfPTcE4sLx+/nPHcGJ3woeyNH/92h3DIEkYmMsLwY489JnnvvXc7hONmyLvujiLDBBTffSy+LHxTdFT4YXxLlj+GromMQYMWSX799WdnCPrps8CIoTz5PPnk4+4e+J0w7rbbbonm0xlQj5qROHjw4OT8889zHV+43OAuRT2+9957uW+Mv6nL6PhAJOBWE8uT3nQMUeozg3vnG2GEhM4UetuvuuqKOhDrpIu5HQJtAAY4f2M4Iz7pVOP7wxD+n//5n1paRi6oyzG4OSdLZBgY/XRIheEbbbRRXYcA7TNGNiNifroyLLPMMk7QHHXUkdH4Iiy55JKunPLuYh1ZjG4QRxrShvE+Sy+9tHtvjz/+mHsH/M8x7RjnIwyt44W/EWF2vMMO27tzGPmJ5Z1GVSKDd00+uBnT9lpH5Icfvu/CaastzNh1112ieQnRRSguMujd4WOj0qRx4qNIq2SrAMPmwAMPcAYblXCR30Io0Mj4YVSSXCuiwA/34Zw8kQEMp1ftLsXvUonHBAMGAwYlv4ebQBhfhNBdioaY/BBeFjZmzG2uQrPjPFopMuh14+8iYEzE8ms1uJQwijJo0CBnWBqUD4wS4G8/jrScw7mxPA16sxCFNlqFAMYdxI75mzA7Ji3nxPLy8csZDS7lwodRBxr6MBxXwVh+hkTGP/CeubeqRQZ1L8YndU9MZND58uqr45LPP/90spicq0M+aVAWGRWzspQF5ezTTz+O5tNK+G4oh4gFDHXKFIYqoxDUif369a2J4Cmjpp+7vzGwMcSzDGzy5Rn6YVa3nXTSickXX3zmRLYfD4h6OpzCcMNEBqMndIZYeV9ooYFujgBiY+jQoS4MYYgbI+cVERmUBfKLGcrUw+ee+4/4oD4lbaNzVXCZe+utN5LXX3+11CiDwfkIPIQcc1uyBARxpCHt6aefluquR1vBfFBj1Kir3D0y8sa3gG1iafmb78WOeb4ItKxOkxhViAy+0XffHe+uMYzjOinflOUwToguTr7IoAfjueeeqYPGjI8uDPdptJeZIeh///vfbkgRowjjqGilwCjL9ttvVxdGzxONY1YlyTl5E9DoGcNViGF17r0KkcE10WjGGhYaRxoyhlURWuEQL8/IP/ZBSNh7wCD96quJteP999/PjUrZkP9NN01xnWEYl2OG3WN50oOEMQmk593w94knnuAatwsuOL/OqPahsS4qMnjXCEp6/yhD/O1DbxQV8uWXX+aOY/m1GowzDJSNN96orteJZ8ckfeBvP460nFNEZPgNJY2i3xPL34TZMWnzREZYzugVphfYd6t4+OGHnIHmh1EmGEEL8/NBZFBOrXwZfCeUkTCcZ8G7rlpk2HPGoGIEkHJPOXn55ZeSeeaZp5YOly3KDnUYBg3iimcYc30pmpb3wT2lQT5++qKYyFh44YVcPozyxUTGXnvt6QzXNddco0MeWVAWi9ZX3PfUEBm4AXKfZiibyLB46neeAXWFiQyMtffff889F0sXI01ksNhGlpsR+SIOYnGAAGC0hWvFYObd+eUdoY3IIA8WW0CI0AFBGs7z8wrhe8NlLGxfmY/C7/ijmrvssrP7Bs2ttQzkT2cJefKcYmmyYGSNHnrc/XheuOlSnrMgDW0X53AuecTy9qEThPYHMZgnMnDN2nLLLWrHRTGRccghI1zdGVLEtdC8BxA6LHzht4+MYOAK54f5xPIToouQLzIwlvyeA6BR5cMOw4HhYD6mRldioPeJ8/nwsnzBYzB5ikrFB2MKYyMMD+HcWJ6ICyo8GvGHHnqgNhJAxcNEaN+AsiF3PyyrJ5gKirxCkUHlSkWLSwDHGEr+yANC4YcfvkudeE/lSjy9ybwrzudvngW9dNabjIFhrlfAb6a5FtAzSCPIb3Muc0U4pvFvduK3LzI4poEbP/4d57qBuCEMwwGfXt4DLkL++Z2NiQzcNPzGhl5XExn87cfZCjhTQ2Tw+3454xjjw0/Dt8s354dxvUVEBm4lGH4+iFfEZRiOmwDX0gqRQZlBCOBCgxFH58G1117j3gVpKEP0+ONCgnGHCyTviuvhfz+/MmlXWmklNzpEGuIpp+aiAaFBWBS+Ge7Lvg++Qeoq6mjc37gO5jFhtPA8/dW7wtVrYnQFkRFiIoNngTsUvuw8f+L8kQxcU6gDEQz++T5pIoM8qTOZ88bzpYPAT0MZRsT6YSF0qiAGmEfil3fKFW0c9RjPlB7utdZa033TtJ15HWrM7WBkKQxHYPI7/J6FIXAYifDTFYFr4LnQHlC385z4nviuuP7YOSEY/dQpNhnfOuDyIC2jcZyb91u8HzoA7DkiMhh14dqBv+kIs2Og7ovllYWJDObMUP5CaKdi5xl0GPK+yQORQccpfxdlanWmCVEB+SIjBh8VBncsjiFtPoxGG1YqYCq3RlY32WCDDVxPhUEFS8Pohxn4a1IB0QvBMefG8sSQZ3KbVZa+uxQCBKGB4YdBE8MM5xB6JentpfLxRQYjKoQjUKxyYZQANxn+poHlnCw/fN8goIEwdykMS19kYADxt42SYNCkiQyDVV2s8ivr25oGrkXk57uE4RZAo0APMhUzDQauHTTI/rlTA5urQK8j12bQ0GPUAn/7caS182J5GggGRK1NbDeBbMf8TZgdkzZLZMTKWUxkcI0YVn5YUZHBdxSGd7a7FGWXfPndWDzsscce0TQ2mmerNpVNa9DrSFyROrQIJjKoE8iXOtVEhhktaXBeLE8fRAYLUzBqmAcdHu0iMhhRZhI19btvVCMGMDrtmPl8GPqILgvzSRMZ9Crb0ty0E7i5+AYvcxIR0HYcwqIgjO5R1/ruUrQZXDMjy5tssrFLa++WcxDrWaMv1I+kpYOPcgDWOUbnF51I/nUyoZ25jHZcBMqYTXinjPOMgLqe30V0UR5i53Y2iC3eN52RXJu5I9GGhTCSQFtC2xjLKwsTGY24SzGKyvum/aYDhHoWcWTzCunAwFOBzjQLC4nlK0QXobzIoBKip2fkyHNd7yyGqh/P+XyQjQzRAiLDeqOaAWN8SuU9NBqPQcXyqLG4LLh/DA+rcDDgizToIYxMIKYY7jfjj4l7NIpcN+GW1iabY3TQkNDIWVwMKlIaQSoo3GDo8eRvKrNQZOASYM+7iMgwY47KHOOWPTj4vwgItFieNBLkaaMWPogKetjpUaayD+OnBhjWYSNWFM6N5WngHueLVFtZjXP9cB/OieUFsXKGyKAsIIANDAnerR9GZ0KeyGBtd3qMw/CpITL4NujhjsUD98Jvh0YnBhTh5htfNq3RKpGBIU35J8xEBntZMBkbo5VlpPldes+tE4XJpWF+IYgMzitKu4gMyjz1ut+RRW8xHV/US376rCWjKfMY09yXwXOmjrQ0vH/Ktz8Sy3wNDEY7DqHs8L3yNyO9PDvctxAeGMY2yoQgYLSBb5G/+WYwltPKMHt5kAduVpQBfsfeCe0xLl60nwar+lF++JsVIcP8QuiQ4BmSj7nemcjgb9oQ8mOeIALOPzcGk+fNUC4L58byNNhXgufKQhqMgDDXhW+BZ4gQ2myzTevS45HBuw2XKS5CMyKDd4nbKcuGU/8iMiyOssn7zqtjhejClBcZ9PjzwfERM1GLj8Rfn5wGj4rQP6cMVYgMelqoCK0HP4RJ4NxDnv+rQSXhN0Q+NFL0kMTigHNjedJjRUWJIKDyOfnkk1wlyKgFFb0vMmhMec4816yldg3y5P5ixEQG18l5NCDWOMbgefFuaIQY3WH4mJEcc4UxeCb0EIbhvlHgw2orXA8Gq3+tRUC8xPJsJcxTsgmxPrgT8A5ZHjgWD1mrtNB7jjHjw/NGeIbhIZwbyzMsZ4RhoJGvDwY6xosfxmhHXgNo/uuUJR8afXqbw3BWS+G9tUJkMKoTizNwNQlHcIDOEq7J/+bKpDVaJTIw+HjGhJnI4G++MTOqGeXDTcvOLQI+94yG8S6KULS+bCV0DMVGMzEuGf0tc40Yz3TC0IljMCrniwxgyVV/EjiTq+1bioG7EeWEb53edcoEUH8jWMwli7qCb872/MDF2Fy/Qpg0Th6MknI+YbjdWN2NyAg7dQzKDoLUzy+EkRtGi/nu/WfoiwzAw4CFQhh1yZuLgMuT3XtZODeWJzAvj/rK0lLPYLDzjhjR4bugDrb0vA/ujblafj5FaUZkgK2KGYoMG8Hn/fj1rk+ey5gQbU55kcEQLAYvhZ+Gh48Ew9jiMRSzJsXl0azIYCgSw57rYuWJIUOG1Bm3uD/RIJdZjpFGnnwMJtThq0qlhgFHLxVuB34aGioaJ+sRCrE5K2b8sQeHrYBFpR4aMQz30qCkGeo+9AJRIWKk8BwwJDgGeqt8kUEPF0uXcl6eyGBEhHdNI8f9IShp9MJ0xJeZN8EShlwPPViMaoQgumgs/TCum0Y6a0J5q6DR4v5D6N3mPnAticVDls81Q+f0LgPGI3nRY2phPpQ9DBU75txYnmE5s3C+A0Y5KA8c883RQ2vxQDnMExkYN/TE0sPsgzBCfIfhCFPui7ojll+jUHYhFmdMEQ6/dAhPFxnF0hqtEhl8H4gLwtJEBiNe1Gtl5sKRN65Hsbh2he/Hv0cMdEY2/DRFoXzH3KX4Tv0wJu4jPux3ePdpc/gYwacn3b5d6idGM8iDCfz0qAN/0zmXVR+EMGpMZ01MZGTBiCR1RCwOcL3l/igP4YIiocgA7pEFEXDZw6j343yoWxgJ8mH5X64nDAfaJfvb6qUQm89gcz9ZgZHOAEbuOGaED8GEoLPlX2mLqKPMBa4szYoMIxQZwKgyo9s+zPmh/kQ8NbKqlxBtRDmRQQ8SH5tvjNB7xCQ4U+v0bCNCLL4szYoMKmIm0WH8449KBcQHy8fNxjZUcPiol1nqEZgfwR4TNEpUWPTm2MRr3JwweOkJ45jeKCp/1vDOm7QVGn8QExnzzjuvM3ow7P3wNOhxsiF63hmjOvaOfJHBSlNmqNDIpIkM7ol7xC/YGmIqQAh763k+lAs/LK2nHRBYuPTE4gBx4/fE8bwZLaG33E+XBQ06jUTZ9x6Dd8rzw8/WGmHKGEaFHWNM44trx6TlnCKT+GjoKcMI2dj1MqmVBh6DI4xLIyxnGD2UUToN6Lnk2kIXmylzSLKNVs6nNzgMnxruUhCLMxBM/DZGjB9elbsUc7SIq1pk4FKDcUZYmsigV7ToSCdgqGNYNrP/QWfCPWMk0mPNfdr8Lb4F6viY0CCMuiUMN/gui4gMQFza39T99947ti7eh7aH94CYCOP8Cc4+iMQieyLERAZtg7nJheCug2twTBQDcwZoV5hEb+2Dj9VfYThQp1CG/NXbsmABBtqQmAsz90Ub4HtFxKATk1WeWL6cb43/adtZOYpj3JJIh41CJwztDnWljRY1QqtEBnWwX64MvnfKOHVNGCdEF6O4yOCDwHCit8z3radX2a9kGObEuLXjslThLuWrf0Y2qLz5wKkoACFUtAeJipFRD4xIxBMGM3mEjRq9wlS4NBYM5WLU503yhaIiA/gNBB2NShgXYpurMQrA0DEVOL1v+AObocfkQ541vUE8syyRQdq7777L/e03xPRqcQ8+NPoYRX4YxoCfnw9+tWk9g+CLDAxhKmAq6zLzfuy9pbnQlcFEhr9SCdfnD/Hze4wg2TFpOSdPZDDZkndCY4xPNbus07vFUp4YAbwfRBxhsfPT4B2E5QxDjfLE+2T0r0yvKvD8GVGJuTa0o8goM5m7TFrD7i0mQBqBuodRUd4NRhMjlIyWWH3riww7po6y5V6zIF+u1TpGqoZV9RBqWd99UdgTg1Ex/kdkcN3+5GM6lnA5CQ02OoX4VvzJ4T5lRIYPHVV5m2ryTdjok0+ayCBtlouQERMZhPFMYmAYkzfff5iXkWU8Z4kMYPQ5Fh6D0VZsiFjvPJ0Z1BeInTAuhi8yOMYwpy6yeH4Dd27S0G6Vrdt8WiUy8E6gnmdUxr5DcxkuK/6r/N6EqJBiIoPlElnOkQrb3600BsOyef6fWSAy/MqiWeiBpyHBMKX3CXcfem744BEKeZUPDTuVIyuX0Ptnq0uFIoOGHdcR4tjjo0iPNcSMPxMZ5MEcmKuvHuXmTjA6QUVMI5flFmErfNETQl4Y1vRW4w/M++MeaJgwgnmnGMCM7jAvJGu/EDNu8hpi4su4S3FPvPdYHPgig/fFkHiYJg8aL54Jxn8svgytFBkYTzQYuAvwrujNRihyrvkhM/cjzZ0gjVg5QwTbrvLm+le03IK5KMRWZmtHkYEh4y9Ly6ggHQhcD73TjaY1MGxwcUAc4kZifv5FXBzTYFI/v0lZoKPD31TUFxmIHuppfp+eazqFLF2MKaO8v5QuR0Wh3uG6s4zbIlDnMNfG3LpiIgN4FjZy4NeNjHamuQpxjXRu0OYZuPrmiQw62ujwicUZrRQZtGV8y3yziAzE/pRRx47wPVPHp3Ue5sEzglhcGejc4tmmLelOBwpuQtwbi07E0vj4IoP7p2PT3LY55nfMi4EONuZxhHkUpVUig2+PES9GrcmfTkH+D91Wi1DV9yZExeSLDBpJlonjg+Vj8+OAkQIbZqVBoCfN33U0DxpzemnZG4LJbaygkTeBMwt6+blOGiX7eJkv4V87aaiQqHzx9U+bNxGDxps8MTxoaDAAqcjoaaMixX+aZ8BKFwgRKvpYPkZo/LF0Kz0w9PgweR2DHZFhvXH05uEqhOEdExo8Q4xRBAPHVD7We28jIIgP7pthZJavxQii4SNffxnZNExk8N4xFkMYpub5hOEx1x/ui+eZ1aOKaOM5x+KKggGKwcjIWyy+DM2IjFgvXogJDOadmDHIBFIEB+Uaw4IwvhVcIooIA7+csbsuIoB8eJdcG+WF94aBzCgY5ZhGNWspacoWhk7sntJEBt86zwHXojCuGYqIDOD7xZ+c6+abpa5h7lTMLaxMWoORBuZzYFBxn5Bn8KdBfcJv2jKkPFNc8DC0OcawRggx2sUoKuG4hPIdc17aCj308HNdRYzaRqnK6KEcIuqsjLFjPfmaW4xhnUW0QdSflh5RixtjrB7mGv2NSg1bXtbgO6ATieeJAczv5wkRni3fE/WADwIFwnDSFhUZfPuMYvD98w3H0hm27G2j3xvPCGJxeVAv4cbHM+PeuA566Bnpw02XNoz5K4zcEkf9TDlG+OV1/vkiwxaToB1nBBLXVPKhY4A2krqNNpE2hA6qtDoYg586NgShSv6MlsTioYibYigyDEQRAplVFPkdOhFpx8N0WVT1vQlRMdkiA8OQgkuDlbZqhxlPDFdbJVFmmTgaC873waiOpc2DSoXzqbAfeeRh9/HbRlwxaDzo5eIchtxjaYDGHsMBY8Pyp7eQieVULoyW+IYHPYoYahhwuKNkPQ/f+GMFDPKnVwZhwOodMYOGOQkYMWnLCNL7aZU0lY/vIsQKNPwGIsNveBjd4PdpZC0sDRMZPFved1HCXcuBnll6X0NDmV4dGmN6+ChXtjFhI/D+yOPWW2+OxpeljMhg4ifvE8GIQWPxMTBuEKhWxsiTXi3Kl9/o8jcNKi4blAMa6TSXEMPKmY0k8GwxmCmrlgYDjhEzRB3vmHQQy5vlholL2xXZFxmMalG2cNvjd+mtDOc6iI4wSkEHj40gUhdgVJohYcYPmxD64hkhlzZhn7KDMcPIZaPiZ2rC9fON0CmCmyfGKnUt9QNgYNKp5J+TJsKpG0N3qRh8h/YtAN+m/93EoC5g5JgR2iKQtqjI4Nvkb77XmLsSQpfng1FNZxUiq0hHRIxmRAajTTwvOvOo/yiniIoHHrjP1V0Y/9gbuO7RFiMIaFMQ9HkizhcZPDeENu+SUQvKQfhc+D5YpIT6Mk0QUG6ss6IsYZmLkSYyDN4RHUuUL/KMpRGii5EtMoAlMLN6MxnJYCiSyVi46ZQ1Hmg0MAIRG6j3Zho+epowougZiMWngZjKcmmg54PeEnoJ8xoXHybd0SjE4gzcsGzfC9Z6N4MiD0Z+YuEh9Hj575SGKU0wsuJJkR4UenQwfGNxjRATgkxKR2ThY0oj1GgjCUw0xLBtdHWREMosz5TnZWE0kP418jdliveEeKAHLe/ZspAAo0z0mhYtw/ReszoM1xSLN/xyVrTs8E2GAtqHHvG062SUyxZG4LnTU4fhwHdUxWhST4BvIJxfQceCjVAwMsiEVhtJLgqdHlV+v50NPeN0liAyGDFltAkXQlwiEfVF2xAWWAjdrmLQAUNaRgAR3EUWj6DOOfHEE6JxMUgbmxAdQp1CHR6LM2ij6DxjpIPOjmbcfChvRfbEiEGdlLVvTRqs3sizjsUZ2BmIC/7nmXDPeAHkeQ5QJ1m91NnQOeB3TKWBQMJjIRYnRBfjH3GRJjKEEEIIIYQQogQSGUIIIYQQQohKkcgQQgghhBBCVIpEhqgG/GKz5rUIIYQQQogeQ77IYNIbE3D9MB8mlDLZLhaXBys6cX4sjgnlBx54QKFJdmLqk7b+uxBCCCGE6HHkiwwEBMt/ssIPxyxZSZjxxBOPu+Vr/TCI7akRwk627HoZi2O5OZaoa2TdZ1YCYfnMGFrZpnFY6Yn3H+PNN193xOIgaydSViVjFawQVlpizXSWrIxBOYvlJ4QQQgghpir5IoPlK9lYjn0LWJaTXa1ZHs8gnM2B/DDAsPTzMVjqkw3EMDoRLyxDx7FtFAfHHHO0Wyub5RlZ13r06Bszl9ENYRQkNHLZOA/RwhrisXO6IyzPiCFe1XJ4rPPNuus2agHsH8I+F35YCOfYXiAxXnjhebfXAxs+An8D74ylT8N3CQhb/o/lJ4QQQgghpir5IgNYv5sdh1m/Gb97RgMM1iZnl2A/DNLW7WcNc4z9ENtPgs3H2NNg2LDd3TF7U9BrjZHKPhJ+XmVgrW9+Z5111o7Gd0cQVNxzVfNs/M2E2DsEEcMoBDuHP/jg/W6XakuLiGQTQPZR4JwskWGwQzsbU4XhbD5ImbJj1l6fMrrWeHkQQgghhBAto5jIQDAwAsHftotnHlmb4hDH6AQ7APM3sKkYu9Ry7ksvveh2DDYQMggP4u65Z2wy11zlN+xjXgnnZ80v6W60SmSwmRGCk7zZyJBN5th9GLGBICWMEajjjz/OnVdEZLCREvnhIhXGITLPPfcf8cFmXKQNNyoTQgghhBBtQbrIwE2K3SkNf5do3KaMU089Jfn004/rwsDSxsD1ia3z2Tna3JduueWm5IsvPnOuU88990wHGOE47LBD3d95+cc48sgjnGFKHrH4qnnllXEODOHbbx+TfPfdN8kvv/yUvPzyS3W7ejOyw461n3/+qXMR++CD99w8FBN1PkXTMiLAvabR6K7qCADKAXNbeHfkhaCweHZbR2QgMNiJFyHy0EMPujRpu4wbBxywf/LNN191WKEKQcnvsKu1hbEjLG5VZXd2F0IIIYQQnUK6yJh99tlrvvEYtPRG7777bh0mUn/77deuBzsMhzRjFlcoRib23PM/zoAkbNCgRZKVV17ZGaqGP0+jf//+zlhF/FhYGe69d6z7rRtvvCEaXzUIjPHj33FC4IorLnfG9/bbb+cmtA8aNMil4V5efXWcc/3BKN9vv32TMWNuddfJ/35+ZdKutNJKydprr+XSEH/22We5Y6OZpWaZ44EYWH755VzeJjK4PlydKAsIH97XWmut6QQPrlRp7nMGczsuu+zSDuFrrrmG+x1+z8IQOMwT8tPlMXbs3S6fRhYSEEIIIYQQpUgXGT5PPfWEExmLLLKwmztRlJggwNhkFaI777zDrViF4WdxuNRwbGA885uHHnqIEx2E4a7l51cElsFFKDEKwvyRzugBR2Rwvbh7xeJhjz32iKa56aYpbmOrrrpqQ2mNqt2lhg7dyolDxKbvLsVKYhj9X301Mdlkk41d2iWWWMLFcw4LA+y1154d8jNWXHEFlxa3qAkTPnCwqhRxe++9V/LHH7/VlSXKzl133Vk7LoJEhhBCCCFEp1FOZPD33HPP7Xqss9h333065GHg6vTzzz86wxHDH8MP1xd+F5HB6AZzNOitRmQgRBgVaUZkYJgz4rLooos6I7kzJn8jMrhHJinH4sHmoPBM/XDckgi3OQ1l0xpViwyu4YwzTnd/4wZG3u+//557poxCzDHHHC4OQcD7++23X93fPH8ERNqz+O9/b3d54GbFtfI7uOARx2pmrCTlly+EIs+Xvw8++KAO+cU45JARyQ03XO/EbyxeCCGEEEJURnmRsdRSSznjcsyY21xYCC5Cjz32aIc8fHB9oQecvRDIi78xnhEZuNawrO3zzz9bicjAp/+HH76rGeEsr0tPeJiuajCCP/zw/WicgYsQAisMHzhwoLvXq666ohZWJq1RtchgFIrfO+qoI91qY+QNCASe8ffff+vm2uDShWBgCWLOY84ILlxhfsCkcfLAjYzzCaNc+CLjxRdfiIL4yCtrQgghhBCi02lcZBx++GHOTSaEzfmKGn4xd6lnnnnK9Ti//vqrlYiM66671k2Utl505iRgBFe1d0QaiAyIxRlThMMvHcLTRUaxtEaVIgMXM4Qf+THPhLkZjGYw72bhhRdy+1kAfyPs8uZg+LDzOyMMMZGRxZNPPu5EYyxOCCGEEEJMNRoXGRiaiIIQDMU8kTHjjDO61YaYkExejH6st9567vwq3aV22mlHd47NEzDuu+8e1xPOXg9+eJUUERmtdpdiIj1xVY1kMGGfyeSIiTDONt4Lwy+88IJk11136RAeEhMZTPxntbEYiEZW6oqJKyGEEEIIMVUpLjLwuWeVJEYCMFwRBYiFEIRBlshgTgZuP+RhnHbaqa5XHJHhhzcjMph3Qc8/rldh3L/+9S/najNy5Dkd4qqiiMho9cRve2YxAdIoPE8mZofhaSKDtLF3EBITGYRx/TFYEIC8i07kZuQNocYmgbF4IYQQQghRGcVFBr72n332iTNmY0afT95IBpN1N998s2SHHbZ36S0ckXHMMUe70RL2g0BksAwqPdZlRMZmm23qrvfWW29OXUnKRjmOOOLwaHyzFBEZTIr2l6Vlwvxtt93iros5L42mNRip4Z0xUZpVmjbccENHM0vYtlJkMLeDMsB+KYgM3p1t1hiCWGViedFRGq0uJYQQQgjRaRQTGQ8//JAzADfeeKOauxQuSPjjh2DMPfroIx3yiBGbk8HyqPzN6AYiw+KWWWYZl5adoS0sBBeaM888wxnio0ZdFd3Qzocec9Kyo/hMM80UTdMoRUQG8MwQVBjWrLbFZHE2OPSXbG0krcG8DOZzMBGb5wfkE0tbBAQDAo6RLZ+PP57gCMNJW1RkMPLEO2G1qbw5GbbsLS5hsfgQiQwhhBBCiE6jmMjAvciMfxMZtgmbcf311yWPPPKwMxSvvnpUXVwaaSKDFYXogceff8CAAW7+BHEsCctu4X4ePvSks1Ec8zpi8THYJI/lbR944L5ovKgHwcBKUv6SslmQtqjIMHepWWaZxe3DEaZBMPGOH3rogeSjjz5084Ia2f1dCCGEEEK0lGIiw4eRhGuuudrt0O2Hb7HF5skll1zseopZXciPS4MN28jLjjkX8YIrE25T1uN+0EEHuh77rbbaspY2Bn767FQei8uC/R04NxYn6kGUnXjiCdG4GKTlnFicDy5ciItYnIHgZDUpRjoYJdE7E0IIIYRoS8qLDCGEEEIIIYTIQCJDCCGEEEIIUSkSGd0N5rgUJXa+EEIIIYQQTSKR0d2IiYk0YucLIYQQQgjRJBIZ3Y2YmEgjdr4QQgghhBBNIpHRWRx66CHOsGdTwVh8VYRCIovY+UIIIYQQQjRJtsiwfSwMNq777rtv3H4YW265RS2dyEciQwghhBBC9BCKiQw2QGMn5kMOGZFcfPFFyY8/fu/Chw0bVksrspHIEEIIIYQQPYRiIuOEE46vhcFyyy3rRjXef/+9unCRjkSGEEIIIYToITQmMuCjjz50QqN3b86bJunfv7/b3fnZZ59Ovv76y2TSpF+T8ePfdmH9+vXrcP5ss82WnHfeyOTDD993ab/8cmLy0EMPJuuvv35DaT/55KPk7rvvqjsvhPMffPD+2jE7il9++WXJ559/mvz++6Tkgw/ec7uO9+3bt+4845VXxjkWWGCB5PbbxzjXsV9++Sl5+eWXknnmmaeWjt3Q77lnbPLzzz+6ZzFy5LnJ0Ucf5Z6lRIYQQgghhOjmNCYyZplllsnG/i/Jt99+XQubf/75nQC48cYbkgMO2D/Za689k6uvHpX89dcfyZgxt9adD/feOzb57bdfkzPPPCPZbbddk8MOOzS58847kjPOOL2htDffPDqZOPHzuvN85pprTncvxx9/nDvu169v8uqr45xQuvTSS5L99tvXXSdpYtcLCIzx499xYuSKKy5Phg4dmmy//XbJtddeM1lYDHJp5phjjuSLLz5Lfvrph+Tkk09Khg8fnrz55utOGJF3msgYO/ZuF4/IicUXhTyKEjtfCCGEEEKIJikmMhhFWHDBBZOFF14oWW+99ZInnnjchV9yycW1tIxWDBgwV+3YwIAn7UILDayFMerx55+/J6NGXVmXFqabbrq646JpDzzwAPc7jDJwjIiYe+65ayMtm2yysYsfMmRdd7zHHnu442OPPaaWB9x002gXvuqqq9aFAyIjdo4PwoI0W221ZS1s9tlnT3744TsXLpEhhBBCCCG6OcVERghGP733CAAvszpwOUJ4MPLAORtssEEtbtppp01+/fXnZNy4lycLkwF154UUTbvCCsu739l2223d8YgRI9zxOuus7Y5x2+K6Z5xxRnfMyAfxCBHLA9Zddx0XbiMePoiMP/74LZlhhhk6xBnPPfeMc6MycWPwvMg3TWQwqf6GG65Ptt5662h8UfiNosTOF0IIIYQQokmKiQwMcpas3XzzzZK1117L9cx7mdTYcccdkmeeecqJgtCg3WKLzevS7rvvPm4eBIb/Sy+96OZGbLzxRnVpyqRFjOCidM45Z7vj++67J/nmm69qLlUcc66lf/rpJ517lx0bAwcOdNd71VVXdIhDZDCvIwz3+eyzT5I33nitQzgih3w1J0MIIYQQQnRziomM2MTvkGHDdndpmZDNPIXVV189WWWVVWoTnmP7asw333xu7sb111/nBAHpTjvt1A7piqZ9+OGHkieffNy5SjEhGxcqhAFxTMC+4ILza2mniIxfasdGnsiw/NKQyBBCCCGEED2c6kTG448/5gzscE7FEUcc7vLI27wPNyZcohAHvXr1iqYx0tKedNKJLoxVp955561k+umnd0JitdVWc9dgrlTQqLtUnshgda1G3KWqgt8oSux8IYQQQgghmqQ6kfHYY4+6ZWRxW7Iw5i6wEhN5+CKD+RqxeQ133XWnW0XKz6NMWuZ98FtsHsiysYSxZC2uUoQzGmJpG534nScybMSi7MTvww8/zAkfXwg1Ar9RlNj5QgghhBBCNEl1IuOggw50aVluFgMeo5nlXlkmlnBfZLBSFbuGs8QtO4njasXfpON/P98yaVlalyVziWMVLMJYQpbjCRM+qEsbLmHLvI/bbrvFpR0z5ra6tEYRkeEvYcvICtfMErYTJ37h8k4TGVpdSgghhBBCdBOqExm4LSEs3n13vJtQzQgG8zHYS4I8fJEx00wzuWVxMfLp4WfTOuYx4FqF8e/nWyYtkA6XKYvDqOf32b8jTGub8SEKmFjOpO5TTz0lmi8UERnAnhmILa6j6GZ8EhlCCCGEEKKbwHwGo6PIEF2PmJhII3a+EEIIIYQQTWJ/SGR0F2JiIo3Y+UIIIYQQQjSJ/SGR0V2IiYk0YucLIYQQQgjRJPaHREZ3ISYm0oidL4QQQgghRJPYHxIZQgghhBBCiEqwPyQyhBBCCCGEEJVgf0hkCCGEEEIIISrB/pDIEEIIIYQQQlSC/SGRIYQQQgghhKgE+0MiQwghhBBCCFEJ9odEhhBCCCGEEKJZpkn+D4RlIWWT9/2gAAAAAElFTkSuQmCC)

而卸载的命令是 `bash ~/.VimForCpp/uninstall.sh`。（这个配置文档可以查看一下）

但是这个脚本有一些限制（只支持 `CentOS 7`），您可以查找一下其他的脚本，或者配置文件。

另外如果您不适应这个脚本下 `Vim` 的缩减（默认两格），则可以在家目录下使用 `vim .vimrc` 修改下面的代码。

![2eb150b7-c897-4a9b-8bf4-9f19f01033ad](./assets/2eb150b7-c897-4a9b-8bf4-9f19f01033ad.png)

