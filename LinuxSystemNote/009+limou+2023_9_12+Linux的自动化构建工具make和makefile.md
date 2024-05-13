[TOC]

# 1.make概念

`make`是一个指令，`makefile`是一个在当前目录下存在的一个具有特定格式的文件。

如果在`Linux`中编写了大量的代码文件，难不成要一个一个使用`gcc/h++`编译吗，要是代码出了问题，修改后又需要编译怎么办？因此出现了`make`这样的自动化构建工具。 

# 2.编写.c/.cpp源文件

先在一个目录下编写一个或多个`.c`源文件。

# 3.编写makefile文件

1. 首先`touch`一个`makefile`文件

2. `makefile`内部编写目标文件、依赖文件列表、依赖方法

3. 写入用作清理的伪目标，`.PHONY`是修饰伪目标的关键字

```bash
#make指令自顶向下执行，如果直接使用make指令就会执行第一份依赖命令

#目标文件:依赖文件列表
#   依赖方法（Linux下的命令）
#.PHONY:伪目标（重点是执行方法而不是目的（“phony”即“假的，伪的”））
#   依赖方法

run:main.c #依赖关系
    gcc main.c -o run #依赖方法

.PHONY:clean #没有依赖关系，设定为伪目标
clean:
    rm -f run #依赖方法
```

4. 使用`make`指令运行`makefile`文件，不指定目标直接使用`make`指令，就会自动找第一对依赖关系和依赖方法（即`makefile`的第一个目标）。

# 4.运行指令

```bash
$ ll
total 8
-rw-rw-r-- 1 limou limou 147 Sep 14 10:51 main.c
-rw-rw-r-- 1 limou limou  62 Sep 14 10:53 makefile
$ cat main.c
#include <stdio.h>
int main()
{
    int i = 0;
    for(i; i < 10; i++)
    {
        printf("%d ", i * i);
    }
    printf("\n");
    return 0;
}
$ cat makefile
run:main.c
    gcc main.c -o run
.PHONY:clean

clean:
    rm -f run

$ make run
gcc main.c -o run

$ ll
total 20
-rwxrwxr-x 1 limou limou 8408 Sep 14 10:54 run
-rw-rw-r-- 1 limou limou  147 Sep 14 10:51 main.c
-rw-rw-r-- 1 limou limou   62 Sep 14 10:53 makefile

$ ./run
0 1 4 9 16 25 36 49 64 81 

$ make clean
rm -f run

$ ll
total 8
-rw-rw-r-- 1 limou limou 147 Sep 14 10:51 main.c
-rw-rw-r-- 1 limou limou  62 Sep 14 10:53 makefile
```

# 5.重复指令

很多书都会写：“伪目标总是会被执行的”。例如：

1. `make run/make`指令是不能被重复调用的（会显示`make:'run' is up to date.`。
   除非我们要编译的源文件的最近修改时间发生了变动，也就是说如果源文件没有经过修改，`make run`是不会再次编译的，因为这样做的效率低下（大型的文件编译的时间非常久））。
   另外如果我们对源文件使用`touch`指令更新修改时间，那么就也是能重复使用`make main`指令。

2. 但`make clean`指令总是可以被调用的。因此“总是被执行”的另外一个说法是“总是会根据依赖关系，执行依赖方法”。

> 补充：使用`stat`可以查看文件的三个重要时间
> 
> 1. `Access`：文件最近被访问的时间（即：读取的时候，`touch`只更改这个时间，但是由于`Access`也是属性，所以这个改动也会同时改动`Change`属性），如果修改了文件也算访问。不过由于读取文件是高频工作，因此`Linux`修改了内核，只会在一定时间后进行修改，不会读取一次就修改一次（每次都修改就会导致反复访问磁盘，导致效率低下）
> 
> 2. `Modify`：文件的最近内容修改时间（`make`指令就是看这个文件来确认是否要重新编译的），不过有的时候改变内容会改变大小，导致文件的属性变化，`Change`也发生了变化
> 
> 3. `Change`：文件的最近属性修改时间，比如：文件权限的设置、文件的大小改变

> 注意：文件改变的本质不是时间变化，而是随着时间文件内容产生新旧内容。

# 6.自动推导

`make`可以进行自动推导（嵌套代码），但是一般不建议像下面这么写，只是为了证明`make`可以自动推导这一现象：

```bash
run:main.o
    gcc main.o -o run
main.o:main.s
    gcc -c main.s -o main.o
mian.s:main.i
    gcc -S mian.i -o mian.s
mian.i:mian.c
    gcc -E main.c -o main.i

.PHONY:clean

clean:
    rm -f run main.i main.s main.o
```

# 7.拓展学习

上面的知识在平时的学习和使用中就很够用了，但是我们可以拓展学习一些其他的内容。

1. 有的时候不希望打印出使用`make`命令的信息，这个时候可以在每个依赖方法前加上`@`符号，这样就不会回显信息了

2. 在`makefile`允许创建变量，因此我们上面的代码还可以改写为更加普遍的（变量的赋值一般不建议留空格，不过您其实可以将这种变量作为宏来理解）
   
   ```bash
   cc=gcc
   src=main.c
   target=run
   
   $(TAGET):$(src) #依赖关系
       $(cc) $(src) -o $(target) #依赖方法
   
   .PHONY:clean #伪目标
   clean:
       rm -f $(target) #依赖方法
   ```

3. `makefile`还可以在依赖方法省略依赖关系用到的依赖文件列表和目标文件
   
   ```bash
   run:main.c #依赖关系
       gcc $^ -o $@ #依赖方法，$^表示冒号右侧内容，$@表示冒号左侧的内容
   
   .PHONY:clean #没有依赖关系，设定为伪目标
   clean:
       rm -f run #依赖方法
   ```
