# 1.语言级别文件接口

## 1.1.文件和输入输出

在谈及系统接口之前，我们先来从`C`语言的角度来谈及一些前要知识，以辅助我们后续来理解系统`IO`。

我们知道，在`C`语言中，有很多用于文件输入输出的接口。

> 补充：`C/C++`程序会默认打开三个文件流：标准输入、标准输出、标准错误，这也就是为什么在没有手动打开（`open`）键盘和显示器的情况下，却依旧可以使用`printf()`、`scanf()`、`cout`...

但是这一操作很奇怪，中间省略了很多步骤，文件是谁在访问呢？操作系统吗？太宽泛了，让我们再思考一下：我们先写一个包含打开文件接口的`C`代码、经过编译生成可执行程序、运行程序加载到内存中转化为进程，因此：是进程在访问文件！

但是还是不够深入，文件放在哪里呢？磁盘上，但是进程是这么从磁盘上访问文件呢？需要通过一些系统接口。

而我们之前在代码中使用的接口都是语言级别的接口（对系统接口进行了封装，好用是好用了，但是每一种语言都会做不同的封装，但是如果直接使用文件接口，写出来的代码又不具备跨平台性）。

另外，文件实际上也有狭义和广义之分，上述提及的文件是狭义上的文件。

在`C`语言中使用`fope()`打开然后使用`fread()`读取，就是读到了进程内部，进程就在内存内部，因此就是读到内存里（`input`）。然后通过`fwrite()`等接口写入到文件中（`output`）。

因此对于系统来说：

1. 狭义的文件：存储在磁盘上的资源、数据叫做文件

2. 广义的文件：上几乎所有的外设，只要具有`input`和`output`特点的都可以称为文件

## 1.2.C语言的常用输入输出接口

如果我们使用`C`语言打开一个文件然后进入死循环，然后打开进程`ID`对应在`/proc/进程ID`对应的文件，可以看到当前这个进程的进程信息。

其中：

1. `exe`指向的是该进程对应源文件在磁盘上的路径

2. `cwd`指向当前的工作路径，这也就是为什么直接用追加读写模式的时候可以在源文件同级的目录下新建文件 

```c
//写入文件
#include <stdio.h>
#include <string.h>
#include <unistd.h>
int main()
{
    FILE* fp = fopen("limou.txt", "w");//最后一个参数是打开模式：r是只读，w是写入，a是追加。
    if(!fp) 
    {
        preeor("fopen()");
        return 1;
    }

    const char* s1 = "hello fwrite.\n";
    fwrite(s1, strlen(sl), 1, fp);//这里会发现这个字符串不需要结尾加上`\0`，因为这只是C语言的规定，已经写入到文件中的数据就已经和C语言无关了，不适合文件中的规则，文件只需要保存有效数据就可以

    const char* s2 = "hello fprintf.\n";
    fprintf(fp, "%s", s2);

    const char* s3 = "hello fputs.\n";
    fputs(s3, fp);

    fclose(fp);
    return 0;
}
```

还有一些读文件的接口以后补充。 

# 2.系统级别文件接口

不同语言需要封装在不同平台的系统文件接口，以便于自己的语言可以跨平台使用。但是为了理解一些概念，我们必须对一些接口有所概念。

## 2.1.open(fopen)

```c
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
int open(const char* pathname, int flags);//路径和选项，通常这个函数用来读取比较多
int open(const char* pathname, int flags, mode_t mode);//路径和选项以及模式，通常这个函数用来写入比较多
//其中flags对应的选项主要有：
//1.O_APPEND追加
//2.O_CLOEXEC文件不存在就会被创建
//3.O_TRUNC如果文件存在，并且是常规文件，且打开的目的是写入，那么这个文件会被清空
//4.O_RDONLY表示只读、O_WRONLY表示只写、O_RDWR表示读和写，这三个选项必须使用一个，再配合其他关键字使用
//...

//其中mode就是文件的权限，和；linux的文件权限的二进制表示是一样的，也会受到umask权限掩码的影响，我们也可以调用系统接口umask()来设置umask掩码，是进程专有的
```

可以看到光是头文件就需要包含三个，而且还有两个`open()`需要选择，这样就比`C`语言会难用一些，更是还有众多的`flag`可以组合选择。

那么上面的`flag`宏关键字怎么组合使用呢？想要传递多个选项怎么做呢？

一个`int`有`32`个比特位，那么每一个比特位都可以表示一种状态，只需要使用位操作来组合即可，这种方式再编码中很常见，可以使一个参数有多种可能，比如下面的代码。

```c
#define ONE 0x1
#define TWO 0x2
#define THR 0x3
void show(int flags)
{
    if(flags & ONE) printf("one\n");
    if(flags & TWO) printf("two\n");
    if(flags & THR) printf("thr\n");
}
int main()
{
    show(ONE);
    show(ONE | TWO);
    show(ONE | TWO | THR);
    return 0;
}
```

因此使用`open()`也是使用类似的方式来组合调用不同的标志位。

那么`mode`参数又该怎么写呢？

```c
#include <stdio.h>

#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
int mian()
{
    umask(0);//设置进程专用的umask掩码
    int fd = open("limou.txt", O_WRONLY, 0666);//不会默认创建文件，并且创建出来的权限有很多是没有的
    if(fd < 0)
    {
        perror("open");
        return 1;
    }
    printf("open success, fd: %d\n", fd);//输出3
    return 0;
}
```

## 2.2.close(fclose)

```c
#include <unistd.h>
int close(int fd);
```

比较简单，就是关闭一个文件，将`open()`的返回值传入即可关闭文件。

## 2.3.read(fread)

```c
ssize_t read(int fd, void* buf, size_t count);//将文件内容读取到buf中，ssize_t是实际读取到的字符个数
```

## 2.4.write(fwrite)

```c
#include <unistd.h>
ssize_t write(int fd, const void* buf, size_t conut);//fd就是打开文件返回的fd，buf就是需要写入的数据，count就是写入个数
```

我们直接来使用试试：

```c
#include <stdio.h>

#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
int mian()
{
    umask(0);//设置进程专用的umask掩码
    int fd = open("limou.txt", O_WRONLY, 0666);//不会默认创建文件，并且创建出来的权限有很多是没有的
    if(fd < 0)
    {
        perror("open");
        return 1;
    }
    const char* s = "hello word!\n";
    write(fd, s, strlen(s));//最后一个参数不用加1
    printf("open success, fd: %d\n", fd);//输出3
    return 0;
}
```

但是上述函数的返回值是什么？这就需要提到文件描述符的概念了。

# 3.文件描述符和重定向

## 3.1.文件描述符

```c
int main()
{
    inf fd1 = open("limou1.txt", O_WRONLY|O_CREAT|O_APPEND, 0666);
    printf("open success, fd1: %d\n", fd1);
    inf fd2 = open("limou1.txt", O_WRONLY|O_CREAT|O_APPEND, 0666);
    printf("open success, fd2: %d\n", fd2);
    inf fd3 = open("limou1.txt", O_WRONLY|O_CREAT|O_APPEND, 0666);
    printf("open success, fd3: %d\n", fd3);
    inf fd4 = open("limou1.txt", O_WRONLY|O_CREAT|O_APPEND, 0666);
    printf("open success, fd4: %d\n", fd4);

    close(fd1);
    close(fd2);
    close(fd3);
    close(fd4);
    return 0;
}
//输出了3、4、5、6
```

这个`fd`返回值究竟是什么？是文件描述符，`0`、`1`、`2`去哪里了呢？是三个标准输入输出文件！被`stdin(0)`、`stdout(1)`、`stderr(2)`所占用。

```c
int main()
{
    fprintf(stdout, "hello\n");

    const char* s = "I am limou\n";
    write(1, s, strlen(s));
    return 0;
}
```

而`FILE`是`C`标准库设计的文件结构体，内部有多种成员。在系统角度只认识`fd`，不认识`FILE`。因此在`FILE`结构体内部一定封装了文件描述符`fd`。因此在上面所说的标准输入输出中，由于它们也是`FILE`结构体，因此内部也一定有`fd`文件描述符，这也就是为什么上面会说“被占用”。这一点可以打印`FILE`结构体的成员变量的`_fileno(存储了文件描述符)`来证明。

## 3.2.进程和文件的关联

一个进程可以打开多个文件，而一个文件要被访问也必须被一起加载到内存中才能被进程访问，那如果多个进程都在打开文件怎么办？内存根本不够用！因此操作系统不可能将如此多的文件全部一次性打开，必须经过描述和管理，构建一个结构体。

在内核中，为了管理被打开的文件，就必须创建一个`struct file{struct file* next; struct file* prev;};`（不仅包含属性和数据还有更多的）结构体来描述文件，并且使用双链表链接起来组织（先描述再组织）。

每打开一个文件，就会实例化该结构体，插入到链表中，来代表一个文件。一条链表就是当前所有进程需要的被打开的文件，但是文件和进程之间怎么关联呢？靠的就是`fd`文件描述符，所有的`fd`值构成一个数组的下标，这个数组的类型就是`struct file* arrary[]`，也就是一个指针数组，每一个指针成员都指向一个文件结构体。

一句话：这样系统就可以在进程对象中使用`fd`下标来访问一个指针数组，然后找到指向的文件结构对象，进而使用该对象描述的文件。

因此也可以将文件分为：

1. 内存文件：已经被打开的文件（在内存）

2. 磁盘文件：没有被打开的文件（在磁盘）

而再上述提到的结构体内部具有成员变量，而它又是描述文件的，因此文件有大部分属性来自这个结构体。

### 3.2.1.文件结构指针的指针数组

是否真的存在这么一个“数组”来指向结构体呢？我们可以查看一下`Linux`内核的源码来证明：

```c
struct files_struct
{
    atomic_t count;
    struct fdtable *fdt;
    struct fdtable fdtab;

    spinlock_t file_lock ___cacheline_aligned_in_smp;
    int next_fd;
    struct embedded_fd_set close_on_exec_init;
    struct embedded_fd_set open_fds_init;
    struct file* fd_array[NR_OPEN_DEFAULT];//重点关注这个数组，如果是64位，NR_OPEN_DEFAULT=64，32位就是32。
};
```

上面的结构体内的`fd_array[]`数组就是指针数组，每一个指针成员指向一个描述文件的结构体。

但是为什么这个数组这么小呢？只能同时打开`64/32`个文件？实际上还有其他的拓展字段（其他成员变量）来帮助这个数组指向更多的文件，在现在的操作系统里，有的时候一个进程可以打开的文件能达到`10 000`个。

### 3.2.2.描述文件的文件结构体

那描述文件用的结构体呢？

```c
struct file
{
    union
    {
        struct list_head fu_list;
        struct rcu_head fu_rcuhead;
    } f_u;
    struct path f_path;
    //...
    fmode_t f_mode;//文件权限
    struct fown_struct f_owner;//文件的拥有者
    u64 f_version;//文件的版本
    //...
};
```

这个文件被实例化就是具体描述了一个文件。

## 3.3.C语言库文件读写函数底层

因此经过上面所有知识的铺垫，我们终于可以得到一个结论：

1. `C`语言调用`fopen()`，则调用了系统接口`open()`，`open()`得到一个文件描述符`fd`（数组下标），而文件描述符又被`C`语言的`FILE`结构体封装，最后以`FILE*`的方式返回给`fopen()`，也就是我们平时在C语言内使用的文件指针。

2. 然后我们在使用`C`语言的`fwrite()`的时候，传进来了一个`FILE*`，指向的结构体变量内部具有一个`fd`文件描述符，就可以通过这个`fd`传给我i`write()`来调用系统接口，然后操作系统找到进程的`task_struct{};`，就找到了内部的`*fs`，该指针指向`files_struct{};`，而内部的数组`fd_array[]`根据`fd`就找到了`struct file{};`的变量，也就是一个内存文件被找到了，然后就进行某一些操作

## 3.4.文件描述符的分配原则

我们之前有注意到，`fd`的分配永远是从`3`开始么？

如果我们把默认的三个标准输入输出文件使用`fclose()`关掉，那么就会优先分配最小的没有被占用的文件描述符给新打开的文件。

## 3.5.输出输入重定向的原理

如果`close()`关掉`fd=1`的文件，根据`fd`的分配原则。代码会打印不出新分配给打开文件的`fd` 。因为关闭`fd=1`意味着下次打开文件分配的`fd`为`1`，而`C`又默认往`fd=1`d的`stdout`打印。那么打印就会往新打开的文件内输出。而且不只是这一次打印，往后所有的打印输出函数都会输入的这个新打开的文件内部，这也模拟了输出重定向。

类似的关掉`fd=0`也可以模拟输入重定向。

```c
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
int mian()
{
    close(1);
    int fd = open("limou.txt", O_WRONLY|O_TRUNC|O_CREAT);//会覆盖文件的内容
    if(fd < 0)
    {
        perror("open");
        return 1;
    }
    fprintf(stdout, "you can see me, success\n");
}
```

但是如果我们在代码的末尾带上`fcloes(文件指针)`或者`fflush(stdout)`，我们会发现，又可以打印出来了？！这又是为什么呢？我们后面来讲解，这里先跳过。

上面代码还需要我们先关闭标准输入输出，有没有其他的办法呢？有的，接下来就让我们来学习一下重定向的底层调用：

```c
int dup(int oldfd);
int dup2(int oldfd, int newfd);//重点了解这一个
int dup3(int oldfd, int newfd, int flags);
```

我们了解`dup2()`就够了，`duq2()`就是把`oldfd`内的指针（也就是文件结构体指针）拷贝给`newfd`（也就是留下`oldfd`）。

因此`newfd`曾经的指向就可以被关闭了。

```c
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
int mian(int argc, char* argv[])
{
    if(argc != 2)
    {
        return 2;
    }
    int fd = open("limou.txt", O_WRONLY|O_TRUNC|O_CREAT);//会覆盖文件的内容
    if(fd < 0)
    {
        perror("open");
        retrun 1;
    }
    dup2(fd, 1);//重定向输出
    fprintf(stdout, "%s\n", argv[1]);//打印出携带的参数
    return 0;
}
```

因此上面的代码就是重定向的原理。

> 补充：“`Linux`下一切皆文件”，在进程控制和基础`IO`的知识背景下我们可以更加深入了解这句话。
> 
> 首先，`Linux`内核大部分都是使用`C`语言实现的，那么`C`有没有办法实现面向对象呢？是可以的！
> 
> 例如：对于一个文件结构体，首先`C`的结构体具备类的雏形，可以在内部定义文件的属性（结构体成员变量），那么方法怎么实现呢？使用函数指针（结构体成员变量），只需要函数指针实现恰当，就可以在结构体中包含方法。
> 
> 因此一个基础的类就实现了。
> 
> 因此“一切皆文件”就是指：可以使用`C`语言的结构体来描述所有的硬件的“属性”和“操作”，那么调用这些硬件就如同调用一个对象，设置对象属性，根据属性使用对象的操作方法...这就是“一切接文件”的本质！
> 
> 另外，`C`语言虽然是面向过程的语言，但是并不意味着`C`不可以实现面向对象思想，像上述的说明就实现了一个类的封装。在某些巧妙地设计下，实现`OOP`其他重要特性（比如：多态）也是完全可以的，当然，对比纯`OOP`语言来说会有些麻烦（比如`Java`）。
> 
> 实际上，语言从面向过程到面向对象也是经历了这些大量的实践（每次都要设计出这样带有属性和方法的结构体）才被人们设计出来的。

我们之前写过一个`MyShell`项目，但是不支持重定向，接下来让我们完整实现一下。

```c
//myshell.c
#include <stdio.h>
#include <stdlib.h>
#include <assert.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#define NUM 1024
#define SIZE 32
#define SEP " "
char cmd_line[NUM];//保存完整命令字符串
char* g_argv[SIZE];//保存打散后的命令字符串

#define INPUT_REDIR 1//输入重定向
#define OUTPUT_REDIR 2//输出重定向
#define APPEND_REDIR 3//追加重定向
#define NONE_REDIR 0

int redir_status = NONE_REDIR;
char* Checkredir(char* cmd_str)//查找重定向符号
{
    assert(cmd_str);
    char* end = cmd_str + strlen(cmd_str) - 1;//例如"abcd\0"，end->'\0'
    while (end >= cmd_str)
    {
        if (*end == '>')
        {
            if (*(end - 1) == '>')
            {
                //追加重定向
                redir_status = APPEND_REDIR;
                *(end - 1) = '\0';//例如"ls -a -l>>mytext.txt"，end->后一个'>'，end-1->前一个'>'，将前面的命令分割为单独的字符串
                end++;//此时end->'m'
                break;
            }
            //输出重定向
            redir_status = OUTPUT_REDIR;
            *end = '\0';//例如"la -a -l>mytext.txt"，end->'>'，将前面的命令分割为单独的字符串
            end++;//end->'m'
            break;
        }
        else if (*end == '<')
        {
            redir_status = INPUT_REDIR;
            *end = '\0';
            end++;
            break;
        }
        end--;
    }
    if (end >= cmd_str)
    {
        return end;//返回要打开文件
    }
    else
    {
        return NULL;//说明没有找到重定向符号，直接返回NULL
    }
}
int main()
{
    while (1)
    {
        //1.打印提示信息
        printf("[user@myshell]$ ");
        fflush(stdout);
        memset(cmd_line, '\0', sizeof(cmd_line));
        //2.获取user的输入
        if (fgets(cmd_line, sizeof(cmd_line), stdin) == NULL)
        {
            //出错处理
            continue;
        }
        cmd_line[strlen(cmd_line) - 1] = '\0';
        char* sep = Checkredir(cmd_line);//分析是否有重定向符号

        //3.命令行字符解析
        //虽然可以自己写一个算法解析，但是我们可以一些现有的接口
        g_argv[0] = strtok(cmd_line, SEP);//第一次调用需要传入原始字符串

        int index = 1;
        if (strcmp(g_argv[0], "ls") == 0)
        {
            g_argv[index++] = "--color=auto";
        }
        while (g_argv[index++] = strtok(NULL, SEP));//第二次还想要解析原始字符串，就需要传入NULL
        //4.TODO                                                
        //需要判断命令，如果是cd这样的命令不能使用子进程
        if (strcmp(g_argv[0], "cd") == 0)
        {
            printf("下面功能让父进程来\n");
            //更改路径的命令
            if (g_argv[1] != NULL) chdir(g_argv[1]);
            continue;
        }
        //5.fork()一个子进程
        pid_t id = fork();
        if (id == 0)//child
        {
            if (sep != NULL)
            {
                //找到重定向符号，并且返回的sep保存了重定向的文件
                int fd = -1;//因为switch内部不可以定义变量，所以搬到外面先定义了
                switch (redir_status)//确认重定向的状态
                {
                case INPUT_REDIR:
                    fd = open(sep, O_RDONLY);
                    dup2(fd, 0);//输入重定向
                    break;
                case OUTPUT_REDIR:
                    fd = open(sep, O_WRONLY | O_TRUNC | O_CREAT, 0666);
                    dup2(fd, 1);//输出重定向
                    break;
                case APPEND_REDIR:
                    fd = open(sep, O_WRONLY | O_APPEND | O_CREAT, 0666);
                    dup2(fd, 1);//输出重定向
                    break;
                default:
                    printf("There is bug.\n");
                    break;
                }
            }
            printf("子进程run：\n");
            execvp(g_argv[0], g_argv);
            exit(1);
        }
        //father
        int status;
        pid_t ret = waitpid(id, &status, 0);
        if (ret > 0) printf("exit code: %d\n", WEXITSTATUS(status));
    }
    return 0;
}
```

# 4.缓冲区

我们之前提到过缓冲区，实际就是一段内存空间。

## 4.1.缓冲区作用

为什么需要缓冲区呢？原因很简单，为了效率的问题。

1. 如果一个一个输出/输入数据，直接写透输出到磁盘的另外一个文件中，那么这个实现就会频繁访问磁盘（写透模式`WT`，缓慢且效率低）。

2. 但是如果先拿内存空间存储起来表示写入完成（这里写了就回去了，在写入的文件看来自己已经完成对某文件的写入了（实际并没有，只是交给小段的内存空间了），也就是所谓的“写回”），再一起输出/输入到磁盘中的文件（刷新），这样效率就会很高，访问磁盘不会特别频繁（写回模式`WB`，快速且效率高）。

因此有了缓冲区就可以提高用户的响应速度，避免磁盘访问速度缓慢引起用户的输入输出请求响应缓慢。

> 补充：在某些情况下，我们也可以把内存当作一个大型的缓冲区来理解。

## 4.2.缓冲区刷新

1. 立刻刷新（即时缓冲）

2. 写一行用换行就刷新（行刷新）

3. 占满缓冲区就刷新（全刷新）

但是有一些特殊情况也会刷新缓冲区：用户强制刷新（`fflush()`）、进程退出等。

> 补充：一般显示器采用行刷新（符合阅读习惯），磁盘采用全缓冲（提高效率需求）。

## 4.3.缓冲区提供

但是缓冲区这段内存空间是谁提供的呢？

```c
//mian.c
int mian()
{
    //C语言接口
    printf("hello printf()\n");
    fprintf(stdout, "hello fprintf()\n");
    const char* s = "hello fputs()\n");
    fputs(s, stdout);
    //OS系统接口
    const char* str = "hello write()\n");
    write(1, ss, strlen(ss));   

    fork();
    return 0;
}
//保存通过gcc生成a.out
```

如果直接运行`./a.out`，那么输出`4`行字符串确实没毛病。

```bash
hello printf()
hello fprintf()
hello fputs()
hello write()
```

但是如果将内容重定向到同级的另外一份空的文本文件`/.a.out > text.txt`，就会发现会多出很多语句，并且顺序也很奇怪，这是为什么呢？

```bash
hello write()
hello printf()
hello fprintf()
hello fputs()
hello printf()
hello fprintf()
hello fputs()
```

这是因为我们之前提到的缓冲区是由语言来维护的（准确的说是`C`标准库）。

首先，我们可以选择在进程里使用`C`提供的函数来写入缓冲区，再由缓冲区自己调用`write()`写入系统。也可以直接调用`write()`那就会直接写入到系统。

我们之前是在显示器上刷新缓冲区的（行刷新）因此父进程运行一段代码，遇到`\n`就会刷新一次（这就是一种数据的改动），然后再创建子进程。对于子进程来说，父进程缓冲区内的数据已经被输出了，也就没有缓冲区的数据需要写时拷贝（继承父进程的数据）了，因此只有`4`行的输出，子进程没有任何的输出。

而当我们将输出重定向的时候，就是向磁盘中的文件写入，在`fork()`之前：

1. 前面的函数被执行完了，但是不代表缓冲区的数据已经被刷新了，这是因为现在是将缓冲区的数据刷新到到磁盘（全刷新），因此代码语句中的`\n`变得没有效果了，父进程在缓冲区中的数据会保存着不会立刻输出

2. 而缓冲区的数据也是父进程的数据，在`fork()`之后，由于父进程在结束进程的时候需要刷新缓冲区的文件（这就是一种数据的改动）。为了避免子进程被父进程影响，创建子进程的时候，父进程缓冲区内的数据会发生一次写时拷贝（让子进程继承）

3. 最后在父子进程都结束进程后，由于进程结束必须要刷新所有缓冲区的数据，因此就会出现两份相同的输出（但是谁先退出这是不清楚的）

因此我们可以在`fork()`代码的前面再加一条`fflush()`来避免创建子进程的时候发生写时拷贝，进而实现正常打印四条输出。

```c
//mian.c
int mian()
{
    //C语言接口
    printf("hello printf()\n");
    fprintf(stdout, "hello fprintf()\n");
    const char* s = "hello fputs()\n");
    fputs(s, stdout);
    //OS系统接口
    const char* str = "hello write()\n");
    write(1, ss, strlen(ss));   
    fflush(stdout);
    fork();
    return 0;
}
```

但是为什么`fflush()`只输进去了一个参数呢，缓冲区的所在地呢？实际上`struct FILE{//...};`结构体不仅仅保存了文件描述符`fd`，还封装了`fd`对应的大量的语言级缓冲区结构。在一些`stdio.h` 实现里`FILE`是由`IO_FILE`封装的。因此在`FILE{//...};`内部我们可以看到类似`_IO_read_ptr`、`_IO_read_end`等缓冲区相关的关键字。

而对于操作系统来说，每一个`file{//...};`内也有一个内核缓冲区，所以我们使用`write()`的时候，也是将数据放进了系统级别的缓冲区。但是从上述代码输出来看，为什么就没有发生写时拷贝呢？醒醒吧，是子进程要使用父进程中有可能会被修改的数据时才发生写时拷贝，而`write()`放入操作系统缓冲区，而不是在进程里操作写入，由操作系统来定义刷新。因此进程之间的写实拷贝和这里无关，写实拷贝是在进程里创建子进程的时候发生的（但是这里系统级别的缓冲区我们只能先说到这里，没有办法继续展开了...）。

那么其他语言，例如：`C++`语言又是怎么做到的呢？对`<<`进行重载，然后内部实现的时候将数据拷贝到`buffer`里就可以，后面再进行刷新即可（这里可以查看一下`cout`的实现，找找看里面是否存在`fd`）。

最终结论：在`C`语言中，读写用的语言级别缓冲区由`C`库来维护，并且就在`FILE{//...};`内部描述缓冲区的属性。

## 4.4.缓冲区模拟

这里我们只模拟缓冲区原理的一部分。

```c++
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <assert.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
typedef struct MyFILE//模拟FILE
{
    int fd;//文件描述符
    char buffer[1024];//缓冲区
    int end;//结尾
}MyFILE;
//可以看到文件的数据都在MyFILE里，因此所有的接口都需要使用MyFILE
MyFILE* MyFopen(const char* pathname, const char* mode)
{
    //1.检查参数都不为空
    assert(pathname);
    assert(mode);
    //2.对不同模式做不同的操作
    MyFILE* fp = NULL;
    if (strcmp(mode, "r") == 0)
    {
    }
    else if (strcmp(mode, "r+") == 0)
    {

    }
    else if (strcmp(mode, "w") == 0)
    {
        int fd = open(pathname, O_WRONLY | O_TRUNC | O_CREAT, 0666);//调用系统接口，写入之前先清空文件，不存在时需要创建
        if (fd >= 0)
        {
            fp = (MyFILE*)malloc(sizeof(MyFILE));
            memset(fp, 0, sizeof(MyFILE));
            fp->fd = fd;
        }
    }
    else if (strcmp(mode, "w+") == 0)
    {

    }
    else if (strcmp(mode, "a") == 0)
    {

    }
    else if (strcmp(mode, "a+") == 0)
    {

    }
    return fp;

}
void MyFputs(const char* message, MyFILE* fp)
{
    assert(message);
    assert(fp);
    strcpy(fp->buffer + fp->end, message);//注意这个函数是会自动添加'\0'的
    fp->end += strlen(message);//防止多次写入缓冲区被覆盖

    if (fp->fd == 0)
    {
        //标准输入
    }
    else if (fp->fd == 1)
    {
        //标准输出
        if (fp->buffer[fp->end - 1] == '\n')
        {
            write(fp->fd, fp->buffer, fp->end);//默认显示器行刷新
            fp->end = 0;
        }
    }
    else if (fp->fd == 2)
    {
        //标准错误
    }
    else
    {

    }

}
void MyFflush(MyFILE* fp)
{
    assert(fp);
    if (fp->end != 0)
    {
        write(fp->fd, fp->buffer, fp->end);//实际上是写入到内核缓冲区里了，一般是内核会帮助我们刷新，但是我们也可以尝试使用syncfs()可以刷新数据到磁盘上
        syncfs(fp->fd);//刷新系统缓冲区到磁盘
        fp->end = 0;
    }
}
void MyFclose(MyFILE* fp)
{
    assert(fp);
    MyFflush(fp);//关掉文件之前先刷新一下缓冲区到文件里
    close(fp->fd);
    free(fp);
}
int main()
{
    MyFILE* fp = MyFopen("./limou.txt", "w");
    if (fp == NULL)
    {
        printf("open error\n");
        return 0;
    }
    MyFputs("hello!", fp);
    MyFputs("I am limou.", fp);
    MyFputs("This are words.", fp);
    MyFclose(fp); 
}
```

上面的代码不仅是实现了缓冲区的输入操作，还实现了一种缓冲区刷新策略（行刷新）。

您还可以在代码一开始的时候关闭`fd=1`的文件（也就是标准输出），这样系统就会给您的文件分配`fd=1`，再结合`usleep()`来观察行刷新的操作。

并且我们还可以尝试在代码结尾添加`fork()`来重现我们在`4.3.缓冲区提供`中出现重复打印的现象。

而效率提高的地方就在于`IO`执行的次数变少（访问磁盘次数减少），在内存的操作比较多（放入`buffer[]`中）。

# 5.标准输出和标准错误

两个都是输出到显示器，那么两者有什么区别呢？让我们来先看一段代码。

```c++
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <cstdio>
#include <cstring>
#include <iostream>
using namespace std;
int main()
{
    printf("hello printf 1\n");//->stdio
    fprintf(stdout, "hello fprintf 1\n");
    perror("hello perror 2");//->stder

    const char* s1 = "hello write 1\n";
    write(1, s1, strlen(s1));

    const char* s2 = "hello write 2\n";
    write(2, s2, strlen(s2));

    cout << "hello cout 1" << endl;
    cerr << "hello cerr 2" << endl; 
    return 0;
}
```

这份`C++`代码的运行结果和重定向结果如下：

```bash
$ g++ main.cpp
$ ./a.out
hello printf 1
hello fprintf 1
hello perror 2: Success
hello write 1
hello write 2
hello cout 1
hello cerr 2

$ ./a.out > limou.txt
hello perror 2: Success
hello write 2
hello cerr 2

$ cat limou.txt
hello write 1
hello printf 1
hello fprintf 1
hello cout 1
```

可以看出`1`和`2`对于的都是显示器文件，但是两个是不同的，可以认为一个显示器文件被打开了两次。`1/2`描述符都指向显示器文件。因此做重定向的时候我们会发现，如果`1`被`dup2()`了，不代表`2`会被`dup2()`。

这就是两者的最大区别，这样我们就终于可以理解，为什么开发者不自己使用类似`printf()`和`if()`，而使用`preeor()`这样的函数来做一个错误检查的输出了。

如果程序在运行的过程中出现了问题，使用类似`preeor()`、`cerr()`等函数会更加方便。

并且如果希望一般的文本输出和错误输出分离开查看的话，可以使用类似`./a.out > text.txt 2>error.txt`的命令，这样就可以实现文本输出和错误输出分离到两个文件内，直接进行查看就可以。

如果想要文本输出和错误输出全部放进一个文件了，则可以使用命令`./a.out > all.txt 2>&1`。

重定向也可以使用`cat < source.txt > copy.txt`来拷贝文件。

而`perror()`实际上我们也可以设计一个

```c
void myperror(const char* msg)
{
    fprintf(stderr, "%s: %s\n", msg, strerror(erron));//后面这个函数就是打印错误信息
}
```

# 6.理解文件系统

首先我要注意到我们之前学习的文件都是被打开的文件（被进程访问），那么哪些存储在磁盘中的没有被打开的文件呢？  这些文件又有哪些需要注意的地方呢？

首先我们先要理解磁盘的一些基本知识。

1. 内存是一种失电易失存储介质

2. 磁盘就是永久性存储介质（还有一些存储介质，例如：`SSD`、`U`盘、`flash`卡、光盘、磁带...但是磁盘的性价比要高）

磁盘对比`CPU`的处理速度是比较慢的，因此操作系统也会有一些提速处理的方式（这里暂时不提及，您只需要知道有就是了）

而磁盘的具体结构大概为：磁盘盘片（存储数据，由很多密度非常大的磁性颗粒来构成，利用南北磁性表示`1/0`，磁盘和磁盘之间存在悬浮避免损坏）、磁头（寻址，可以利用放电来改变磁性，因此可以存放数据，也可以拿来读取数据）、伺服系统、音圈马达...

## 6.1.文件角度

### 6.1.1.文件存储



### 6.1.2.文件属性

## 6.2.系统角度

### 6.2.1.文件数量

### 6.2.2.文件查找

# 软硬链接
