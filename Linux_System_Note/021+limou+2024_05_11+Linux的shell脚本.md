>   前要：关于 `Shell` 脚本我参考的学习资料是[阮一峰《Bash 脚本教程》](https://wangdoc.com/bash/intro)，您可以前去一看...这里我不再“照搬” ta 的一些笔记，主要是我前面已经有关于 `bash` 解释器的使用教程了，这里我只提炼出关于脚本的部分，其他内容我不会加入...

# 1.Shell 的含义

`Shell(外壳)` 与 `kernel(内核)` 相对应，比喻内核的最外层，用户跟内核交互的对话界面。对 `Shell` 的不同使用会产生不同的理解：

-   `Shell` 是程序，提供一个与用户对话的环境。接收到用户输入的命令，将命令送入操作系统执行，并将结果返回给用户。
-   `Shell` 是命令解释器，解释用户输入的命令。支持变量、条件判断、循环操作等语法，所以用户可以用 `Shell` 命令写出各种小程序，又称为 `script(脚本)`。这些脚本都通过 `Shell` 的解释执行，而不通过编译。
-   `Shell` 是一个工具箱，提供了各种小工具，供用户方便地使用操作系统的功能。

# 2.Shell 的种类

历史上主要的 `Shell` 有下面这些：

*   `Bourne Shell（sh）`
*   `Bourne Again shell（bash）`
*   `C Shell（csh）`
*   `TENEX C Shell（tcsh）`
*   `Korn shell（ksh）`
*   `Z Shell（zsh）`
*   `Friendly Interactive Shell（fish）`

`Bash` 是目前最常用的 `Shell`，本系列文章采用的都是 `bash` 作为解释器来展开脚本代码...

>   补充：可以理解为 `Bash` 是 `Shell` 这个概念的一个具体实现的软件，因此 `Shell` 对应的软件实现有很多种，您也可以尝试自己写一个 `shell` 实现，在我的，不过需要您知道一定的系统接口知识。

下面的命令可以查看当前系统的默认 `Shell`。

```shell
# 查看系统的默认外壳程序
$ echo $SHELL
/bin/bash
```

除了默认使用 `bash` 除了默认的 `bash` 以外，也可以查看系统是否有其他的已经安装好的外壳程序，下面是我的系统显示的结果。

```shell
# 查看系统安装好的外壳程序
$ cat /etc/shells
/bin/sh
/bin/bash
/bin/tcsh
/bin/csh
```

可以直接使用 `tcsh、csh...` 切换不同的外壳解释器，因此当前正在使用的 `Shell` 不一定是默认的 `Shell`，一般来说，`ps` 命令结果的倒数第二行是当前的 `Shell` 程序。

```shell
# 使用 ps 查看解释器
$ csh # 切换当前解释器为 csh
$ ps
  PID TTY          TIME CMD
16818 pts/3    00:00:00 bash
28500 pts/3    00:00:00 csh # 这就是当前的解释器
28531 pts/3    00:00:00 ps
```

用户可以通过 `bash` 命令的 `--version` 参数或者环境变量 `$BASH_VERSION`，查看本机的 `Bash` 版本。