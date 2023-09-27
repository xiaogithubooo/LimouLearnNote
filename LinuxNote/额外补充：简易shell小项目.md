# 7.简易shell

利用我们当前的知识可以撰写一个简单的`shell`外壳程序。

```c
//myshell.c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>
#include <sys/types.h>
#define NUM 1024
#define SIZE 32
#define SEP " "
char cmd_line[NUM];//保存完整命令字符串
char* g_argv[SIZE];//保存打散后的命令字符串
int main()
{
    while(1)
    {
        //1.打印提示信息
        printf("[user@myshell]$ ");
        fflush(stdout);
        memset(cmd_line, '\0', sizeof(cmd_line));
        //2.获取user的输入
        if(fgets(cmd_line, sizeof(cmd_line), stdin) == NULL)
        {
            //出错处理
            continue;
        }
        cmd_line[strlen(cmd_line) - 1] = '\0';
        //3.命令行字符解析
        //虽然可以自己写一个算法解析，但是我们可以一些现有的接口
        g_argv[0] = strtok(cmd_line, SEP);//第一次调用需要传入原始字符串

        int index = 1;
        if(strcmp(g_argv[0], "ls") == 0)
        {
            g_argv[index++] = "--color=auto";
        }
        while(g_argv[index++] = strtok(NULL, SEP));//第二次还想要解析原始字符串，就需要传入NULL
        //4.TODO                                                
        //需要判断命令，如果是cd这样的命令不能使用子进程
        if(strcmp(g_argv[0], "cd") == 0)       
        {   
            printf("下面功能让父进程来\n");
            //更改路径的命令
            if(g_argv[1] != NULL) chdir(g_argv[1]);
            continue;
        }  
        //5.fork()
        pid_t id = fork();
        if(id == 0)//child
        {
            printf("子进程run：\n");
            execvp(g_argv[0], g_argv);
            exit(1);
        }
        //father
        int status;
        pid_t ret = waitpid(id, &status, 0);
        if(ret > 0) printf("exit code: %d\n", WEXITSTATUS(status));
    }
    return 0;
}
```

