# 1.gets()和puts()（尽量扫少用）

## 1.1.char\* gets(char\*)

`gets()`读取整行输入，直到遇到`\n`停止，并且丢弃`\n`不存储在缓冲区。在字符的结尾加上`\0`，使之成为`C字符串`。

> 使用注意：该函数无法检查是否有足够的空间存储，因此有可能导致缓冲区溢出的问题（多余的字符超出指定的目标空间）。该函数在`C99`中被弃用，在`C11`标准中被废除。

## 1.2.char\* puts(char\*)

`puts()`在输出字符串的同时会自动加上`\n`。

```c
char words[5];
gets(words);
puts(words;)
```

# 2.fgets()和fputs()（推荐使用）

## 2.1.int fgets(const char\* str, int count, FILE\* stream)

函数通过第二个参数来限制输入解决溢出问题，若第二个参数指明为`n`，则输入`n-1`个字符便停止（或者遇到`\n`停止，但是会存储`\n`而不会丢弃），停止后自动加上`\0`。并且第三个参数需要指明读入的文件。另外读到末尾就返回`NULL`，读失败返回`EOF`。

> 使用注意：
> 
> 1. 标准对于`count <= 1`的情况表述不明，因此常见的实现是：某些实现不做任何事并且报告错误、其他实现不读内容存储`0`于`str[0]`并且报告成功
> 
> 2. 如果不想要自动存储换行符，一种已有的办法就是检查字符数组存储`\n`的地方，将其替换成`\0`
>    
>    ```c
>    while(words[i++] != '\n');
>    words[i] = '\0';
>    ```
> 
> 3. 但是如果超出字符数组存储的范围了，就需要我们手动清理多余的字符
>    
>    ```c
>    while(getchar() != '\n');
>    ```

## 2.2.int fputs(const char\* str, FILE\* stream)

这个函数的第二个参数需要指明写入的文件，虽然不会自动换行，但是可以根据`fgets()`获取到的`\n`来换行。

```c
char words1[5];
fgets(words1, 5, stdin);
fputs(words1, stdout);

char words2[6];
while(fgets(words, 6, stdin) != NULL && words[0] != '\n')//后一个子条件是结束条件
{
    int i = 0;
    while(words2[i] != '\n' && words2[i] != '\n')//这里两种情况：一是读取有限字符串后回车；二是读取了部分字符串于数组中，填满的同时还有其他无效字符
    {
        i++;
    }//找到上述两种情况之一的最终下标

    if(words2[i] == '\n')//情况1
    {
        words2[i] = '\0';
    }
    else//情况2
    {
        while(getchar() != '\n');
    }
    puts();
}
puts("done");
```

# 3.gets_s（可选拓展）

函数原型为`char* gets_s(char* str, rsize_t n)`

`gets_s()`默认从标准输入中读取数据，因此对比`fgets()`来说无需第二个参数。读取到换行符会对其抛弃并且不计入缓冲区。如果读取到最大字符数都没有读取到`\n`，首先会把目标数组中的首字符设置为`\0`，读取并丢弃随后的输入直至读到`\n`或者文件结尾，然后返回`NULL`，接着调用依赖实现的“处理函数”（或者你选择的其他函数），可能会终止或者退出程序。

> 使用注意：虽然该函数在`C11`被选出，但是最终只是可选拓张，其使用不如`fgets()`灵活

```c
char words[5];
gets_s(words, 5);//输入字符串大小为4就没问题（不计入\0），如果超出就会有问题，有的编译器直接终止程序或者崩溃
fputs(words, stdout);
```

# 4.scanf()和printf()

这个大家估计比较熟悉就不再赘述，就是需要注意`scanf()`会在某些情况将未读取的字符放回缓冲区，如果不重视和其他`IO`函数混用有可能造成很多意想不到的结果。

# 5.sscanf()和sprintf()

## 5.1.int sscanf(const char\* buffer, const char\* format, ...)

从`buffer`中（而不是`stdin`）读取输入，输入到后面的变量指针列表中。

## 5.2.int sprintf(char\* buffer, const char\* format, ...)

将后面的变量列表输出到`buffer`中（而不是`stdout`）。

# 6.fscanf()和fprintf()

使用方法和`sscanf()`与`sprintf()`很类似，只不过多了一个配合文件读和写的操作。

## 6.1.int fscanf(FILE\* stream, const char\* format, ...)

略。

## 6.2.int fprintf(FILE\* stream, const char\* format, ...)

略。

# 7.判断终止和异常

这个有点复杂，之后再写。
