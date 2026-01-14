# 进程与PIC

## 76P-进程和程序以及CPU相关

进程：

​	程序：死的。只占用磁盘空间。		——剧本。

​	进程；活的。运行起来的程序。占用内存、cpu等系统资源。	——戏。

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631197718246-5a7ce6ee-945a-422d-8f84-df44320179b3.png)

并发和并行：并行是宏观上并发，微观上串行



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631198074617-6a2c270e-b37a-4698-bb86-9b816e5347de.png)





## 77P-虚拟内存和物理内存映射关系

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508138757-c69331f7-d40d-4f6c-a84b-116b1d82fed3.png)

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631198732832-c55df09c-e4ca-4967-9483-4f774b064f68.png)

## 78P-pcb进程控制块

PCB进程控制块：



​	进程id

​	文件描述符表

​	进程状态：	初始态、就绪态、运行态、挂起态、终止态。

​	进程工作目录位置

​	*umask掩码 （进程的概念）

​	信号相关信息资源。

​	用户id和组id



ps aux 返回结果里，第二列是进程id



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508139176-937ee33e-868b-4c54-955c-75b906918fd2.png)



## 79P-环境变量

echo $PATH   查看环境变量

path环境变量里记录了一系列的值，当运行一个可执行文件时，系统会去环境变量记录的位置里查找这个文件并执行。



echo $TERM  查看终端

echo $LANG  查看语言

env         查看所有环境变量

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508139465-99141ad9-970b-47d7-8e69-bd63e884f566.png)





## 80P-fork函数原理

fork函数

```
pid_t fork();
```

​	创建子进程。父子进程各自返回。父进程返回子进程pid。 子进程返回 0.



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631199070919-f3d146ad-f022-4695-ad99-94b26d8f20d1.png)

## 81P-fork创建子进程



fork之前的代码，父子进程都有，但是只有父进程执行了，子进程没有执行，fork之后的代码，父子进程都有机会执行。



getpid函数

`pid_t getpid();`		获取当前进程id

`pid_t getppid();`		获取当前进程的父进程id



## 82P-getpid和getppid



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508140860-cac5c358-f440-4470-b602-26a02769a736.png)

父进程先结束，导致子进程成孤儿，于是回收到孤儿院，看起来合情合理。



修改一下代码，给父进程增加一个等待命令，这样能保证子进程完成时，父进程处于执行状态，子进程就不会成孤儿。同时，这里也解决了终端提示符和输出混在一起的问题，这个问题会在下一节分析，不用管，代码如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508142249-8cc3f072-ab80-40c6-9d91-b51e0b9534ad.png)



写的所有进程都是bash的子进程

那么疯狂收孤儿的1630呢，如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508143299-29cb4803-98cd-4773-b470-3f4a484ebb37.png)

这里的upstart，就是进程孤儿院。



## 83P-循环创建多个子进程

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630542614798-b2547fb6-e022-43ea-984a-c32964d4e8f0.png)





![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631229071788-69296c3b-ef3a-4bae-bc4c-76253b88b96e.png)



## 84P-父子进程共享哪些内容

父子进程相同：

​	刚fork后。 data段、text段、堆、栈、环境变量、全局变量、宿主目录位置、进程工作目录位置、信号处理方式（0-3G的用户空间）



父子进程不同：

​	进程id、返回值、各自的父进程、进程创建时间、闹钟、未决信号集



## 85P-父子进程共享

父子进程共享：

​	读时共享、写时复制。———————— 全局变量。

1.文件描述符（打开文件的结构体） 2. mmap映射区（进程间通信）。 



## 86P-总结

## 87P-复习



# 六

## 88P-父子进程gdb调试

gdb调试：



​	设置父进程调试路径：set follow-fork-mode parent (默认)



​	设置子进程调试路径：set follow-fork-mode child

注意，一定要在fork函数调用之前设置才有效。



## 89P-exec函数族

exec函数族

使进程执行某一程序。成功无返回值，失败返回 -1



```
int execlp(const char *file, const char *arg, ...);
```

借助 PATH 环境变量找寻待执行程序

​		参1： 程序名

​		参2： argv0

​		参3： argv1

​		...： argvN

​		哨兵：NULL

该函数需要配合PATH环境变量来使用，当PATH所有目录搜素后没有参数1则返回出错。

该函数通常用来调用系统程序。如ls、date、cp、cat命令。



```
int execl(const char *path, const char *arg, ...);
```

自己指定待执行程序路径。



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631230145612-cb688ad1-6ac7-4ff3-b7aa-63035f75084c.png)



ps ajx --> pid ppid gid sid

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631230221860-c79acda5-75e3-4be3-beb0-54df3eaf14a9.png)

## 90P-execlp和ececl函数

练习

通过execlp让子进程去执行ls命令





![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316902684-0c505487-01d1-4e56-aa22-7104f3aa692b.png)

编译执行，如下：



练习

使用execl来让子程序调用自定义的程序。

注意

和execlp不同的是，第一个参数是路径，不是文件名。

这个路径用相对路径和绝对路径都行。



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316903615-169374b7-88ee-4695-b0fd-60ef7c0020cd.png)

exec代码如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316904214-75372541-1af8-4e77-b3a3-8a94c0229e1c.png)



用execl也能执行ls这些，把路径给出来就行，但是这样麻烦，所以对于系统指令一般还是用execlp



## 91P-exec函数族特性

练习

写一个程序，使用execlp执行进程查看，并将结果输出到文件里。

要用到open, execlp, dup2



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316904907-45a6bef8-f8f5-42c7-b551-a44dcc7b351d.png)



exec函数族一般规律：

- exec函数一旦调用成功，即执行新的程序，不返回。
- 只有失败才返回，错误值-1，所以通常我们直接在exec函数调用后直接调用perror()，和exit()，无需if判断。



l(list)				命令行参数列表

p(path)				搜索file时使用path变量

v(vector)				使用命令行参数数组

e(environment)		使用环境变量数组，不适用进程原有的环境变量，

​    设置新加载程序运行的环境变量



事实上，只有execve是真正的系统调用，其他5个函数最终都调用execve，是库函数，所以execve在man手册第二节，其它函数在man手册第3节。



## 92P-孤儿进程和僵尸进程



孤儿进程：

- 父进程先于子进终止，子进程沦为“孤儿进程”，会被 init 进程领养。



僵尸进程：

- 子进程终止，父进程尚未对子进程进行回收，在此期间，子进程为“僵尸进程”。  kill 对其无效。这里要注意，每个进程结束后都必然会经历僵尸态，时间长短的差别而已。
- 子进程终止时，子进程残留资源PCB存放于内核中，PCB记录了进程结束原因，进程回收就是回收PCB。回收僵尸进程，得kill它的父进程，让孤儿院去回收它。



## 93P-wait回收子进程



wait函数

回收子进程退出资源， 阻塞回收任意一个。

```
pid_t wait(int *status);
```

参数：（传出） 回收进程的状态。

返回值：成功： 回收进程的pid

​		失败： -1， errno

​	

函数作用

- 阻塞等待子进程退出
- 清理子进程残留在内核的 pcb 资源
- 通过传出参数，得到子进程结束状态



## 94P-获取子进程退出值和异常终止信号



- 一个进程终止时会关闭所有文件描述符，释放在用户空间分配的内存，但它的PCB还保留着，内核在其中保存了一些信息：如果是正常终止则保存着退出状态，如果是异常终止则保存着导致该进程终止的信号是哪个。
- 这个进程的父进程可以调用wait或者waitpid获取这些信息，然后彻底清除掉这个进程。
- 一个进程的退出状态可以在shell中用特殊变量$？查看，因为shell是它的父进程，当它终止时，shell调用wait或者waitpid得到它的退出状态，同时彻底清除掉这个进程。



获取子进程正常终止值：

​	WIFEXITED(status) --> 为真 -->调用 WEXITSTATUS(status) --> 得到 子进程 退出值。



获取导致子进程异常终止信号：

​	WIFSIGNALED(status) --> 为真 -->调用 WTERMSIG(status) --> 得到 导致子进程异常终止的信号编号。



练习

捕获程序异常终止的信号并打印：

```c
int main(void)  
{  
    pid_t pid, wpid;  
    int status;  
  
    pid = fork();  
    if (pid == 0) {  
        printf("---child, my id= %d, going to sleep 10s\n", getpid());  
        sleep(10);  
        printf("-------------child die--------------\n");  
        return 73;  
    } else if (pid > 0) {  
        //wpid = wait(NULL);          // 不关心子进程结束原因  
        wpid = wait(&status);       // 如果子进程未终止,父进程阻塞在这个函数上  
        if (wpid == -1) {  
            perror("wait error");  
            exit(1);  
        }  
        if (WIFEXITED(status)) {        //为真,说明子进程正常终止.   
            printf("child exit with %d\n", WEXITSTATUS(status));  
  
        }  
        if (WIFSIGNALED(status)) {      //为真,说明子进程是被信号终止.  
  
            printf("child kill with signal %d\n", WTERMSIG(status));  
        }  
  
        printf("------------parent wait finish: %d\n", wpid);  
    } else {  
        perror("fork");  
        return 1;  
    }  
  
    return 0;  
}  
```



## 95P-waitpid回收子进程

waitpid函数

指定某一个进程进行回收。可以设置非阻塞。			

waitpid(-1, &status, 0) == wait(&status);



```
pid_t waitpid(pid_t pid, int *status, int options);
```

参数：

​	pid：指定回收某一个子进程pid

​		> 0: 待回收的子进程pid

​		-1：任意子进程

​		0：同组的子进程。

​	status：（传出） 回收进程的状态。

​	options：WNOHANG 指定回收方式为，非阻塞。

返回值：

​	> 0 : 表成功回收的子进程 pid

​	0 : 函数调用时， 参3 指定了WNOHANG， 并且，没有子进程结束。

​	-1: 失败。errno



练习

回收指定子进程

## 96P-中午回顾

ps ajx  --> pid  ppid  gid  sid



## 97P-错误解析



在演示回收指定子进程的代码时出了问题，这里问题原因在于指定子进程的pid传递。

- 父进程里的pid变量和子进程pid变量并不是同一个。子进程结束时，父进程的pid还是原来的0。
- 原来的代码没有使用fork的返回值，导致父进程没有得到指定回收子进程的pid。
- 默认情况下，父进程fork出来的子进程都属于同一个组。



指定回收第三个进程两种实现

- 一个是阻塞等待回收指定进程，一个是非阻塞，但是用sleep延时父进程，以保证待回收的指定子进程已经执行结束。上面这个代码使用的阻塞回收，这个方案的问题在于终端提示符会和输出混杂在一起。
- 下面使用非阻塞回收+延时的方法，这样终端提示符就不会混在输出里。



1. **int** main(**int** argc, **char** *argv[]) 
2. { 
3.   **int** i; 
4.   pid_t pid, wpid, tmpid; 
5.  
6.   **for** (i = 0; i < 5; i++) {     
7. ​    pid = fork(); 
8. ​    **if** (pid == 0) {    // 循环期间, 子进程不 fork  
9. ​      **break**; 
10. ​    } 
11. ​    **if** (i == 2) { 
12. ​      tmpid = pid; 
13. ​      printf("--------pid = %d\n", tmpid); 
14. ​    } 
15.   } 
16.  
17.   **if** (5 == i) {    // 父进程, 从 表达式 2 跳出 
18. ​    sleep(5); 
19. 
20. ​    printf("i am parent , before waitpid, pid = %d\n", tmpid); 
21.  
22. ​    wpid = waitpid(tmpid, NULL, WNOHANG);  //指定一个进程回收, 不阻塞 
23. ​    //wpid = waitpid(tmpid, NULL, 0);     //指定一个进程回收, 阻塞回收 
24. ​    **if** (wpid == -1) { 
25. ​      perror("waitpid error"); 
26. ​      exit(1); 
27. ​    } 
28. ​    printf("I'm parent, wait a child finish : %d \n", wpid); 
29.  
30.   } **else** {      // 子进程, 从 break 跳出 
31. ​    sleep(i); 
32. ​    printf("I'm %dth child, pid= %d\n", i+1, getpid()); 
33.   } 
34.  
35.   **return** 0; 
36. } 



## 98P-waitpid回收多个子进程



一次wait/waitpid函数调用，只能回收一个子进程。上一个例子，父进程产生了5个子进程，wait会随机回收一个，捡到哪个算哪个。



练习

循环回收多个子进程



1. // 回收多个子进程 
2. **int** main(**int** argc, **char** *argv[]) 
3. { 
4.   **int** i; 
5.   pid_t pid, wpid; 
6.  
7.   **for** (i = 0; i < 5; i++) {     
8. ​    pid = fork(); 
9. ​    **if** (pid == 0) {    // 循环期间, 子进程不 fork  
10. ​      **break**; 
11. ​    } 
12.   } 
13.  
14.   **if** (5 == i) {    // 父进程, 从 表达式 2 跳出 
15. ​    /* 
16. ​    while ((wpid = waitpid(-1, NULL, 0))) {   // 使用阻塞方式回收子进程 
17. ​      printf("wait child %d \n", wpid); 
18. ​    } 
19. ​    */ 
20. ​    **while** ((wpid = waitpid(-1, NULL, WNOHANG)) != -1) {   //使用非阻塞方式,回收子进程. 
21. ​      **if** (wpid > 0) { 
22. ​        printf("wait child %d \n", wpid); 
23. ​      } **else** **if** (wpid == 0) { 
24. ​        sleep(1); 
25. ​        **continue**; 
26. ​      } 
27. ​    } 
28.  
29.   } **else** {      // 子进程, 从 break 跳出 
30. ​    sleep(i); 
31. ​    printf("I'm %dth child, pid= %d\n", i+1, getpid()); 
32.   } 
33.  
34.   **return** 0; 
35. } 



## 99P-wait和waitpid总结

总结：

​	wait、waitpid	一次调用，回收一个子进程。

​			想回收多个。while



## 100P-进程间通信常见方式



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316910579-9964a79a-2dc9-4377-95c3-97474df9ca66.png)

IPC(InterProcess Communication)进程间通信

进程间通信的常用方式，特征：

​	管道：简单

​	信号：开销小

​	mmap映射：非血缘关系进程间

​	socket（本地套接字）：稳定



## 101P-管道的特质

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316911154-b56ac2f2-e51e-4f16-9b3c-05439f8e1821.png)



实现原理： 内核借助环形队列机制，使用内核缓冲区实现。

特质；	

1. 伪文件

​	2. 管道中的数据只能一次读取。

​	3. 数据在管道中，只能单向流动。

局限性：

1. 自己写，不能自己读。

​	2. 数据不可以反复读。

​	3. 半双工通信。

​	4. 血缘关系进程间可用。

## 102P-管道的基本用法



pipe函数		创建，并打开管道。

```
int pipe(int fd[2]);
```

参数：	fd[0]: 读端。

​	fd[1]: 写端。

返回值： 成功： 0

失败： -1 errno



管道通信原理：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630543080227-b066177e-2beb-4505-be83-4754761f5661.png)

练习

父进程往管道里写，子进程从管道读，然后打印读取的内容



1. **void** sys_err(**const** **char** *str) { 
2.   perror(str); 
3.   exit(1); 
4. } 
5.  
6. **int** main(**int** argc, **char** *argv[]) 
7. { 
8.   **int** ret; 
9.   **int** fd[2]; 
10.   pid_t pid; 
11.  
12.   **char** *str = "hello pipe\n"; 
13.   **char** buf[1024]; 
14.  
15.   ret = pipe(fd); 
16.   **if** (ret == -1) 
17. ​    sys_err("pipe error"); 
18.  
19.   pid = fork(); 
20.   **if** (pid > 0) { 
21. ​    close(fd[0]);    // 关闭读段 
22. ​    //sleep(3); 
23. ​    write(fd[1], str, strlen(str)); 
24. ​    close(fd[1]); 
25.   } **else** **if** (pid == 0) { 
26. ​    close(fd[1]);    // 子进程关闭写段 
27. ​    ret = read(fd[0], buf, **sizeof**(buf)); 
28. ​    printf("child read ret = %d\n", ret); 
29. ​    write(STDOUT_FILENO, buf, ret); 
30.  
31. ​    close(fd[0]); 
32.   } 
33.  
34.   **return** 0; 
35. } 



要是不想让终端提示和输出混杂在一起，就在父进程写入内容之后sleep一秒钟。



## 103P-管道读写行为



读管道：

​	1. 管道有数据，read返回实际读到的字节数。

​	2. 管道无数据：	

1）无写端，read返回0 （类似读到文件尾）

2）有写端，read阻塞等待。

写管道：

​	1. 无读端， 异常终止。 （SIGPIPE导致的）

​	2. 有读端：	

1） 管道已满， 阻塞等待

2） 管道未满， 返回写出的字节个数。

## 104P-父子进程通信练习分析



练习

使用管道实现父子进程间 ls | wc -l 通信

要求

假定父进程实现wc，子进程实现ls

ls命令正常会将结果集写到stdout，但现在会写入管道写端

wc -l命令正常应该从stdin读取数据，但此时会从管道的读端读。



要用到 pipe  dup2  exec



## 105P-总结

## 106P-复习



普通文件，目录，软链接，这三个要占磁盘空间

管道，套接字，字符设备，块设备，不占磁盘空间，伪文件



# 七

## 107P-父子进程 ls | wc -l



1. **void** sys_err(**const** **char** *str) { 
2.   perror(str); 
3.   exit(1); 
4. } 
5. **int** main(**int** argc, **char** *argv[]) 
6. { 
7.   **int** fd[2]; 
8.   **int** ret; 
9.   pid_t pid; 
10.  
11.   ret = pipe(fd);         // 父进程先创建一个管道,持有管道的读端和写端 
12.   **if** (ret == -1) { 
13. ​    sys_err("pipe error"); 
14.   } 
15.  
16.   pid = fork();          // 子进程同样持有管道的读和写端 
17.   **if** (pid == -1) { 
18. ​    sys_err("fork error"); 
19.   } 
20.   **else** **if** (pid > 0) {      // 父进程 读, 关闭写端 
21. ​    close(fd[1]); 
22. ​    dup2(fd[0], STDIN_FILENO); // 重定向 stdin 到 管道的 读端 
23. ​    execlp("wc", "wc", "-l", NULL);   // 执行 wc -l 程序 
24. ​    sys_err("exclp wc error"); 
25.   } 
26.   **else** **if** (pid == 0) { 
27. ​    close(fd[0]); 
28. ​    dup2(fd[1], STDOUT_FILENO);   // 重定向 stdout 到 管道写端 
29. ​    execlp("ls", "ls", NULL);    // 子进程执行 ls 命令 
30. ​    sys_err("exclp ls error"); 
31.   } 
32.  
33.   **return** 0; 
34. } 



## 108P-兄弟进程间通信



练习

兄弟进程间通信

要求

使用循环创建N个子进程模型创建兄弟进程，使用循环因子i标识，注意管道读写行为

兄：ls；弟：wc -l；父：等待回收子进程

测试

是否允许，一个pipe有一个写端多个读端		可

是否允许，一个pipe有多个写端一个读端		可



练习

统计当前系统中进程ID大于10000的进程个数



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630543638613-c9e2d847-f30f-4fe5-860c-9bff1043826d.png)

注意

父进程不使用管道，所以一定要关闭父进程的管道，保证数据单向流动。



1. **void** sys_err(**const** **char** *str) { 
2.   perror(str); 
3.   exit(1); 
4. } 
5. **int** main(**int** argc, **char** *argv[]) 
6. { 
7.   **int** fd[2]; 
8.   **int** ret, i; 
9.   pid_t pid; 
10.  
11.   ret = pipe(fd); 
12.   **if** (ret == -1) { 
13. ​    sys_err("pipe error"); 
14.   } 
15.  
16.   **for**(i = 0; i < 2; i++) {    // 表达式2 出口,仅限父进程使用 
17. ​    pid = fork(); 
18. ​    **if** (pid == -1) { 
19. ​      sys_err("fork error"); 
20. ​    }  
21. ​    **if** (pid == 0)        // 子进程,出口 
22. ​      **break**; 
23.   }  
24.  
25.   **if** (i == 2) {          // 父进程 . 不参与管道使用.  
26. ​    close(fd[0]);        // 关闭管道的 读端/写端. 
27. ​    close(fd[1]); 
28.  
29. ​    wait(NULL);         // 回收子进程 
30. ​    wait(NULL); 
31.   } **else** **if** (i == 0) { // xiong 
32. ​    close(fd[0]); 
33. ​    dup2(fd[1], STDOUT_FILENO);   // 重定向stdout 
34. ​    execlp("ls", "ls", NULL); 
35. ​    sys_err("exclp ls error"); 
36.   } **else** **if** (i == 1) {      //弟弟 
37. ​    close(fd[1]); 
38. ​    dup2(fd[0], STDIN_FILENO);   // 重定向 stdin 
39. ​    execlp("wc", "wc", "-l", NULL); 
40. ​    sys_err("exclp wc error"); 
41.   } 
42.    
43.   **return** 0; 
44. } 







## 109P-多个读写端操作管道和管道缓冲区大小

练习

父进程读，俩子进程写，也就是一个读端多个写端。

注意

需要调控写入顺序才行

父进程必须等一下，不然可能俩子进程只写了一个，父进程就读完跑路了。



1. **int** main(**void**) 
2. { 
3.   pid_t pid; 
4.   **int** fd[2], i, n; 
5.   **char** buf[1024]; 
6.  
7.   **int** ret = pipe(fd); 
8.   **if**(ret == -1){ 
9. ​    perror("pipe error"); 
10. ​    exit(1); 
11.   } 
12.  
13.   **for**(i = 0; i < 2; i++){ 
14. ​    **if**((pid = fork()) == 0) 
15. ​      **break**; 
16. ​    **else** **if**(pid == -1){ 
17. ​      perror("pipe error"); 
18. ​      exit(1); 
19. ​    } 
20.   } 
21.  
22.   **if** (i == 0) {       
23. ​    close(fd[0]);         
24. ​    write(fd[1], "1.hello\n", strlen("1.hello\n")); 
25.   } **else** **if**(i == 1) {  
26. ​    close(fd[0]);         
27. ​    write(fd[1], "2.world\n", strlen("2.world\n")); 
28.   } **else** { 
29. ​    close(fd[1]);    //父进程关闭写端,留读端读取数据   
30. ​    sleep(1); 
31. ​    n = read(fd[0], buf, 1024);   //从管道中读数据 
32. ​    write(STDOUT_FILENO, buf, n); 
33.  
34. ​    **for**(i = 0; i < 2; i++)    //两个儿子wait两次 
35. ​      wait(NULL); 
36.   } 
37.  
38.   **return** 0; 
39. } 





管道大小，默认4096

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316914834-d2a7e90e-ce25-413d-9a79-285d449a6f43.png)



## 110P-命名管道fifo的创建和原理图

管道

优点：简单，相比信号，套接字实现进程通信，简单很多

缺点：1.只能单向通信，双向通信需建立两个管道

​	   2.只能用于有血缘关系的进程间通信。该问题后来使用fifo命名管道解决。

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630543679006-fddeba62-b3d0-4cfa-acdd-fc7b7c03e55b.png)

fifo管道

可以用于无血缘关系的进程间通信。

fifo操作起来像文件



mkfifo函数 



无血缘关系进程间通信：

​	读端，open fifo O_RDONLY

​	写端，open fifo O_WRONLY



下面的代码创建一个fifo：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631237383481-2f3ac2ba-3c0b-4c5a-a8ce-ce4707a1b693.png)



## 111P-fifo实现非血缘关系进程间通信

练习

非血缘关系进程，一个写fifo，一个读fifo，操作起来就像文件一样的



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316916190-d44cc3ee-d260-49f2-a299-9a9a789ccb0a.png)



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316916497-5df99ae2-cded-4f06-bfd0-6000339ba923.png)



下面测试多个写管道，一个读管道，就是多开两个fifo.w，就一个fifo.r，这是可以的，懒得做了，就这样吧。



测试一个写端多个读端的时候，由于数据一旦被读走就没了，所以多个读端的并集才是写端的写入数据。



## 112P-文件用于进程间通信

文件实现进程间通信：

​	打开的文件是内核中的一块缓冲区。多个无血缘关系的进程，可以同时访问该文件。



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630543726129-43b9bf2d-b4d9-4899-b192-ecc226e9113c.png)

文件通信这个，有没有血缘关系都行，

- 只是有血缘关系的进程对于同一个文件，使用的同一个文件描述符，
- 没有血缘关系的进程，对同一个文件使用的文件描述符可能不同。
- 这些都不是问题，打开的是同一个文件就行。



## 113P-mmap函数原型



存储映射I/O(Memory-mapped I/O) 

- 使一个磁盘文件与存储空间中的一个缓冲区相映射。于是从缓冲区中取数据，就相当于读文件中的相应字节。
- 与此类似，将数据存入缓冲区，则相应的字节就自动写入文件。这样，就可在不使用read和write函数的情况下，使地址指针完成I/O操作。
- 使用这种方法，首先应该通知内核，将一个指定文件映射到存储区域中。这个映射工作可以通过mmap函数来实现。

 

mmap函数

```
void *mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset);
```

创建共享内存映射

参数：

​	addr： 	指定映射区的首地址。通常传【NULL】，表示让系统自动分配

​	length：共享内存映射区的大小。（【<= 】文件的实际大小）

​	prot：	共享内存映射区的读写属性。

PROT_READ、PROT_WRITE、PROT_READ|PROT_WRITE

​	flags：	标注共享内存的共享属性。

MAP_SHARED 修改会反映到磁盘上

MAP_PRIVATE 修改不反映到磁盘上

​	fd:	用于创建共享内存映射区的那个文件的 文件描述符。

​	offset：默认0，表示映射文件全部。偏移位置。需是 【4k 的整数倍】。

返回值：

​	成功：映射区的首地址。

​	失败：MAP_FAILED (void*(-1))， errno



munmap函数

```
int munmap(void *addr, size_t length);	
```

释放映射区。

​	addr：mmap 的返回值

​	length：大小

## 114P-复习



## 115P-mmap建立映射区

练习

使用mmap创建一个映射区（共享内存），并往映射区里写入内容

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316917508-fa72143c-27a0-4c84-94b8-fa74495fbd03.png)



## 116P-mmap使用注意事项1



使用注意事项：

​	1. 用于创建映射区的文件大小为 0，实际指定非0大小创建映射区，出 “总线错误”。

​	2. 用于创建映射区的文件大小为 0，实际制定0大小创建映射区， 出 “无效参数”。

​	3. 用于创建映射区的文件读写属性为，只读。映射区属性为 读、写。 出 “无效参数”。

​	4. 创建映射区，需要read权限。当访问权限指定为 “共享”MAP_SHARED时， mmap的读写权限，应该 <=文件的open权限。	只写不行。

​	5. 文件描述符fd，在mmap创建映射区完成即可关闭。后续访问文件，用 地址访问。

​	6. offset 必须是 4096的整数倍。（MMU 映射的最小单位 4k ）

​	7. 对申请的映射区内存，不能越界访问。 

​	8. munmap用于释放的 地址，必须是mmap申请返回的地址。

​	9. 映射区访问权限为 “私有”MAP_PRIVATE, 对内存所做的所有修改，只在内存有效，不会反应到物理磁盘上。

​	10.  映射区访问权限为 “私有”MAP_PRIVATE, 只需要open文件时，有读权限，用于创建映射区即可。



## 117P-mmap使用注意事项2



mmap函数的保险调用方式：

1. fd = open（"文件名"， O_RDWR）;
2. mmap(NULL, 有效文件大小， PROT_READ|PROT_WRITE, MAP_SHARED, fd, 0);



## 118P-mmap总结



1. 创建映射区的过程中，隐含着一次对映射文件的【读操作】
2. 当MAP_SHARED时，要求：映射区的权限应该<=文件打开的权限（出于对映射区的保护）。而MAP_PRIVATE则无所谓，因为mmap中的权限是对内存的限制
3. 映射区的释放与文件关闭无关。只要映射建立成功，文件可以立即关闭
4. 特别注意，当映射文件大小为0时，不能创建映射区。所以：用于映射的文件必须要有实际大小！！mmap使用时常常会出现总线错误，通常是由于共享文件存储空间大小引起的。如，400字节大小的文件，在简历映射区时，offset4096字节，则会报出总线错误
5. munmap传入的地址一定是mmap返回的地址。坚决【杜绝指针++】操作
6. 文件偏移量必须为4K的整数倍
7. mmap创建映射区出错概率非常高，一定要检查返回值，确保映射区建立成功再进行后续操作。

## 119P-父子进程间mmap通信

练习

父子进程使用 mmap 进程间通信

要求

父进程 先 创建映射区。 open（ O_RDWR） mmap( MAP_SHARED );

指定 MAP_SHARED 权限

fork() 创建子进程。

一个进程读， 另外一个进程写。



父子进程mmap通信，对比全局变量



1. **int** var = 100; 
2.  
3. **int** main(**void**) 
4. { 
5.   **int** *p; 
6.   pid_t pid; 
7.  
8.   **int** fd; 
9.   fd = open("temp", O_RDWR|O_CREAT|O_TRUNC, 0644); 
10.   **if**(fd < 0){ 
11. ​    perror("open error"); 
12. ​    exit(1); 
13.   } 
14.   ftruncate(fd, 4); 
15.  
16.   p = (**int** *)mmap(NULL, 4, PROT_READ|PROT_WRITE, MAP_SHARED, fd, 0); 
17.   //p = (int *)mmap(NULL, 4, PROT_READ|PROT_WRITE, MAP_PRIVATE, fd, 0); 
18.   **if**(p == MAP_FAILED){    //注意:不是p == NULL 
19. ​    perror("mmap error"); 
20. ​    exit(1); 
21.   } 
22.   close(fd);         //映射区建立完毕,即可关闭文件 
23.  
24.   pid = fork();        //创建子进程 
25.   **if**(pid == 0){ 
26. ​    *p = 7000;        // 写共享内存 
27. ​    var = 1000; 
28. ​    printf("child, *p = %d, var = %d\n", *p, var); 
29.   } **else** { 
30. ​    sleep(1); 
31. ​    printf("parent, *p = %d, var = %d\n", *p, var);   // 读共享内存 
32. ​    wait(NULL); 
33.  
34. ​    **int** ret = munmap(p, 4);       //释放映射区 
35. ​    **if** (ret == -1) { 
36. ​      perror("munmap error"); 
37. ​      exit(1); 
38. ​    } 
39.   } 
40.  
41.   **return** 0; 
42. } 



编译运行，如下所示：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316918315-db0a77f6-b51d-42cd-95cf-6e2c715133d6.png)

如图，子进程修改p的值，也反映到了父进程上，这是因为共享内存定义为shared的。

如果将共享内存定义为private，运行结果如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316919056-39b9202b-0203-45b8-ace8-3b82ca130410.png)



## 120P-无血缘关系进程间mmap通信

练习

无血缘关系进程间 mmap 通信：  				【会写】

要求	

两个进程 打开同一个文件，创建映射区。

指定flags 为 MAP_SHARED。

一个进程写入，另外一个进程读出。



无血缘关系进程间通信。

- mmap：

- 数据可以重复读取。内容被读走之后不会消失，
- 如果读进程的读取时间间隔短，它会读到很多重复内容，因为写进程没来得及写入新内容。

- fifo：

- 数据只能一次读取。





下面是两个无血缘关系的通信代码，先是写进程：

1. **struct** STU { 
2.   **int** id; 
3.   **char** name[20]; 
4.   **char** sex; 
5. }; 
6.  
7. **void** sys_err(**char** *str) { 
8.   perror(str); 
9.   exit(1); 
10. } 
11.  
12. **int** main(**int** argc, **char** *argv[]) 
13. { 
14.   **int** fd; 
15.   **struct** STU student = {10, "xiaoming", 'm'}; 
16.   **char** *mm; 
17.  
18.   **if** (argc < 2) { 
19. ​    printf("./a.out file_shared\n"); 
20. ​    exit(-1); 
21.   } 
22.  
23.   fd = open(argv[1], O_RDWR | O_CREAT, 0664); 
24.   ftruncate(fd, **sizeof**(student)); 
25.  
26.   mm = mmap(NULL, **sizeof**(student), PROT_READ|PROT_WRITE, MAP_SHARED, fd, 0); 
27.   **if** (mm == MAP_FAILED) 
28. ​    sys_err("mmap"); 
29.  
30.   close(fd); 
31.  
32.   **while** (1) { 
33. ​    memcpy(mm, &student, **sizeof**(student)); 
34. ​    student.id++; 
35. ​    sleep(1); 
36.   } 
37.  
38.   munmap(mm, **sizeof**(student)); 
39.  
40.   **return** 0; 
41. } 





然后是读进程：

1. **struct** STU { 
2.   **int** id; 
3.   **char** name[20]; 
4.   **char** sex; 
5. }; 
6.  
7. **void** sys_err(**char** *str) { 
8.   perror(str); 
9.   exit(-1); 
10. } 
11.  
12. **int** main(**int** argc, **char** *argv[]) 
13. { 
14.   **int** fd; 
15.   **struct** STU student; 
16.   **struct** STU *mm; 
17.  
18.   **if** (argc < 2) { 
19. ​    printf("./a.out file_shared\n"); 
20. ​    exit(-1); 
21.   } 
22.  
23.   fd = open(argv[1], O_RDONLY); 
24.   **if** (fd == -1) 
25. ​    sys_err("open error"); 
26.  
27.   mm = mmap(NULL, **sizeof**(student), PROT_READ, MAP_SHARED, fd, 0); 
28.   **if** (mm == MAP_FAILED) 
29. ​    sys_err("mmap error"); 
30.    
31.   close(fd); 
32.  
33.   **while** (1) { 
34. ​    printf("id=%d\tname=%s\t%c\n", mm->id, mm->name, mm->sex); 
35. ​    sleep(2); 
36.   } 
37.   munmap(mm, **sizeof**(student)); 
38.  
39.   **return** 0; 
40. } 





编译并运行，结果如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316919473-abcfcfb1-e71a-4b88-905b-3bfcd39f073a.png)



多个写端一个读端也没问题，打开多个写进程即可，完事儿读进程会读到所有写进程写入的内容。





## 121P-mmap总结

## 122P-mmap匿名映射区



匿名映射：只能用于 血缘关系进程间通信。

​	p = (int *)mmap(NULL, 40, PROT_READ|PROT_WRITE, MAP_SHARED|MAP_ANONYMOUS, -1, 0);

## 123P-总结

## 124P-复习