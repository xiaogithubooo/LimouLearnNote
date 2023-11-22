接下来利用我们当前的知识，撰写一个简单的`shell`外壳程序。

# 1.shell原理

`shell`的原理是实际上就是运行了一个父进程，然后创建出子进程，最后使用进程替换调用，替换成其他程序。

# 2.shell实现

## 2.1.死循环

首先一个`shell`一旦运行起来就不会关闭（除非关闭终端窗口），因此一定是一个死循环。

```c
int main()
{
	while(1)
    {
        //...
    }
    return 0；
}
```

## 2.2.提示字符串

运行一个`shell`有出现一个类似`[user@myshell]$`的输入提示符，由于不能换行，因此我们还需要使用`fflush()`进行刷新。

```cpp
#include <stdio.h>
int main()
{
	while(1)
    {
        printf("[user@myshell]$ ");
        fflush(stdout);
    }
    return 0;
}
```

## 2.3.获取用户输入

```cpp
#include <stdio.h>
#include <string.h>

#define NUM 1024
char cmd_line[NUM];//保存完整命令字符串

int main()
{
	while(1)
    {
        //1.打印提示信息
        printf("[user@myshell]$ ");
        fflush(stdout);
    
        //2.获取 user 的输入
        memset(cmd_line, '\0', sizeof(cmd_line));//设定初始值
        if(fgets(cmd_line, sizeof(cmd_line), stdin) == NULL)//获取字符串
        {
            //出错就直接跳出循环
            continue;
        }
        cmd_line[strlen(cmd_line) - 1] = '\0';//即使用户输入字符串超出范围，也能保证获取到一个完整的 C 风格字符串
    }

    return 0;
}
```

## 2.4.解析用户输入

```cpp
#include <stdio.h>
#include <string.h>

#define NUM 1024
char cmd_line[NUM];//保存完整命令字符串

#define SIZE 32
char* g_argv[SIZE];//保存打散后的命令字符串，是一个数组

#define SEP " "//分隔符

int main()
{
	while(1)
    {
        //1.打印提示信息
        printf("[user@myshell]$ ");
        fflush(stdout);
    
        //2.获取 user 的输入
        memset(cmd_line, '\0', sizeof(cmd_line));//设定初始值
        if(fgets(cmd_line, sizeof(cmd_line), stdin) == NULL)//获取字符串
        {
            //出错就直接跳出循环
            continue;
        }
        cmd_line[strlen(cmd_line) - 1] = '\0';//即使用户输入字符串超出范围，也能保证获取到一个完整的 C 风格字符串

        //3.命令行字符解析
        //虽然可以自己写一个算法解析，但是我们可以使用一些现有的接口
        g_argv[0] = strtok(cmd_line, SEP);//第一次调用需要传入原始字符串和分隔符

        int index = 1;
        if(strcmp(g_argv[0], "ls") == 0)//如果输入的命令是 ls 就进行一些特殊处理
        {
            g_argv[index++] = "--color=auto";//增加颜色参数，让输出带有颜色
        }
        while(g_argv[index++] = strtok(NULL, SEP));//第二次还想要解析原始字符串，就需要传入 NULL
    }

    return 0;
}
```

## 2.5.创建并且替换子进程

```cpp
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>
#include <sys/types.h> 
#include <string.h>
#include <stdlib.h>

#define NUM 1024
char cmd_line[NUM];//保存完整命令字符串

#define SIZE 32
char* g_argv[SIZE];//保存打散后的命令字符串，是一个数组

#define SEP " "//分隔符

int main()
{
	while(1)
    {
        //1.打印提示信息
        printf("[user@myshell]$ ");
        fflush(stdout);
    
        //2.获取 user 的输入
        memset(cmd_line, '\0', sizeof(cmd_line));//设定初始值
        if(fgets(cmd_line, sizeof(cmd_line), stdin) == NULL)//获取字符串
        {
            //出错就直接跳出循环
            continue;
        }
        cmd_line[strlen(cmd_line) - 1] = '\0';//即使用户输入字符串超出范围，也能保证获取到一个完整的 C 风格字符串

        //3.命令行字符解析
        //虽然可以自己写一个算法解析，但是我们可以使用一些现有的接口
        g_argv[0] = strtok(cmd_line, SEP);//第一次调用需要传入原始字符串和分隔符

        int index = 1;
        if(strcmp(g_argv[0], "ls") == 0)//如果输入的命令是 ls 就进行一些特殊处理
        {
            g_argv[index++] = "--color=auto";//增加颜色参数，让输出带有颜色
        }
        while(g_argv[index++] = strtok(NULL, SEP));//第二次还想要解析原始字符串，就需要传入 NULL

        //4.fork() 创建子进程，execvp() 替换子进程
        pid_t id = fork();
        if(id == 0)//child
        {
            printf("子进程 run:\n");
            execvp(g_argv[0], g_argv);//自动在环境变量中搜索 g_argv[0] 的文件，执行 g_argv 数组存储的指令
            exit(1);//没替换成功就会异常退出
        }
        //father
        int status;
        pid_t ret = waitpid(id, &status, 0);//阻塞等待
        if(ret > 0) 
            printf("exit code: %d\n", WEXITSTATUS(status));
    }
    return 0;
}
```

## 2.6.debug

有一个小小的`bug`我们没有解决，就是使用`cd`命令的时候，没有办法切入到对应的目录，这是为什么呢？

# 3.shell拓展

## 3.1.用户名、主机名、目录获取

上面我们只是生硬显示了`user`充当用户名字，实际上我们可以通过环境变量获取执行`shell`的用户的名字，还可以获取在不同环境下的系统主机名字，使用这两个环境变量的内容填充到提示字符串中，目录也可以这样操作。

```cpp
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>
#include <sys/types.h> 
#include <string.h>
#include <stdlib.h>

#define NUM 1024
char cmd_line[NUM];//保存完整命令字符串

#define SIZE 32
char* g_argv[SIZE];//保存打散后的命令字符串，是一个数组

#define SEP " "//分隔符

const char* GetUserName()//获取用户名字
{
    const char* userName = getenv("USER");
    if(userName) 
        return userName;
    else
        return "none";
}

const char* GetHostName()//获取主机名字
{
    const char* hostName = getenv("HOSTNAME");
    if(hostName)
        return hostName;
    else
        return "none";
}

const char* GetPwd()//获取路径地址
{
    const char* pwd = getenv("PWD");
    if(pwd)
        return pwd;
    else
        return "none";
}

int main()
{
	while(1)
    {
        //1.打印提示信息
        printf("[%s@%s %s]$ ", GetUserName(), GetHostName(), GetPwd());
        fflush(stdout);
    
        //2.获取 user 的输入
        memset(cmd_line, '\0', sizeof(cmd_line));//设定初始值
        if(fgets(cmd_line, sizeof(cmd_line), stdin) == NULL)//获取字符串，不能使用 scanf()
        {
            //出错就直接跳出循环
            continue;
        }
        cmd_line[strlen(cmd_line) - 1] = '\0';//将用户输入的字符串去掉 \n

        //3.命令行字符解析
        //虽然可以自己写一个算法解析，但是我们可以使用一些现有的接口
        g_argv[0] = strtok(cmd_line, SEP);//第一次调用需要传入原始字符串和分隔符

        int index = 1;
        if(strcmp(g_argv[0], "ls") == 0)//如果输入的命令是 ls 就进行一些特殊处理
        {
            g_argv[index++] = "--color=auto";//增加颜色参数，让输出带有颜色
        }
        while(g_argv[index++] = strtok(NULL, SEP));//第二次还想要解析原始字符串，就需要传入 NULL

        //4.fork() 创建子进程，execvp() 替换子进程
        pid_t id = fork();
        if(id == 0)//child
        {
            printf("子进程 run:\n");
            execvp(g_argv[0], g_argv);//自动在环境变量中搜索 g_argv[0] 的文件，执行 g_argv 数组存储的指令
            exit(1);//没替换成功就会异常退出
        }
        //father
        int status;
        pid_t ret = waitpid(id, &status, 0);//阻塞等待
        if(ret > 0) 
            printf("exit code: %d\n", WEXITSTATUS(status));
    }
    return 0;
}
```

## 3.2.封装步骤

既然上面的用户名、主机名、路径都进行了函数封装，那么我也可以选择将部分的代码进行分钟，变得更有逻辑。

```cpp
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>
#include <sys/types.h> 
#include <string.h>
#include <stdlib.h>
#include <stdbool.h>

#define NUM 1024
char cmd_line[NUM];//保存完整命令字符串

#define SIZE 32
char* g_argv[SIZE];//保存打散后的命令字符串，是一个数组

#define SEP " "//分隔符

const char* GetUserName(void)//获取用户名字
{
    const char* userName = getenv("USER");
    if(userName) 
        return userName;
    else
        return "none";
}

const char* GetHostName(void)//获取主机名字
{
    const char* hostName = getenv("HOSTNAME");
    if(hostName)
        return hostName;
    else
        return "none";
}

const char* GetPwd(void)//获取路径地址
{
    const char* pwd = getenv("PWD");
    if(pwd)
        return pwd;
    else
        return "none";
}

void Print(void)
{
    printf("[%s@%s %s]$ ", GetUserName(), GetHostName(), GetPwd());
    fflush(stdout);
}

bool GetUserCommand()
{
    memset(cmd_line, '\0', sizeof(cmd_line));//设定初始值
    if(fgets(cmd_line, sizeof(cmd_line), stdin) == NULL)//获取字符串，不能使用 scanf()
    {
        //出错就直接跳出循环
        return false;
    }
    cmd_line[strlen(cmd_line) - 1] = '\0';//将用户输入的字符串去掉 \n
    return true;
}

void AnalyzeStr(void)
{
        //虽然可以自己写一个算法解析，但是我们可以使用一些现有的接口
        g_argv[0] = strtok(cmd_line, SEP);//第一次调用需要传入原始字符串和分隔符

        int index = 1;

        //处理特殊的命令情况
        if(strcmp(g_argv[0], "ls") == 0)//如果输入的命令是 ls 就进行一些特殊处理
        {
            g_argv[index++] = "--color=auto";//增加颜色参数，让输出带有颜色
        }

        while(g_argv[index++] = strtok(NULL, SEP));//第二次还想要解析原始字符串，就需要传入 NULL
}

void RunCommand(void)
{
    pid_t id = fork();
    if(id == 0)//child
    {
        printf("子进程 run:\n");
        execvp(g_argv[0], g_argv);//自动在环境变量中搜索 g_argv[0] 的文件，执行 g_argv 数组存储的指令
        exit(1);//没替换成功就会异常退出
    }
    else
    {
        //father
        int status;
        pid_t ret = waitpid(id, &status, 0);//阻塞等待
        if(ret > 0) 
        printf("exit code: %d\n", WEXITSTATUS(status));
    }
}

int main()
{
	while(1)
    {
        //1.打印提示信息
        Print();
    
        //2.获取 user 的输入
        if(!GetUserCommand())
            continue;

        //3.命令行字符解析
        AnalyzeStr();

        //4.fork() 创建子进程，execvp() 替换子进程
        RunCommand();
    }
    return 0;
}
```

## 3.3.debug

如果使用了上面的`shell`程序，很快就会发现两个`bug`。

### 3.3.1.无法直接输入换行

在系统默认运行的`shell`中，允许用户不断回车，而我们的程序如果直接回车就会退出，这个问题的解决思路很简单，只需要在获取字符的时候，查看用户输入的字符长度是否为`0`即可，若是就让`GetUserCommand()`返回`false`，执行`continue`语句即可。

```cpp
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>
#include <sys/types.h> 
#include <string.h>
#include <stdlib.h>
#include <stdbool.h>

#define NUM 1024
char cmd_line[NUM];//保存完整命令字符串

#define SIZE 32
char* g_argv[SIZE];//保存打散后的命令字符串，是一个数组

#define SEP " "//分隔符

const char* GetUserName(void)//获取用户名字
{
    const char* userName = getenv("USER");
    if(userName) 
        return userName;
    else
        return "none";
}

const char* GetHostName(void)//获取主机名字
{
    const char* hostName = getenv("HOSTNAME");
    if(hostName)
        return hostName;
    else
        return "none";
}

const char* GetPwd(void)//获取路径地址
{
    const char* pwd = getenv("PWD");
    if(pwd)
        return pwd;
    else
        return "none";
}

void Print(void)
{
    printf("[%s@%s %s]$ ", GetUserName(), GetHostName(), GetPwd());
    fflush(stdout);
}

bool GetUserCommand()
{
    memset(cmd_line, '\0', sizeof(cmd_line));//设定初始值
    if(fgets(cmd_line, sizeof(cmd_line), stdin) == NULL)//获取字符串，不能使用 scanf()
    {
        //出错就直接跳出循环
        return false;
    }
    cmd_line[strlen(cmd_line) - 1] = '\0';//将用户输入的字符串去掉 \n
    if(strlen(cmd_line) == 0)
        return false;
    return true;
}

void AnalyzeStr(void)
{
        //虽然可以自己写一个算法解析，但是我们可以使用一些现有的接口
        g_argv[0] = strtok(cmd_line, SEP);//第一次调用需要传入原始字符串和分隔符

        int index = 1;

        //处理特殊的命令情况
        if(strcmp(g_argv[0], "ls") == 0)//如果输入的命令是 ls 就进行一些特殊处理
        {
            g_argv[index++] = "--color=auto";//增加颜色参数，让输出带有颜色
        }

        while(g_argv[index++] = strtok(NULL, SEP));//第二次还想要解析原始字符串，就需要传入 NULL
}
void RunCommand(void)
{
    pid_t id = fork();
    if(id == 0)//child
    {
        printf("子进程 run:\n");
        execvp(g_argv[0], g_argv);//自动在环境变量中搜索 g_argv[0] 的文件，执行 g_argv 数组存储的指令
        exit(1);//没替换成功就会异常退出
    }
    else
    {
        //father
        int status;
        pid_t ret = waitpid(id, &status, 0);//阻塞等待
        if(ret > 0) 
        printf("exit code: %d\n", WEXITSTATUS(status));
    }
}

int main()
{
	while(1)
    {
        //1.打印提示信息
        Print();
    
        //2.获取 user 的输入
        if(!GetUserCommand())
            continue;

        //3.命令行字符解析
        AnalyzeStr();

        //4.fork() 创建子进程，execvp() 替换子进程
        RunCommand();
    }
    return 0;
}
```

### 3.3.2.无法切换对应目录

还可以发现一个比较严重的问题，我们无法使用`cd`命令切换目录，这是为什么呢？原因也很简单，`cd`程序替换的是子进程，该目录只让子进程进行了切换命令，而我们希望的是父进程自己执行`cd`，修改父进程的工作路径。因此，我们需要调用一些库函数，更改父进程的工作环境。

```cpp
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>
#include <sys/types.h> 
#include <string.h>
#include <stdlib.h>
#include <stdbool.h>

#define NUM 1024
char cmd_line[NUM];//保存完整命令字符串

#define SIZE 32
char* g_argv[SIZE];//保存打散后的命令字符串，是一个数组

#define SEP " "//分隔符

const char* GetUserName(void)//获取用户名字
{
    const char* userName = getenv("USER");
    if(userName) 
        return userName;
    else
        return "none";
}

const char* GetHostName(void)//获取主机名字
{
    const char* hostName = getenv("HOSTNAME");
    if(hostName)
        return hostName;
    else
        return "none";
}

const char* GetPwd(void)//获取路径地址
{
    const char* pwd = getenv("PWD");
    if(pwd)
        return pwd;
    else
        return "none";
}

void Print(void)//打印提示字符
{
    printf("[%s@%s %s]$ ", GetUserName(), GetHostName(), GetPwd());
    fflush(stdout);
}

bool GetUserCommand(void)//获取用户命令字符串
{
    memset(cmd_line, '\0', sizeof(cmd_line));//设定初始值
    if(fgets(cmd_line, sizeof(cmd_line), stdin) == NULL)//获取字符串，不能使用 scanf()
    {
        //出错就直接跳出循环
        return false;
    }
    cmd_line[strlen(cmd_line) - 1] = '\0';//将用户输入的字符串去掉 \n
    if(strlen(cmd_line) == 0)
        return false;
    return true;
}

void AnalyzeStr(void)//拆分解析用户字符串
{
    //虽然可以自己写一个算法解析，但是我们可以使用一些现有的接口
    g_argv[0] = strtok(cmd_line, SEP);//第一次调用需要传入原始字符串和分隔符

    int index = 1;

    //处理特殊的命令情况
    if(strcmp(g_argv[0], "ls") == 0)//如果输入的命令是 ls 就进行一些特殊处理
    {
        g_argv[index++] = "--color=auto";//增加颜色参数，让输出带有颜色
    }

    while(g_argv[index++] = strtok(NULL, SEP));//第二次还想要解析原始字符串，就需要传入 NULL
}

bool DoBuildin(void)//判断是否内建命令并且执行
{
    //内建命令
    if(strcmp(g_argv[0], "cd") == 0)       
    {   
        printf("父进程 run:\n");
        //更改路径的命令
        char* path = NULL;
        if(g_argv[1] == NULL)
        {
            path = ".";
        }
        else
        {
            path = g_argv[1];
        }
        chdir(path);
        return true;
    }
    return false;
}

void RunCommand(void)//运行用户命令
{
    pid_t id = fork();
    if(id == 0)//child
    {
        printf("子进程 run:\n");
        execvp(g_argv[0], g_argv);//自动在环境变量中搜索 g_argv[0] 的文件，执行 g_argv 数组存储的指令
        exit(1);//没替换成功就会异常退出
    }
    else
    {
        //father
        int status;
        pid_t ret = waitpid(id, &status, 0);//阻塞等待
        if(ret > 0) 
        printf("exit code: %d\n", WEXITSTATUS(status));
    }
}

int main()
{
	while(1)
    {
        //1.打印提示信息
        Print();
    
        //2.获取 user 的输入
        if(!GetUserCommand())
            continue;

        //3.命令行字符解析
        AnalyzeStr();

        //4.处理内建命令
        if(DoBuildin())
            continue;

        //5.fork() 创建子进程，execvp() 替换子进程
        RunCommand();
    }
    return 0;
}
```

这种只能由父进程来执行的命令也叫做“内建命令”，这类环境变量有很多。

### 3.3.3.无法更改环境变量

还有一个比较尴尬的问题，我们发现使用`cd`指令后，提示字符串的地址无法及时更新，也就是没有更新对应的环境变量。如果更新呢？可以使用系统提供的接口`getcwd()`来获取调用该接口进程所在的路径。

```cpp
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>
#include <sys/types.h> 
#include <string.h>
#include <stdlib.h>
#include <stdbool.h>

#define NUM 1024
char cmd_line[NUM];//保存完整命令字符串

#define SIZE 32
char* g_argv[SIZE];//保存打散后的命令字符串，是一个数组

#define SEP " "//分隔符

char cwd[1024];//设置pwd环境变量

const char* GetUserName(void)//获取用户名字
{
    const char* userName = getenv("USER");
    if(userName) 
        return userName;
    else
        return "none";
}

const char* GetHostName(void)//获取主机名字
{
    const char* hostName = getenv("HOSTNAME");
    if(hostName)
        return hostName;
    else
        return "none";
}

const char* GetPwd(void)//获取路径地址
{
    const char* pwd = getenv("PWD");
    if(pwd)
        return pwd;
    else
        return "none";
}

void Print(void)
{
    printf("[%s@%s %s]$ ", GetUserName(), GetHostName(), GetPwd());
    fflush(stdout);
}

bool GetUserCommand()
{
    memset(cmd_line, '\0', sizeof(cmd_line));//设定初始值
    if(fgets(cmd_line, sizeof(cmd_line), stdin) == NULL)//获取字符串，不能使用 scanf()
    {
        //出错就直接跳出循环
        return false;
    }
    cmd_line[strlen(cmd_line) - 1] = '\0';//将用户输入的字符串去掉 \n
    if(strlen(cmd_line) == 0)
        return false;
    return true;
}

void AnalyzeStr(void)
{
    //虽然可以自己写一个算法解析，但是我们可以使用一些现有的接口
    g_argv[0] = strtok(cmd_line, SEP);//第一次调用需要传入原始字符串和分隔符

    int index = 1;

    //处理特殊的命令情况
    if(strcmp(g_argv[0], "ls") == 0)//如果输入的命令是 ls 就进行一些特殊处理
    {
        g_argv[index++] = "--color=auto";//增加颜色参数，让输出带有颜色
    }

    while(g_argv[index++] = strtok(NULL, SEP));//第二次还想要解析原始字符串，就需要传入 NULL
}

bool DoBuildin()
{
    //内建命令
    if(strcmp(g_argv[0], "cd") == 0)       
    {   
        printf("父进程 run:\n");
        //更改路径的命令
        char* path = NULL;
        if(g_argv[1] == NULL)
        {
            path = ".";
        }
        else
        {
            path = g_argv[1];
        }
        chdir(path);
        
        char tmp[1024];
        getcwd(tmp, sizeof(tmp));//获取当前进程所在的地址（工作目录）
        sprintf(cwd, "PWD=%s", tmp);//组合环境变量
        putenv(cwd);//设置环境变量，必须使用全局的 cwd，如果直接将 tmp 传入，只是传了地址，而 tmp 变量出了该函数就会被销毁，到时候父进程就读取不到这个环境变量了，因此环境变量一般要设置为全局变量来存储
        return true;
    }
    return false;
}

void RunCommand(void)
{
    pid_t id = fork();
    if(id == 0)//child
    {
        printf("子进程 run:\n");
        execvp(g_argv[0], g_argv);//自动在环境变量中搜索 g_argv[0] 的文件，执行 g_argv 数组存储的指令
        exit(1);//没替换成功就会异常退出
    }
    else
    {
        //father
        int status;
        pid_t ret = waitpid(id, &status, 0);//阻塞等待
        if(ret > 0) 
        printf("exit code: %d\n", WEXITSTATUS(status));
    }
}

int main()
{
	while(1)
    {
        //1.打印提示信息
        Print();
    
        //2.获取 user 的输入
        if(!GetUserCommand())
            continue;

        //3.命令行字符解析
        AnalyzeStr();

        //4.处理内建命令
        if(DoBuildin())
            continue;

        //5.fork() 创建子进程，execvp() 替换子进程
        RunCommand();
    }
    return 0;
}
```

## 3.4.更多内建命令

我们还可以进一步实现更多的内建命令。

```cpp
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>
#include <sys/types.h> 
#include <string.h>
#include <stdlib.h>
#include <stdbool.h>

#define NUM 1024
char cmd_line[NUM];//保存完整命令字符串

#define SIZE 32
char* g_argv[SIZE];//保存打散后的命令字符串，是一个数组

#define SEP " "//分隔符

char cwd[1024];//设置pwd环境变量
char new_env[1024];//设置新增加的环境变量


const char* GetUserName(void)//获取用户名字
{
    const char* userName = getenv("USER");
    if(userName) 
        return userName;
    else
        return "none";
}

const char* GetHostName(void)//获取主机名字
{
    const char* hostName = getenv("HOSTNAME");
    if(hostName)
        return hostName;
    else
        return "none";
}

const char* GetPwd(void)//获取路径地址
{
    const char* pwd = getenv("PWD");
    if(pwd)
        return pwd;
    else
        return "none";
}

void Print(void)//打印提示信息
{
    printf("[%s@%s %s]$ ", GetUserName(), GetHostName(), GetPwd());
    fflush(stdout);
}

bool GetUserCommand()//获取 user 的输入
{
    memset(cmd_line, '\0', sizeof(cmd_line));//设定初始值
    if(fgets(cmd_line, sizeof(cmd_line), stdin) == NULL)//获取字符串，不能使用 scanf()
    {
        //出错就直接跳出循环
        return false;
    }
    cmd_line[strlen(cmd_line) - 1] = '\0';//将用户输入的字符串去掉 \n
    if(strlen(cmd_line) == 0)
        return false;
    return true;
}

void AnalyzeStr(void)//命令行字符解析
{
    //虽然可以自己写一个算法解析，但是我们可以使用一些现有的接口
    g_argv[0] = strtok(cmd_line, SEP);//第一次调用需要传入原始字符串和分隔符

    int index = 1;

    //处理特殊的命令情况
    if(strcmp(g_argv[0], "ls") == 0)//如果输入的命令是 ls 就进行一些特殊处理
    {
        g_argv[index++] = "--color=auto";//增加颜色参数，让输出带有颜色
    }

    while(g_argv[index++] = strtok(NULL, SEP));//第二次还想要解析原始字符串，就需要传入 NULL
}

bool DoBuildin()//处理内建命令
{
    //内建命令 cd
    if(strcmp(g_argv[0], "cd") == 0)   
    {   
        printf("父进程 run:\n");
        //更改路径的命令
        char* path = NULL;
        if(g_argv[1] == NULL)
        {
            path = ".";
        }
        else
        {
            path = g_argv[1];
        }
        chdir(path);
        
        char tmp[1024];
        getcwd(tmp, sizeof(tmp));//获取当前进程所在的地址（工作目录）
        sprintf(cwd, "PWD=%s", tmp);//组合环境变量
        putenv(cwd);//设置环境变量
        return true;
    }
    //内建命令 export
    if(strcmp(g_argv[0], "export") == 0)
    {
        if(g_argv[1] == NULL) return false;
        strcpy(new_env, g_argv[1]);
        putenv(new_env);
        return true;
    }

    return false;
}

void RunCommand(void)//fork() 创建子进程，execvp() 替换子进程
{
    pid_t id = fork();
    if(id == 0)//child
    {
        printf("子进程 run:\n");
        execvp(g_argv[0], g_argv);//自动在环境变量中搜索 g_argv[0] 的文件，执行 g_argv 数组存储的指令
        exit(1);//没替换成功就会异常退出
    }
    else
    {
        //father
        int status;
        pid_t ret = waitpid(id, &status, 0);//阻塞等待
        if(ret > 0) 
        printf("exit code: %d\n", WEXITSTATUS(status));
    }
}

int main()
{
	while(1)
    {
        //1.打印提示信息
        Print();
    
        //2.获取 user 的输入
        if(!GetUserCommand())
            continue;

        //3.命令行字符解析
        AnalyzeStr();

        //4.处理内建命令
        if(DoBuildin())
            continue;

        //5.fork() 创建子进程，execvp() 替换子进程
        RunCommand();
    }
    return 0;
}
```

## 3.5.用echo打印最近一次返回值

首先我们知道`echo`有多种个用法，一是单纯打印到显示屏，二是打印环境变量和打印最近一次进程调用的返回值。我们可以将`echo`作为内置命令，查看其是否有为第二种特殊用法，是就作为内建命令处理，不是就交给父进程做程序替换即可。

```cpp
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>
#include <sys/types.h> 
#include <string.h>
#include <stdlib.h>
#include <stdbool.h>

#define NUM 1024
char cmd_line[NUM];//保存完整命令字符串

#define SIZE 32
char* g_argv[SIZE];//保存打散后的命令字符串，是一个数组

#define SEP " "//分隔符

char cwd[1024];//设置pwd环境变量
char new_env[1024];//设置新增加的环境变量
int lats_code = 0;//最近一次

const char* GetUserName(void)//获取用户名字
{
    const char* userName = getenv("USER");
    if(userName) 
        return userName;
    else
        return "none";
}

const char* GetHostName(void)//获取主机名字
{
    const char* hostName = getenv("HOSTNAME");
    if(hostName)
        return hostName;
    else
        return "none";
}

const char* GetPwd(void)//获取路径地址
{
    const char* pwd = getenv("PWD");
    if(pwd)
        return pwd;
    else
        return "none";
}

void Print(void)//打印提示信息
{
    printf("[%s@%s %s]$ ", GetUserName(), GetHostName(), GetPwd());
    fflush(stdout);
}

bool GetUserCommand()//获取 user 的输入
{
    memset(cmd_line, '\0', sizeof(cmd_line));//设定初始值
    if(fgets(cmd_line, sizeof(cmd_line), stdin) == NULL)//获取字符串，不能使用 scanf()
    {
        //出错就直接跳出循环
        return false;
    }
    cmd_line[strlen(cmd_line) - 1] = '\0';//将用户输入的字符串去掉 \n
    if(strlen(cmd_line) == 0)
        return false;
    return true;
}

void AnalyzeStr(void)//命令行字符解析
{
    //虽然可以自己写一个算法解析，但是我们可以使用一些现有的接口
    g_argv[0] = strtok(cmd_line, SEP);//第一次调用需要传入原始字符串和分隔符

    int index = 1;

    //处理特殊的命令情况
    if(strcmp(g_argv[0], "ls") == 0)//如果输入的命令是 ls 就进行一些特殊处理
    {
        g_argv[index++] = "--color=auto";//增加颜色参数，让输出带有颜色
    }

    while(g_argv[index++] = strtok(NULL, SEP));//第二次还想要解析原始字符串，就需要传入 NULL
}

bool DoBuildin()//处理内建命令
{
    //内建命令 cd
    if(strcmp(g_argv[0], "cd") == 0)   
    {   
        printf("父进程 run:\n");
        //更改路径的命令
        char* path = NULL;
        if(g_argv[1] == NULL)
        {
            path = ".";
        }
        else
        {
            path = g_argv[1];
        }
        chdir(path);
        
        char tmp[1024];
        getcwd(tmp, sizeof(tmp));//获取当前进程所在的地址（工作目录）
        sprintf(cwd, "PWD=%s", tmp);//组合环境变量
        putenv(cwd);//设置环境变量
        return true;
    }
    //内建命令 export
    if(strcmp(g_argv[0], "export") == 0)
    {
        if(g_argv[1] == NULL) return false;
        strcpy(new_env, g_argv[1]);
        putenv(new_env);
        return true;
    }
    //内建命令 echo
    if(strcmp(g_argv[0], "echo") == 0)
    {
        if(*g_argv[1] == '$')//特殊命令（查找环境变量和打印最近一次进程的返回值）
        {
            char* val = g_argv[1] + 1;//这个指针指向的就是 $PATH 或者 $? 中的 PATH 和 ?
            
            if(strcmp(val, "?") == 0)//查看退出码
            {
                printf("%d\n", lats_code);
                lats_code = 0;
            }
            else//查看环境变量
            {
                printf("%s\n", getenv(val));
            }
        }
        else//一般命令（如果不是特殊的命令就直接退出交给后续的程序替换即可）
        {
            return false;
        }
        return true;
    }
    return false;
}

void RunCommand(void)//fork() 创建子进程，execvp() 替换子进程
{
    pid_t id = fork();
    if(id == 0)//child
    {
        printf("子进程 run:\n");
        execvp(g_argv[0], g_argv);//自动在环境变量中搜索 g_argv[0] 的文件，执行 g_argv 数组存储的指令
        exit(1);//没替换成功就会异常退出
    }
    else
    {
        //father
        int status = 0;
        pid_t ret = waitpid(id, &status, 0);//阻塞等待
        if(ret > 0) 
        {
            lats_code = WEXITSTATUS(status);//最近一次运行子进程的退出码
        }
    }
}

int main()
{
	while(1)
    {
        //1.打印提示信息
        Print();
    
        //2.获取 user 的输入
        if(!GetUserCommand())
            continue;

        //3.命令行字符解析
        AnalyzeStr();

        //4.处理内建命令
        if(DoBuildin())
            continue;

        //5.fork() 创建子进程，execvp() 替换子进程
        RunCommand();
    }
    return 0;
}
```
