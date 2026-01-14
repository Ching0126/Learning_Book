## 47P-系统编程阶段说在前面的话

系统调用（系统函数）	内核提供的函数

库调用				程序库中的函数

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631153107363-8a6c6528-414a-4b29-b7a6-a2fa3feaf44c.png)

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631153498026-c0dec3bc-208b-4e0d-86d9-c1f94441ff99.png)

## 操作系统 os

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1627880703721-b83a9f40-3327-4f6b-88ba-1133c8aab87c.png)

操作系统：管理计算机硬件和软件的计算机程序 作用于硬件之上应用软件之下

- os属于系统软件 能够管理应用软件，并且给应用软件提供服务
- 管理与配置内存、决定系统资源供需的优先次序、控制输入设备与输出设备、操作网络与管理文件系统等



内核+shell + 系统应用(vim) ... = os



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1627880638264-338750b7-0090-43be-98ea-d72425e887c4.png)



## 系统编程 syscall

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1627881235419-afa5e0ce-a44c-4b36-9311-df21aad62d1c.png)

系统编程的特点

- 无法跨平台
- 速度更慢：空间切换需要一定时间
- 提高系统的安全性：只能够一一定规则让内核实现相应的操作
- 接口复杂：能力越大责任越大

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1627886103313-3dd63efd-9d63-4b3a-a9e9-d0b4ce37ded1.png)

## 命令行解析函数 getopt()



getopt()函数

```
int getopt( int argc, char *const argv[], const char *optstring);
```



- `argc、argv`：由main函数的参数直接传递而来
- `optstring` ：一个包含【准确合法】的选项字符的字符串
- 返回值：返回这个选项字符，没有参数的最后一次调用返回-1

- 如果有未知选项或选项未加参数的情况，返回字符 `?` 
- 如果optsting的第一个字符为 `:` ，则选项未加参数的情况下返回字符 `:` 





`optstring` ：一个由所有合法的“可选字符”所组成的字符串

- 单个字符串，表示选项
- 单个字符后面接一个 `:` ，表示该选项后面必须接一个参数。参数紧跟在选项后面或者空格隔开。该参数的指针赋给optarg
- 单个字符后面接两个 `::` ，表示该选项后面可以带参数也可以不带参数，但是参数必须紧跟在选项后面不能以空格隔开。同上，该参数的指针赋给optarg



样例 `ab:c::` 

- a选项没有参数

- `-a` 

- b选项有一个参数，可以紧跟在b后也可以有空格间隔

- `-boptarg` 
- `-b optarg` 

- c选项有或者没有参数，如果有参数必须直接接参数

- `-c` 
- `-coptarg` 



getopt设置的一些全局变量



- `char *optarg` 指向当前参数选项（如果有）的指针
- `int ``optind` 再次调用getopt()时的下一个argv指针的索引号
- `int optopt` 最后一个未知选项
- `int opterr` 这个变量非零时，向stdeer打印错误。默认为1



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1627889266335-79aac0a6-e7bb-4ee1-b3bb-d461aefaa54b.png)

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1627893732726-1c4575aa-c173-40cf-9ed2-7fb063634c4c.png)

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1627893741215-6abc815e-a54b-4906-8af2-b1c00a6bc39f.png)



getopt函数的通用写法

```c
#include<stdio.h>
#include<unistd.h>

int main(int argc, char *argv[]) {
    char c;
    while ((c = getopt(argc, argv, ":ab:c::")) != -1) {//“ab:c::”; "+ab:c::"; "-ab:c::"
        switch (c) {
            case 'a' :
                printf("the option is [%c], the optind is  %d, tne optarg [%s]\n", c, optind, optarg);
                break;
            case 'b' :
                printf("the option is [%c], the optind is  %d, tne optarg [%s]\n", c, optind, optarg);
                break;
            case 'c' :
                printf("the option is [%c], the optind is  %d, tne optarg [%s]\n", c, optind, optarg);
                break;
            default :
                printf("the option is illegal\n");
                break;
        }
    }
    return 0;
}
```



## 48P-open函数



open函数

`#include <unistd.h>` ：包含man里面系统头文件



```
int open(char *pathname, int flags)
```

参数：

​	pathname: 欲打开的文件路径名

​	flags：文件打开方式：	#include <fcntl.h>

返回值：

​     成功： 打开文件所得到对应的 文件描述符（整数）

​	    失败： -1， 设置errno	



`int open(char *pathname, int flags， mode_t mode)`		123  775	

参数：

​	pathname: 欲打开的文件路径名

​	flags：文件打开方式：	

​     O_RDONLY|O_WRONLY|O_RDWR	

​     O_CREAT|O_APPEND|O_TRUNC|O_EXCL|O_NONBLOCK ....

​	mode: 参数3使用的前提， 参2指定了 O_CREAT。	

​     取值8进制数，用来描述文件的 访问权限。 rwx    0664

​		    创建文件最终权限 = mode & ~umask

返回值：

​	成功： 打开文件所得到对应的 文件描述符（整数）

​	失败： -1， 设置errno	

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631156145724-1b1f13cc-f5b8-4fd9-8f9d-babb6fd8426e.png)



close函数

​	`int close(int fd);`



strerror函数

```
char *strerror(int errnum);
```



## 49P-总结

## 50P-复习

# 四



## 51P-makefile作业

将当前目录下所有.c文件编译为可执行程序

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631170475913-8336ad97-d0e9-4f6b-9387-a0de03ade0ad.png)



## 52P-read和write实现cp

read函数

```
ssize_t read(int fd, void *buf, size_t count);
```

参数：

​	fd：文件描述符

​	buf：存数据的缓冲区

​	count：缓冲区大小

返回值：

​	0：		读到文件末尾。

​	成功；	> 0 读到的字节数。

​	失败：	-1， 设置 errno

​	-1： 	并且 errno = EAGIN 或 EWOULDBLOCK, 说明不是read失败，而是read在以非阻塞方式读一个设备文件（网络文件），并且文件无数据。



write函数

```
ssize_t write(int fd, **const** void *buf, size_t count);
```

参数：

​	fd：文件描述符

​	buf：待写出数据的缓冲区

​	count：数据大小

返回值：

​	成功：写入的字节数。

​	失败：-1， 设置 errno



练习

用read和write实现一个copy函数

用read/write实现的copy和fgetc/fputc实现的copy对比

```c
#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
#include <stdlib.h>

//./a.out file file1
int main(int argc, char **argv) 
{
    //格式判断
    if (argc != 3) {
        fprintf(stderr, "Usage: ./%s file file1\n", argv[0]);
        exit(1);
    }

    //打开文件
    int fd = open(argv[1], O_RDONLY);
    if (fd < 0) {
        perror("open");
        exit(1);
    }
    int fd1 = open(argv[2], O_WRONLY | O_CREAT | O_TRUNC, 0666);
    if (fd1 < 0) {
        perror("open1");
        exit(1);
    }

    //拷贝
    int size;
    char buff[1024] = {0};
    while ((size = read(fd, buff, 1024)) > 0) {
        printf("%d\n", size);
        write(fd1, buff, size);
    }

    //关闭文件
    close(fd);
    close(fd1);
    return 0;
}
```

## 53P-系统调用和库函数比较—预读入缓输出

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631171710899-21736f8a-08ca-45ac-ad80-b75e4503ae7a.png)



fputc/fgetc实现

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508123411-74e3e2a6-8359-4e64-92f3-5c60051cbb06.png)

read/write实现

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631174265546-5cdbaed9-a8cb-4032-a018-aa06c63b9609.png)

```c
$strace ./a.out
结果表明
read/write速度慢
```

原因分析：

- read/write这块，每次写一个字节，会疯狂进行内核态和用户态的切换，所以非常耗时。
- fgetc/fputc，有个缓冲区，4096，所以它并不是一个字节一个字节地写，内核和用户切换就比较少

预读入，缓输出机制。所以系统函数并不是一定比库函数牛逼，能使用库函数的地方就使用库函数。

- 标准IO函数自带用户缓冲区，系统调用无用户级缓冲。系统缓冲区是都有的。

## 54P-文件描述符

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1628409681136-7baaea92-39db-4a99-a76f-c5887a2385f9.png)



文件描述符是指向一个文件结构体的指针

PCB进程控制块：本质 结构体。



​	成员：文件描述符表。

​	文件描述符：0/1/2/3/4。。。。/1023     表中可用的最小的。

​	0 - STDIN_FILENO		键盘

​	1 - STDOUT_FILENO	显示器

​	2 - STDERR_FILENO



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631175574561-0518c0d4-1ec3-4a84-9952-0be1333c8430.png)

## 55P-阻塞和非阻塞



产生阻塞的场景：读设备文件。读网络文件的属性。（读常规文件无阻塞概念。）

​	/dev/tty -- 终端文件。

​	open("/dev/tty", O_RDWR | O_NONBLOCK)	--- 设置 /dev/tty 非阻塞状态。(默认为阻塞状态)



更改非阻塞读取终端——超时设置

```c
#include <unistd.h>  
#include <fcntl.h>  
#include <stdlib.h>  
#include <stdio.h>  
#include <errno.h>  
#include <string.h>  
  
#define MSG_TRY "try again\n"  
#define MSG_TIMEOUT "time out\n"  
  
int main(void)  
{  
    //打开文件
    int fd, n, i;  
    fd = open("/dev/tty", O_RDONLY | O_NONBLOCK);  
    if(fd < 0){  
        perror("open /dev/tty");  
        exit(1);  
    }  
    printf("open /dev/tty ok... %d\n", fd);  
  	
    //轮询读取
    char buf[10];  
    for (i = 0; i < 5; i++){  
        n = read(fd, buf, 10);  
        if (n > 0) {                    //说明读到了东西  
            break;  
        }  
        if (errno != EAGAIN) {          //EWOULDBLOCK    
            perror("read /dev/tty");  
            exit(1);  
        } else {  
            write(STDOUT_FILENO, MSG_TRY, strlen(MSG_TRY));  
            sleep(2);  
        }  
    }  
  	//超时判断
    if (i == 5) {  
        write(STDOUT_FILENO, MSG_TIMEOUT, strlen(MSG_TIMEOUT));  
    } else {  
        write(STDOUT_FILENO, buf, n);  
    }  
  
    //关闭文件
    close(fd);  
    return 0;  
}  
```

## 56-fcntl改文件属性



- fcntl用来改变一个【已经打开】的文件的 访问控制属性
- 重点掌握两个参数的使用， F_GETFL，F_SETFL



fcntl函数

```
int (int fd, int cmd, ...)
```

参数：

fd		文件描述符

cmd		命令，决定了后续参数个数

获取文件状态： F_GETFL

设置文件状态： F_SETFL

返回值：

int flgs = fcntl(fd,  F_GETFL);

flgs |= O_NONBLOCK

fcntl(fd,  F_SETFL, flgs);

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1628266139925-ca5c0ec0-6090-4d0a-a1ab-2b82c815b299.png)

终端文件默认是阻塞读的，这里用fcntl将其更改为非阻塞读

```c
#include <unistd.h>  
#include <fcntl.h>  
#include <errno.h>  
#include <stdio.h>  
#include <stdlib.h>  
#include <string.h>  
  
#define MSG_TRY "try again\n"  
  
int main(void)  
{  
    char buf[10];  
    int flags, n;  
  
    flags = fcntl(STDIN_FILENO, F_GETFL); //获取stdin属性信息  
    if(flags == -1){  
        perror("fcntl error");  
        exit(1);  
    }  
    flags |= O_NONBLOCK;  
    int ret = fcntl(STDIN_FILENO, F_SETFL, flags);  
    if(ret == -1){  
        perror("fcntl error");  
        exit(1);  
    }  
  
tryagain:  
    n = read(STDIN_FILENO, buf, 10);  
    if(n < 0){  
        if(errno != EAGAIN){          
            perror("read /dev/tty");  
            exit(1);  
        }  
        sleep(3);  
        write(STDOUT_FILENO, MSG_TRY, strlen(MSG_TRY));  
        goto tryagain;  
    }  
    write(STDOUT_FILENO, buf, n);  
  
    return 0;  
}  
```

## 57P-午后回顾

## 58P-lseek函数

lseek函数

```
off_t lseek(int fd, off_t offset, int whence);
```

参数：

​	fd：文件描述符

​	offset： 偏移量，就是将读写指针从whence指定位置向后偏移offset个单位

​	whence：起始偏移位置 

SEEK_SET / SEEK_CUR / SEEK_END

返回值：

​	成功：较起始位置偏移量

​	失败：-1 errno

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631180874219-55047b42-7ffd-4b4f-a3cb-74e9752136da.png)



lseek示例，写一个句子到空白文件，完事调整光标位置，读取刚才写那个文件。

这个示例中，如果不调整光标位置，是读取不到内容的，因为读写指针在内容的末尾

```c
#include <stdio.h>  
#include <stdlib.h>  
#include <unistd.h>  
#include <string.h>  
#include <fcntl.h>  
  
int main(void)  
{  
    int fd, n;  
    char msg[] = "It's a test for lseek\n";  
    char ch;  
  
    fd = open("lseek.txt", O_RDWR|O_CREAT, 0644);  
    if(fd < 0){  
        perror("open lseek.txt error");  
        exit(1);  
    }  
  
    write(fd, msg, strlen(msg));    //使用fd对打开的文件进行写操作，问价读写位置位于文件结尾处。  
  
    lseek(fd, 0, SEEK_SET);         //修改文件读写指针位置，位于文件开头。 注释该行会怎样呢？  
  
    while((n = read(fd, &ch, 1))){  
        if(n < 0){  
            perror("read error");  
            exit(1);  
        }  
        write(STDOUT_FILENO, &ch, n);   //将文件内容按字节读出，写出到屏幕  
    }  
  
    close(fd);  
  
    return 0;  
}  
```

应用场景：	

​	1. 文件的“读”、“写”使用同一偏移位置。

​	2. 使用lseek获取文件大小（返回值接收）

​	3. 使用lseek拓展文件大小：要想使文件大小真正拓展，必须【引起IO操作】。		



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508128959-90802f57-2ae2-4369-825b-fa4ce603bfe5.png)



简单小结一下：

- 对于写文件再读取那个例子，由于文件写完之后未关闭，读写指针在文件末尾，所以不调节指针，直接读取不到内容。
- lseek读取的文件大小总是相对文件头部而言。
- 用lseek读取文件大小实际用的是读写指针初末位置的偏移差，一个新开文件，读写指针初位置都在文件开头。如果用这个来扩展文件大小，必须引起IO才行，于是就至少要写入一个字符。上面代码出现lseek返回799，ls查看为800的原因是，lseek读取到偏移差的时候，还没有写入最后的‘$’符号，末尾那一大堆^@，是文件空洞，如果自己写进去的也想保持队形，就写入“\0”。



truncate函数

直接拓展文件。	

```
int ret = truncate("dict.cp", 250);
```

## 59P-传入传出参数

传入参数：

​	1. 指针作为函数参数。

​	2. 同常有const关键字修饰。

​	3. 指针指向有效区域， 在函数内部做读操作。



传出参数：

​	1. 指针作为函数参数。

​	2. 在函数调用之前，指针指向的空间可以无意义，但必须有效。

​	3. 在函数内部，做写操作。

​	4。函数调用结束后，充当函数返回值。



传入传出参数：

​	1. 指针作为函数参数。

​	2. 在函数调用之前，指针指向的空间有实际意义。

​	3. 在函数内部，先做读操作，后做写操作。

​	4. 函数调用结束后，充当函数返回值。



## 60P-目录项和inode

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1628268741155-09aed6b1-d80a-489f-80f7-8ae1f91489a2.png)



一个文件主要由两部分组成，dentry(目录项)和inode



inode本质是结构体，存储文件的属性信息，如：权限、类型、大小、时间、用户、盘快位置…

也叫做文件属性管理结构，大多数的inode都存储在磁盘上。



少量常用、近期使用的inode会被缓存到内存中。



所谓的删除文件，就是删除inode，但是数据其实还是在硬盘上，以后会覆盖掉。

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1628486265456-7f4c7cd4-6ddb-494d-893a-3526f6561f75.png)

## 61P-stat函数

获取文件属性，（从inode结构体中获取）

stat/lstat 函数

```
int stat(const char *path, struct stat *buf);
```

参数：

​	path： 文件路径

​	buf：（传出参数） 存放文件属性，inode结构体指针。

返回值：

​	成功： 0

​	失败： -1 errno

​	

获取文件大小： buf.st_size

获取文件类型： buf.st_mode

获取文件权限： buf.st_mode

符号穿透：stat会。lstat不会。



下面这个例子是获取文件大小的正规军解法，用stat：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508130379-1dde2d73-ae2a-475f-858b-620e9de214ec.png)

## 62P-lstat和stat



lstat查看文件类型

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631182504968-e848a8bd-b307-4d27-a75c-29fe93c7ee43.png)



stat会拿到符号链接指向那个文件或目录的属性。

类似穿透现象还有 cat vim（实现基于系统调用）

不想穿透符号就用 lstat



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631183094312-686fedb3-6dae-40b8-9fab-091169cb0a8b.png)

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631183473625-c89465dc-a0af-47d4-94af-a4855cb5bbbb.png)

## 63P-传出参数当返回值



其实就是传入参数在函数中被改变，可以用来传出。而且传出参数数量不限，比单纯的返回值更灵活。



## 64P-link和unlink隐式回收

硬链接数就是dentry数目

link就是用来创建硬链接的

link可以用来实现mv命令



link函数

```
int link(const char *oldpath, const char *newpath);
```

- 用这个来实现mv，用oldpath来创建newpath，完事儿删除oldpath就行。



unlink函数

删除一个链接  

```
int unlink(const char *pathname);
```

- unlink是删除一个文件的目录项dentry，使【硬链接数-1】
- unlink函数的特征：清除文件时，如果文件的硬链接数到0了，没有dentry对应，但该文件仍不会马上被释放，要等到所有打开文件的进程关闭该文件，系统才会挑时间将该文件释放掉。



下面用一段代码来验证unlink是删除dentry：

```c
/* 
 *unlink函数是删除一个dentry 
 */  
#include <unistd.h>  
#include <fcntl.h>  
#include <stdlib.h>  
#include <string.h>  
#include <stdio.h>  
  
  
int main(void)  
{  
    int fd, ret;  
    char *p = "test of unlink\n";  
    char *p2 = "after write something.\n";  
  
    fd = open("temp.txt", O_RDWR|O_CREAT|O_TRUNC, 0644);  
    if(fd < 0){  
        perror("open temp error");  
        exit(1);  
    }  
  
    ret = write(fd, p, strlen(p));  
    if (ret == -1) {  
        perror("-----write error");  
    }  
  
    printf("hi! I'm printf\n");  
    ret = write(fd, p2, strlen(p2));  
    if (ret == -1) {  
        perror("-----write error");  
    }  
  
    printf("Enter anykey continue\n");  
    getchar();  
  
    ret = unlink("temp.txt");        //具备了被释放的条件  
    if(ret < 0){  
        perror("unlink error");  
        exit(1);  
    }  
  
    close(fd);  
  
    return 0;  
}  
```





编译程序并运行，程序阻塞，此时打开新终端查看临时文件temp.c如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508132407-f3af6c5b-bbcf-4e54-827b-657217a9d465.png)

可以看到，临时文件没有被删除，这是因为当前进程没结束。

输入字符使当前进程结束后，temp.txt就不见了

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508132780-9226377d-3f99-4e4b-84a1-45e55217acb4.png)



下面开始搞事，在程序中加入段错误成分，段错误在unlink之前，由于发生段错误，程序后续删除temp.txt的dentry部分就不会再执行，temp.txt就保留了下来，这是不科学的。



解决办法是检测fd有效性后，立即释放temp.txt，由于进程未结束，虽然temp.txt的硬链接数已经为0，但还不会立即释放，仍然存在，要等到程序执行完才会释放。这样就能避免程序出错导致临时文件保留下来。

因为文件创建后，硬链接数立马减为0，即使程序异常退出，这个文件也会被清理掉。这时候的内容是写在内核空间的缓冲区。



修改后代码如下：

```c
/* 
 *unlink函数是删除一个dentry 
 */  
#include <unistd.h>  
#include <fcntl.h>  
#include <stdlib.h>  
#include <string.h>  
#include <stdio.h>  
  
  
int main(void) {
    int fd, ret;  
    char *p = "test of unlink\n";  
    char *p2 = "after write something.\n";  
  
    fd = open("temp.txt", O_RDWR|O_CREAT|O_TRUNC, 0644);  
    if(fd < 0){  
        perror("open temp error");  
        exit(1);  
    }  
  
    ret = unlink("temp.txt");        //具备了被释放的条件  
    if(ret < 0){  
        perror("unlink error");  
        exit(1);  
    }  
  
    ret = write(fd, p, strlen(p));  
    if (ret == -1) {  
        perror("-----write error");  
    }  
  
    printf("hi! I'm printf\n");  
    ret = write(fd, p2, strlen(p2));  
    if (ret == -1) {  
        perror("-----write error");  
    }  
  
    printf("Enter anykey continue\n");  
    getchar();  
  
    close(fd);  
  
    return 0;  
}  
```



隐式回收：

当进程结束运行时，所有进程打开的文件会被关闭，申请的内存空间会被释放。系统的这一特性称之为隐式回收系统资源。



比如上面那个程序，要是没有在程序中关闭文件描述符，没有隐式回收的话，这个文件描述符会保留，多次出现这种情况会导致系统文件描述符耗尽。所以隐式回收会在程序结束时收回它打开的文件使用的文件描述符。



## 65P-文件目录rwx权限差异



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508133104-073f878d-0549-41ad-b4de-f5a32f4c35ed.png)

- vi 目录 会得到目录项的列表

## 66P-目录操作函数



目录操作函数

```
DIR * opendir(char *name);
int closedir(DIR *dp);
struct dirent *readdir(DIR * dp);
```



struct dirent {

​	inode

​	char dname[256];

}



没有写目录操作，因为目录写操作就是创建文件。可以用touch



练习

实现一个ls -a 操作

实现一个ls -l 操作

## 67P-总结

## 68P-复习

应用程序的系统调用过程

应用程序->标库函数->系统调用->驱动->硬件





# 五

## 69P-递归遍历目录思路分析

任务需求：使用opendir	closedir	readdir	stat实现一个递归遍历目录的程序

输入一个指定目录，默认为当前目录。递归列出目录中的文件，同时显示文件大小。



思路分析

递归遍历目录：ls-R.c

​	1. 判断命令行参数，获取用户要查询的目录名。	int argc, char *argv[1]

​		argc == 1  --> ./

​	2. 判断用户指定的是否是目录。 stat  S_ISDIR(); --> 封装函数 isFile() {   }

​	3. 读目录： read_dir() { 

​		opendir（dir）

​		while （readdir（））{

​			普通文件，直接打印

​			目录：

​				拼接目录访问绝对路径。sprintf(path, "%s/%s", dir, d_name) 

​				递归调用自己。--》 opendir（path） readdir closedir

​		}

​		closedir（）

​		}

​		read_dir() --> isFile() ---> read_dir()



## 70P-递归遍历目录代码预览

1. \#include <stdio.h> 
2. \#include <stdlib.h> 
3. \#include <string.h> 
4. \#include <unistd.h> 
5. \#include <sys/stat.h> 
6. \#include <dirent.h> 
7. \#include <pthread.h> 
8.  
9. **void** isFile(**char** *name); 
10.  
11. // 打开目录读取,处理目录 
12. **void** read_dir(**char** *dir, **void** (*func)(**char** *)) 
13. { 
14.   **char** path[256]; 
15.   DIR *dp; 
16.   **struct** dirent *sdp; 
17.  
18.   dp = opendir(dir); 
19.   **if** (dp == NULL) { 
20. ​    perror("opendir error"); 
21. ​    **return**; 
22.   } 
23.   // 读取目录项 
24.   **while**((sdp = readdir(dp)) != NULL) { 
25. ​    **if** (strcmp(sdp->d_name, ".") == 0 || strcmp(sdp->d_name, "..") == 0) { 
26. ​      **continue**; 
27. ​    } 
28. ​    //fprintf(); 
29. ​    // 目录项本身不可访问, 拼接. 目录/目录项 
30. ​    sprintf(path, "%s/%s", dir, sdp->d_name); 
31.  
32. ​    // 判断文件类型,目录递归进入,文件显示名字/大小 
33. ​    //isFile(path);   
34. ​    (*func)(path); 
35.   } 
36.  
37.   closedir(dp); 
38.  
39.   **return** ; 
40. } 
41.  
42. **void** isFile(**char** *name) 
43. { 
44.   **int** ret = 0; 
45.   **struct** stat sb; 
46.  
47.   // 获取文件属性, 判断文件类型 
48.   ret = stat(name, &sb); 
49.   **if** (ret == -1) { 
50. ​    perror("stat error"); 
51. ​    **return** ; 
52.   } 
53.   // 是目录文件 
54.   **if** (S_ISDIR(sb.st_mode)) { 
55. ​    read_dir(name, isFile); 
56.   } 
57.   // 是普通文件, 显示名字/大小 
58.   printf("%10s\t\t%ld\n", name, sb.st_size); 
59.  
60.   **return**; 
61. } 
62.  
63.  
64. **int** main(**int** argc, **char** *argv[]) 
65. { 
66.   // 判断命令行参数 
67.   **if** (argc == 1) { 
68. ​    isFile("."); 
69.   } **else** { 
70. ​    isFile(argv[1]); 
71.   } 
72.  
73.   **return** 0; 
74. } 



## 71P-递归遍历目录实现

先写个简易版的，可以判定文件，读取文件大小：

1. \#include <stdio.h> 
2. \#include <stdlib.h> 
3. \#include <string.h> 
4. \#include <unistd.h> 
5. \#include <pthread.h> 
6. \#include <sys/stat.h> 
7.   
8. **void** isFile(**char** *name){ 
9.   **int** ret = 0; 
10.   **struct** stat sb; 
11.  
12.   ret = stat(name, &sb); 
13.   **if**(ret == -1){ 
14. ​    perror("stat error"); 
15. ​    **return**; 
16.   } 
17.  
18.   **if**(S_ISDIR(sb.st_mode)){ 
19.  
20.   } 
21.   printf("%s\t%ld\n", name, sb.st_size); 
22.  
23.   **return**; 
24. } 
25. **int** main(**int** argc, **char** *argv[]){ 
26.   **if**(argc == 1) { 
27. ​    isFile("."); 
28.   } 
29.   **else** { 
30. ​    isFile(argv[1]); 
31.   } 
32.  
33.   **return** 0; 
34. } 





编译运行，并查看一个文件，如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508134002-36c83070-189c-456a-baed-0bfa567a4b11.png)

下面完善功能，把对目录的递归处理补全，如下：



1. \#include <stdio.h> 
2. \#include <stdlib.h> 
3. \#include <string.h> 
4. \#include <unistd.h> 
5. \#include <sys/stat.h> 
6. \#include <dirent.h> 
7. \#include <pthread.h> 
8.  
9. **void** isFile(**char** *name); 
10.  
11. // 打开目录读取,处理目录 
12. **void** read_dir(**char** *dir, **void** (*func)(**char** *)) 
13. { 
14.   **char** path[256]; 
15.   DIR *dp; 
16.   **struct** dirent *sdp; 
17.  
18.   dp = opendir(dir); 
19.   **if** (dp == NULL) { 
20. ​    perror("opendir error"); 
21. ​    **return**; 
22.   } 
23.   // 读取目录项 
24.   **while**((sdp = readdir(dp)) != NULL) { 
25. ​    **if** (strcmp(sdp->d_name, ".") == 0 || strcmp(sdp->d_name, "..") == 0) { 
26. ​      **continue**; 
27. ​    } 
28. ​    //fprintf(); 
29. ​    // 目录项本身不可访问, 拼接. 目录/目录项 
30. ​    sprintf(path, "%s/%s", dir, sdp->d_name); 
31.  
32. ​    // 判断文件类型,目录递归进入,文件显示名字/大小 
33. ​    //isFile(path);   
34. ​    (*func)(path); 
35.   } 
36.  
37.   closedir(dp); 
38.  
39.   **return** ; 
40. } 
41.  
42. **void** isFile(**char** *name) 
43. { 
44.   **int** ret = 0; 
45.   **struct** stat sb; 
46.  
47.   // 获取文件属性, 判断文件类型 
48.   ret = stat(name, &sb); 
49.   **if** (ret == -1) { 
50. ​    perror("stat error"); 
51. ​    **return** ; 
52.   } 
53.   // 是目录文件 
54.   **if** (S_ISDIR(sb.st_mode)) { 
55. ​    read_dir(name, isFile); 
56.   } 
57.   // 是普通文件, 显示名字/大小 
58.   printf("%10s\t\t%ld\n", name, sb.st_size); 
59.  
60.   **return**; 
61. } 
62.  
63.  
64. **int** main(**int** argc, **char** *argv[]) 
65. { 
66.   // 判断命令行参数 
67.   **if** (argc == 1) { 
68. ​    isFile("."); 
69.   } **else** { 
70. ​    isFile(argv[1]); 
71.   } 
72.  
73.   **return** 0; 
74. } 





这里和视频里有一点差异就是，这里用的回调函数来实现对目录中目录项的处理，视频里直接调用的isFile，差别不大。



编译运行，如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508134248-ca35d214-f208-42be-a7d8-c5371e82a6d7.png)

如图，基本达到了ls-r的功能。



## 72P-递归遍历目录总结

递归改回调，问题不大





## 73P-dup和dup2



用来做重定向，本质就是复制文件描述符：

dup 和 dup2函数

`int dup(int oldfd);`		文件描述符复制。

​	oldfd: 已有文件描述符

​	返回：新文件描述符，这个描述符和oldfd指向相同内容。

​	

```
int dup2(int oldfd, int newfd); 
```

文件描述符复制，oldfd拷贝给newfd。返回newfd==>全是oldfd



一个小例子，给一个旧的文件描述符，返回一个新文件描述符：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508134562-91f8a9b6-cd33-417f-851e-c1ed3ae558e8.png)



下面讲dup2（dupto）：

下面这个例子，将一个已有文件描述符fd1复制给另一个文件描述符fd2，然后用fd2修改fd1指向的文件：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508135255-4ee1f679-d9fd-4b8f-9715-52ed604b7239.png)



编译运行，如下图：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508135620-5a182a6c-0971-4020-bffb-aeb11e295c93.png)

下面查看hello.c

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508135878-5caf2661-8e9d-49e6-a4fa-bcc611946011.png)

没错，惠惠是我老婆。

上面那个例子，fd1是打开hello.c的文件描述符，fd2是打开hello2.c的文件描述符

用dup2将fd1复制给了fd2，于是在对fd2指向的文件进行写操作时，实际上就是对fd1指向的hello.c进行写操作。

这里需要注意一个问题，由于hello.c和hello2.c都是空文件，所以直接写进去没关系。但如果hello.c是非空的，写进去的内容默认从文件头部开始写，会覆盖原有内容。



dup2也可以用于标准输入输出的重定向。

下面这个例子，将输出到STDOUT的内容重定向到文件里：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508136186-9478b834-16f3-41da-ad6c-38de0ba4bebb.png)

编译执行，如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508136654-1fcdbdfd-00a1-4a01-8cac-c976051d1d46.png)

这个程序，将fd1的内容复制给了fd2，使得原来指向hello2.c的fd2也指向了hello.c

并通过fd2向hello.c里写入了惠惠是我老婆。完事儿将标准输出重定向至fd1，就是将要显示在标准输出的内容，写入了fd1指向的文件，就是hello.c中

这里有一点和上面程序不同，就是hello.c是处于打开状态的，连续写入两段话，写入小忍是我老婆的时候，读写指针在这句话末尾，就不会覆盖惠惠是我老婆这句话，所以，都是我老婆，没有问题的。

这里再强调一下，打开一个文件，读写指针默认在文件头，如果文件本身有内容，直接写入会覆盖原有内容。



## 74P-fcntl实现dup描述符



fcntl 函数

```
int fcntl(int fd, int cmd, ....);
```

​	cmd: F_DUPFD

​	参3:  	被占用的，返回最小可用的。

​			未被占用的， 返回=该值的文件描述符。



下面这个代码用fcntl来实现描述符的复制：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508137051-6d446ed3-39ef-427d-b4bd-11fc523d1542.png)

对于fcntl中的参数0，这个表示0被占用，fcntl使用文件描述符表中的最小文件描述符返回

假设传入0，传一个7，且7未被占用，则会返回7

所以这个参数可以这样理解，你传入一个文件描述符k，如果k没被占用，则直接用k复制fd1的内容。如果k被占用，则返回描述符表中最小可用描述符，也就是自己指定一个一志愿，如果行，就返回这个。如果不行，国家给你分配一个最小的。



编译执行，如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508137324-7404876f-6870-480e-a665-f420570bee6e.png)

如图可知，原来指向hello.c的文件描述符是3，复制了一个，新的文件描述符4也指向hello.c



下面这个例子，用fcntl复制2次文件描述符，第一次使用默认分配，就是传0，第二次使用自己选定文件描述符复制，完事儿向文件里写入内容

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508137751-9f781c4f-f2cb-4717-9e1f-8d0557ea9653.png)



编译执行，结果如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508138108-fbf97f3e-0c1b-4867-b848-9fd904c67c4c.png)

可见上述说明都是没有问题的。



## 75P-复习