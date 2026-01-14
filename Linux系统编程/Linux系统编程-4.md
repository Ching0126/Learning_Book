# 信号与多线程

## 125P-信号的概念和机制

信号共性：

​	简单、不能携带大量信息、满足条件才发送。

信号的特质：

​	信号是软件层面上的“中断”。一旦信号产生，无论程序执行到什么位置，必须立即停止运行，处理信号，处理结束，再继续执行后续指令。

​	**所有信号的产生及处理全部都是由【内核】完成的。**



## 126P-与信号相关的概念



产生信号：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631240076363-da3512e3-c498-4c22-a81e-8f9ed9e0fadc.png)



未决：产生与递达之间状态。  

递达：产生并且送达到进程。直接被内核处理掉。

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631240227172-c5d1b5bd-b4b3-46fe-96d0-0411b8b76d73.png)

信号处理方式：

执行默认处理动作

忽略

捕捉（自定义）

## 127P-信号屏蔽字和未决信号集



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631240568853-fae24dee-bde7-44f2-91db-265eb665f7f1.png)



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631242274052-e5eb19ad-3c43-4f86-a9a7-2a585b6e1451.png)



阻塞信号集（信号屏蔽字）

本质：位图。用来记录信号的屏蔽状态。一旦被屏蔽的信号，在解除屏蔽前，一直处于未决态。



未决信号集

本质：位图。用来记录信号的处理状态。该信号集中的信号，表示，已经产生，但尚未被处理。



## 128P-信号四要素和常规信号一览



kill -l 

查看当前系统中常规信号



信号4要素

编号、名称、对应事件、默认处理动作。



信号使用之前，应先确定其4要素，而后再用！！！



## 129P-kill函数和kill命令

kill函数

```
int kill（pid_t pid, int signum）
```

参数：

​	pid: 	> 0:发送信号给指定进程

​		= 0：发送信号给跟调用kill函数的那个进程处于同一进程组的进程。

​		< -1: 取绝对值，发送信号给该绝对值所对应的进程组的所有组员。

​		= -1：发送信号给，有权限发送的所有进程。

​	signum：待发送的信号

返回值：

​	成功： 0

​	失败： -1 errno



练习

子进程发送信号kill父进程：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316920617-b43d3db6-e7d3-408c-aa98-6f6ffb4fc6f4.png)

编译运行，结果如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316921759-785993ab-11a9-4140-b865-9fd3a22977db.png)



kill -9 -groupname  杀一个进程组

## 130P-alarm函数



每个进程都有唯一的闹钟



alarm 函数

使用自然计时法。

定时发送SIGALRM给当前进程。



```
unsigned int alarm(unsigned int seconds);
```

参数：	seconds：定时秒数

返回值：上次定时剩余时间。

​	无错误现象。

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631242608064-5ab64e39-b5a1-4cc7-aca9-bf2cc553c6ae.png)

alarm（0）； 取消闹钟。



练习

使用alarm函数计时，打印变量i的值。

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631242629434-4ee0b705-e509-41ea-ad87-bdb4e06f799a.png)



time 命令 ： 查看程序执行时间。   实际时间 = 用户时间 + 内核时间 + 等待时间。  

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631242733487-6d02d41d-5a8d-4e0f-936d-1c1a229bd460.png)

--> 优化瓶颈 IO

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631242800981-eaeb4786-8d39-4b1b-b4f0-1c152d5c6018.png)



## 131P-setitimer函数

setitimer函数

设置闹钟，可以替代alarm函数，精度微秒us，可以实现周期定时

```
int setitimer(int which, const struct itimerval *new_value, struct itimerval *old_value);
```

参数：

​	which：	ITIMER_REAL： 采用自然计时。 ——> SIGALRM

​			ITIMER_VIRTUAL: 采用用户空间计时  ---> SIGVTALRM

​			ITIMER_PROF: 采用内核+用户空间计时 ---> SIGPROF

​	new_value：定时秒数

​	old_value：传出参数，上次定时剩余时间。

返回值：

​	成功： 0

​	失败： -1 errno



类型

`struct itimerval` {

`struct timeval` {

time_t      tv_sec;         /* seconds */

suseconds_t tv_usec;        /* microseconds */

}it_interval;---> 用于设定两个定时任务之间的间隔时间

`struct timeval` {

time_t      tv_sec;         

suseconds_t tv_usec;        

}it_value;  ---> 第一次定时秒数

};



可以理解为有2个定时器

- 一个用于第一个闹钟什么时候触发打印
- 一个用于之后间隔多少时间再次触发闹钟。



例子

使用setitimer定时，向屏幕打印信息：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316922677-6092bedc-4897-4318-966d-be68793e54cb.png)



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316923304-425faee5-2c4f-40d7-b961-d1d6076766ff.png)

第一次信息打印是两秒间隔，之后都是5秒间隔打印一次



## 132P-午后回顾

## 133P-信号集操作函数



信号集set函数

```
sigset_t set;
```

自定义信号集。

```
sigemptyset(sigset_t *set);
```

清空信号集

```
sigfillset(sigset_t *set);
```

全部置1

```
sigaddset(sigset_t *set, int signum);
```

将一个信号添加到集合中

```
sigdelset(sigset_t *set, int signum);
```

将一个信号从集合中移除

```
sigismember（const sigset_t *set，int signum); 
```

判断一个信号是否在集合中。 在-->1， 不在-->0



sigprocmask函数

```
int sigprocmask(int how, const sigset_t *set, sigset_t *oldset);
```

设置信号屏蔽字和解除屏蔽：

参数：	

​	how:	SIG_BLOCK:		设置阻塞（与）

​		SIG_UNBLOCK:	取消阻塞（取反位与）

​		SIG_SETMASK:		用自定义set替换mask。（不推荐）

​	set：	自定义set

​	oldset：旧有的 mask。



sigpending函数

```
int sigpending(sigset_t *set);
```

读取未决信号集

参数：	set： 传出的 未决信号集。



## 134P-信号操作函数使用原理分析

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316924347-c68ffe59-2132-4b91-9890-c88ca87a345b.png)

## 135P-信号集操作函数练习



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316924895-aad28de9-fbf9-4943-85c0-0d57c6bac502.png)

其中9号和19号信号比较特殊，只能执行默认动作，不能忽略捕捉，不能设置阻塞。



例子

利用自定义集合，来设置信号阻塞，输入被设置阻塞的信号，可以看到未决信号集发生变化



1. **void** sys_err(**const** **char** *str) 
2. { 
3.   perror(str); 
4.   exit(1); 
5. } 
6.  
7. **void** print_set(sigset_t *set) 
8. { 
9.   **int** i; 
10.   **for** (i = 1; i<32; i++) { 
11. ​    **if** (sigismember(set, i))  
12. ​      putchar('1'); 
13. ​    **else**  
14. ​      putchar('0'); 
15.   } 
16.   printf("\n"); 
17. } 
18. **int** main(**int** argc, **char** *argv[]) 
19. { 
20.   sigset_t set, oldset, pedset; 
21.   **int** ret = 0; 
22.  
23.   sigemptyset(&set); 
24.   sigaddset(&set, SIGINT); 
25.   sigaddset(&set, SIGQUIT); 
26.   sigaddset(&set, SIGBUS); 
27.   sigaddset(&set, SIGKILL); 
28.  
29.   ret = sigprocmask(SIG_BLOCK, &set, &oldset); 
30.   **if** (ret == -1) 
31. ​    sys_err("sigprocmask error"); 
32.  
33.   **while** (1) { 
34. ​    ret = sigpending(&pedset); 
35. ​    print_set(&pedset); 
36. ​    sleep(1); 
37.   } 
38.  
39.   **return** 0; 
40. } 



编译运行，如下图所示：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316925460-507d8c22-6a2b-48a6-ab3c-975f963bfbf4.png)

可以看到，在输入Ctrl+C之后，进程捕捉到信号，但由于设置阻塞，没有处理，未决信号集对应位置变为1.



## 136P-signal实现信号捕捉



signal函数

注册一个信号捕捉函数，ANS设置，不同操作系统存在差异建议使用sigaction函数

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631244958083-4f32598a-b926-48e2-a6d6-0348dc3645a7.png)



参数：

signum ：待捕捉信号

handler：捕捉信号后的操纵函数



返回值：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631245033991-e399df07-ef94-4835-94d1-4e239fd88758.png)



例子

一个信号捕捉

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631245131406-a574fabf-01e3-4e97-b5f9-721a5ca8c45e.png)

编译运行，如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316928929-1f486474-9247-44dd-80a6-5bc4ce6e1fd9.png)



## 137P-sigaction实现信号捕捉



sigaction函数

注册一个信号捕捉函数

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631245224163-683f3d75-cc64-447d-8fa4-b88787ee07ff.png)

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631245916632-e0b57716-3ca3-47a0-a93b-0379236f5514.png)

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316931486-7d81855f-4ae0-474d-b79e-42b4a4946f5f.png)



例子

使用sigaction捕捉两个信号



1. **void** sys_err(**const** **char** *str) { 
2.   perror(str); 
3.   exit(1); 
4. } 
5.  
6. **void** sig_catch(**int** signo) {          // 回调函数 
7.   **if** (signo == SIGINT) { 
8. ​    printf("catch you!! %d\n", signo); 
9. ​    sleep(10); 
10.   } **else** **if** (signo == SIGQUIT) 
11. ​    printf("-----------catch you!! %d\n", signo); 
12.   **return** ; 
13. } 
14.  
15. **int** main(**int** argc, **char** *argv[]) 
16. { 
17.   **struct** sigaction act, oldact; 
18.   act.sa_handler = sig_catch;    	 // set callback function name    设置回调函数 
19.   sigemptyset(&(act.sa_mask));     // set mask when sig_catch working. 清空sa_mask屏蔽字, 只在sig_catch工作时有效 
20.   act.sa_flags = 0;         		 // usually use.           默认值 
21.    
22.   **int** ret = sigaction(SIGINT, &act, &oldact);   //注册信号捕捉函数 
23.   **if** (ret == -1) 
24. ​    sys_err("sigaction error"); 
25.   ret = sigaction(SIGQUIT, &act, &oldact);   //注册信号捕捉函数 
26.  
27.   **while** (1); 
28.   **return** 0; 
29. } 





![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316932524-55dea62d-26c6-4f73-8b59-586d7989d7d5.png)

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631249172163-4b7d7c61-fde3-4b0b-abb9-ac37680b0e37.png)



## 138P-信号捕捉的特性



信号捕捉特性：

​	1. 捕捉函数执行期间，信号屏蔽字 由 mask --> sa_mask , 捕捉函数执行结束。 恢复回mask

​	2. 捕捉函数执行期间，本信号自动被屏蔽(sa_flgs = 0).其他信号不屏蔽，如需屏蔽则调用sigsetadd函数修改

3. 捕捉函数执行期间，被屏蔽信号多次发送，解除屏蔽后只处理一次！

## 139P-内核实现信号捕捉简析

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631250038767-16c2411b-ca75-4208-8ff6-b0d1cb6a01f0.png)



## 140P-借助信号捕捉回收子进程



SIGCHLD的产生条件：

​	子进程终止时

​	子进程接收到SIGSTOP

​	子进程处于停止态，接收到SIGCONT后唤醒时



练习

一次回调可以回收多个子进程

问题

- 一次回调只回收一个子进程，同时出现多个子进程死亡时，只会回收累积信号中的一个子进程。
- 有可能父进程还没注册完捕捉函数，子进程就死亡了

解决方法

- 首先是让子进程sleep，但这个不太科学。在fork之前注册也行，但这个也不是很科学。
- 最科学的方法是在int i之前设置屏蔽，等父进程注册完捕捉函数再解除屏蔽。这样即使子进程先死亡了，信号也因为被屏蔽而无法到达父进程。解除屏蔽过后，父进程就能处理累积起来的信号了。



## 141P-慢速系统调用中断



慢速系统调用：

​	可能会使进程永久阻塞的一类。如果在阻塞期间收到一个信号，该系统调用就被中断，不再继续执行(早期)，也可以设定系统调用是否重启。如read, write, pause…



## 142P-总结

## 143P-复习子进程借助信号回收



一次回调可以回收多个子进程

信号回收子进程的完整代码



1. **void** sys_err(**const** **char** *str) { 
2.   perror(str); 
3.   exit(1); 
4. } 
5.  
6. **void** catch_child(**int** signo)     // 有子进程终止，发送SGCHLD信号时，该函数会被内核回调 
7. { 
8.   pid_t wpid; 
9.   **int** status; 
10.   //if((wpid = wait(NULL)) != -1) { //只能执行一次，处理一个信号
11.   **while**((wpid = waitpid(-1, &status, 0)) != -1) {     // 循环回收,防止僵尸进程出现. 
12. ​    **if** (WIFEXITED(status)) 
13. ​      printf("---------------catch child id %d, ret=%d\n", wpid, WEXITSTATUS(status)); 
14.   } 
15.   **return** ; 
16. } 
17.  
18. **int** main(**int** argc, **char** *argv[]) 
19. { 
20.   pid_t pid; 
21.   //阻塞 
22.   sigset_t set; 
23.   sigemptyset(&set); 
24.   sigaddset(&set, SIGCHLD); 
25.   sigprocmask(SIG_BLOCK, &set, NULL); 
26.    
27.   **int** i;  
28.   **for** (i = 0; i < 15; i++) 
29. ​    **if** ((pid = fork()) == 0)        // 创建多个子进程 
30. ​      **break**; 
31.  
32.   **if** (15 == i) { 
33. ​    **struct** sigaction act; 
34. ​    act.sa_handler = catch_child;      // 设置回调函数 
35. ​    sigemptyset(&act.sa_mask);       // 设置捕捉函数执行期间屏蔽字 
36. ​    act.sa_flags = 0;            // 设置默认属性, 本信号自动屏蔽 
37. ​    sigaction(SIGCHLD, &act, NULL);     // 注册信号捕捉函数 
38. ​    //解除阻塞 
39. ​    sigprocmask(SIG_UNBLOCK, &set, NULL); 
40.  
41. ​    printf("I'm parent, pid = %d\n", getpid()); 
42. ​    **while** (1); 	//模拟后续逻辑
43.  
44.   } **else** { 
45. ​    printf("I'm child pid = %d\n", getpid()); 
46. ​    **return** i; 
47.   } 
48.  
49.   **return** 0; 
50. } 



编译运行，结果如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316937000-704cecd9-55b5-4456-9d93-d3662cade69d.png)







# 九

## 144P-进程组 会话

进程组（别名：作业）

- 多个进程的集合，每个进程都属于一个一个进程组，简化对多个进程的管理，waitpid函数和kill函数的参数中用到
- 父进程创建子进程的时候默认父子进程属于同一进程组。进程组的ID==第一个进程ID（组长进程），组长进程id==进程组id，组长进程可以创建一个进程组，创建该进程组中的进程，然后终止。
- 只要有一个进程存在，进程组就存在，生存期与组长进程是否终止无关
- kill -SIGKILL -进程组id 杀掉整个进程组
- 一个进程可以为自己或子进程设置进程组id



会话

多个进程组的集合

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631261561796-d90aed79-9db8-4f10-a11b-7f9c3b7bb8f4.png)

创建会话的6点注意事项

1. 调用进程不能是进程组组长，该进程变成新会话首进程（平民）
2. 该进程成为一个新进程组的组长进程
3. 需要root权限（ubuntu不需要）
4. 新会话丢弃原有的控制终端，该会话没有控制终端
5. 该调用进程是组长进程，则出错返回
6. 建立新会话时，先调用fork，父进程终止，子进程调用setsid



getsid函数

```
pid_t getsid(pid_t pid);
```

获取当前进程的会话id

成功返回调用进程会话ID，失败返回-1，设置error



setsid函数

```
pid_t setsid();    
```

创建一个会话，并以自己的ID设置进程组ID，同时也是新会话的ID

成功返回调用进程的会话ID，失败返回-1，设置error



## 145P-守护进程创建步骤分析

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631261753504-bfc0b939-c10b-4eb6-95a2-d7d911912a86.png)

守护进程

​	daemon进程。通常运行于操作系统后台，脱离控制终端。一般不与用户直接交互。周期性的等待某个事件发生或周期性执行某一动作。

​	不受用户登录注销影响。通常采用以d结尾的命名方式。

​	创建守护进程，最关键的一步是调用setsid函数创建一个新的Session，并成为Session Leader



守护进程创建步骤：

​	1. fork子进程，让父进程终止。

​	2. 子进程调用 setsid() 创建新会话

​	3. 通常根据需要，改变工作目录位置 chdir()， 防止目录被卸载。

​	4. 通常根据需要，重设umask文件权限掩码，影响新文件的创建权限。  022 -- 755	0345 --- 432   r---wx-w-   422

​	5. 通常根据需要，关闭/重定向 文件描述符

​	6. 守护进程 业务逻辑。while（）

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631262229868-ab1582d7-ecf9-4fe2-adcf-f23ae4f0b7df.png)

chdir函数

```
int chdir(const char *path);
```

## 146P-守护进程创建



例子

创建一个守护进程：



1. **void** sys_err(**const** **char** *str) { 
2.   perror(str); 
3.   exit(1); 
4. } 
5.  
6. **int** main(**int** argc, **char** *argv[]) 
7. { 
8.   pid_t pid; 
9.   **int** ret, fd; 
10.  
11.   pid = fork(); 
12.   **if** (pid > 0)        // 父进程终止 
13. ​    exit(0); 
14.  
15.   pid = setsid();      //创建新会话 
16.   **if** (pid == -1) 
17. ​    sys_err("setsid error"); 
18.  
19.   ret = chdir("/home/zhcode/Code/code146");    // 改变工作目录位置 
20.   **if** (ret == -1) 
21. ​    sys_err("chdir error"); 
22.  
23.   umask(0022);      // 改变文件访问权限掩码 
24.  
25.   close(STDIN_FILENO);  // 关闭文件描述符 0 
26.  
27.   fd = open("/dev/null", O_RDWR); // fd --> 0 
28.   **if** (fd == -1) 
29. ​    sys_err("open error"); 
30.  
31.   dup2(fd, STDOUT_FILENO); // 重定向 stdout和stderr 
32.   dup2(fd, STDERR_FILENO); 
33.  
34.   **while** (1);       // 模拟 守护进程业务. 
35.  
36.   **return** 0; 
37. } 



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316937564-3aa44f17-362b-48ab-add4-693210486bea.png)

查看进程列表，如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316938529-315c70a6-30e4-48b2-a4e7-2eab2028dceb.png)

这个daemon进程就不会受到用户登录注销影响。

要想终止，就必须用kill命令



## 147P-线程概念



进程：有独立的 进程地址空间。有独立的pcb。	分配资源的最小单位。

线程：有独立的pcb。没有独立的进程地址空间。	最小单位的执行。



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630544666611-f3963e21-9491-4124-991c-69a6d794fcc3.png)

ps -Lf 进程id 	---> 线程号LWP  -->cpu 执行的最小单位。

ps -Lf 进程号		查看进程的线程



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631262875747-5f57b163-3160-48c7-85f7-26106a33019d.png)

## 148P-三级映射

借助进程机制实现了线程

1. 轻量级线程，存在pcb，创建线程使用的底层函数和进程一样，都是clone
2. 从内核看进程和线程一样，都有各自不同的pcb，但是pcb中指向内存资源的三级页表是相同的
3. 进程可以变成线程
4. 线程可以看成寄存器和栈的集合
5. 线程是最小的执行单位，进程是最小的资源分配的单位

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316941587-e28a2e60-909d-4776-a6fc-ce823c2ea0b0.png)



## 149P-线程共享和非共享



独享 栈空间（内核栈、用户栈）

共享 ./text./data ./rodataa ./bsss heap  ---> 共享【全局变量】（errno）

## 150P-中午复习

## 151P-创建线程



pthread_self函数

```
pthread_t pthread_self();
```

获取线程id。 线程id是在【进程】地址空间内部，用来标识线程身份的id号。

返回值：本线程id

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631265759010-98ffaaec-fe7a-424f-8f4a-9af02d2cf03a.png)





pthread_create函数

```
int pthread_create(pthread_t *tid, const pthread_attr_t *attr, void *(*start_rountn)(void *), void *arg);
```

创建子线程

参数：

tid：传出参数，表新创建的子线程 id

attr：线程属性。传NULL表使用默认属性。

start_rountn：子线程回调函数。创建成功，ptherad_create函数返回时，该函数会被自动调用。

arg：回调函数的参数。没有的话，传NULL

返回值：

成功：0

失败：errno



 线程中检查出错返回： 

​	fprintf(stderr, "xxx error: %s\n", strerror(ret));



## 152P-循环创建多个子线程

例子

循环创建多个子线程



1. **void** sys_err(**const** **char** *str) { 
2.   perror(str); 
3.   exit(1); 
4. } 
5.  
6. **void** *tfn(**void** *arg){ 
7.   **int** i = (**int**)arg; 
8.   sleep(i); 
9.   printf("--I'm %dth thread: pid = %d, tid = %lu\n",i+1, getpid(), pthread_self()); 
10.  
11.   **return** NULL; 
12. } 
13.  
14. **int** main(**int** argc, **char** *argv[]){ 
15.   **int** i; 
16.   **int** ret; 
17.   pthread_t tid; 
18.    
19.   **for**(i=0;i<5;i++){ 
20. ​    ret = pthread_create(&tid, NULL, tfn, (**void** *)i); //直接传值不是传地址
21. ​    **if** (ret != 0) { 
22. ​      sys_err("pthread_create error"); 
23. ​    } 
24.   } 
25.   sleep(i); //主进程最后退出
26.   printf("I'm main, pid = %d, tid = %lu\n", getpid(), pthread_self()); 
27.  
28.   **return** 0; 
29. } 



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316944245-3a3eccfe-f238-49fb-a0c9-af42f21eeffc.png)

编译时会出现类型强转的警告，指针4字节转int的8字节，不过不存在精度损失，忽略就行。



## 153P-错误分析



在152P的代码中，如果将i取地址后再传入线程创建函数里，就是说



```
(void *)i`	改成		`(void *)&i
```

相应的，修改回调函数：`int i = *((int *)arg)`



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316944819-cd579f5c-5da4-41e0-891f-eadbf0597bee.png)



错误原因在于，子线程如果用引用传递i，会去读取【主线程】里的【i值】，而主线程里的i是动态变化的，不固定。所以，应该采用值传递，不用引用传递。



## 154P-线程间全局变量共享



子线程里更改全局变量后，主线程里也跟着发生变化。

## 155P-pthread_exit退出



pthread_exit函数

退出当前线程

```
void pthread_exit(void *retval);
```

参数		retval：退出值。 无退出值时，NULL



====================================

区分		exit();	退出当前进程。

​		return: 返回到调用者那里去。

​		pthread_exit(): 退出当前线程。

====================================



如果在【回调函数】里加一段代码：

if(i == 2)

```
exit(0);
```

看起来好像是退出了第三个子线程，然而运行时，发现后续的4,5也没了。这是因为，exit是退出进程。



修改一下，换成：

if(i == 2)

```
return NULL;
```

这样运行一下，发现后续线程不会凉凉，说明return是可以达到退出线程的目的。然而真正意义上，return是返回到函数调用者那里去，线程并没有退出。



再修改一下，再定义一个函数func，直接返回那种

```
void *func(void)
```

{

return NULL;

}

if(i == 2)

```
func();
```

运行，发现1,2,3,4,5线程都还在，说明没有达到退出目的。



再次修改：

```
void *func(void)
```

{

​	pthread_exit(NULL);

​	return NULL;

}

if(i == 2)

```
func();
```

编译运行，发现3没了，看起来很科学的样子。pthread_exit表示将当前线程退出。放在函数里，还是直接调用，都可以。



​	回到之前说的一个问题，由于主线程可能先于子线程结束，所以子线程的输出可能不会打印出来，当时是用主线程sleep等待子线程结束来解决的。现在就可以使用pthread_exit来解决了。方法就是将return 0替换为pthread_exit，只退出当先线程，不会对其他线程造成影响。



## 156P-pthread_join



pthread_join函数

阻塞 回收线程

```
int pthread_join(pthread_t thread, void **retval);	
```

参数		thread: 待回收的线程id

​		retval：传出参数。 回收的那个线程的退出值。

​			线程异常借助，值为 -1。

返回值：成功：0	

失败：errno



例子

回收线程并获取子线程返回值



1. **struct** thrd { 
2.   **int** var; 
3.   **char** str[256]; 
4. }; 
5.  
6. **void** sys_err(**const** **char** *str) { 
7.   perror(str); 
8.   exit(1); 
9. } 
10.  
11. **void** *tfn(**void** *arg) { 
12.   **struct** thrd *tval; 
13.  
14.   tval = malloc(**sizeof**(tval)); 
15.   tval->var = 100; 
16.   strcpy(tval->str, "hello thread"); 
17.  
18.   **return** (**void** *)tval; 
19. } 
20.  
21. **int** main(**int** argc, **char** *argv[]) 
22. { 
23.   pthread_t tid; 
24.  
25.   **struct** thrd *retval; 
26.  
27.   **int** ret = pthread_create(&tid, NULL, tfn, NULL); 
28.   **if** (ret != 0) 
29. ​    sys_err("pthread_create error"); 
30.  
31.   //int pthread_join(pthread_t thread, void **retval); 
32.   ret = pthread_join(tid, (**void** **)&retval); 
33.   **if** (ret != 0) 
34. ​    sys_err("pthread_join error"); 
35.  
36.   printf("child thread exit with var= %d, str= %s\n", retval->var, retval->str); 
37.    
38.   pthread_exit(NULL); 
39.  
40. } 



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316946711-fb2f938c-ad95-4c1d-8ae6-13a59de262a0.png)

## 157P-pthread_join作业

练习

使用pthread_join函数将【循环创建】的多个子线程回收

这里tid要使用【数组】来存

## 158P-pthread_cancel函数



pthread_cancel函数

杀死一个线程。  需要到达取消点（保存点）

```
int pthread_cancel(pthread_t thread);	
```

参数		thread: 待杀死的线程id

返回值： 成功：0

​		失败：errno



如果，子线程没有到达取消点， 那么 pthread_cancel 无效。

我们可以在程序中，手动添加一个取消点。使用 `pthread_testcancel();`

成功被 pthread_cancel() 杀死的线程，返回 -1.使用pthead_join 回收。



例子

主线程调用pthread_cancel杀死子线程

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631268197773-d2f6bb5e-8c19-4375-9740-51523f97a1f6.png)

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631268168650-a1bdad54-1dff-464e-a1f7-11907e4a8a34.png)



这里要注意一点，pthread_cancel工作的必要条件是进入内核，如果tfn真的奇葩到没有进入内核，则pthread_cancel不能杀死线程，此时需要手动设置取消点，就是pthread_testcancel()

## 159P-检查出错返回



pthread_detach函数

设置线程分离

```
int pthread_detach(pthread_t thread);
```

参数		thread: 待分离的线程id

返回值：	成功：0

​		失败：errno	



例子

使用detach分离线程，分离后的线程会自动回收



1. **void** *tfn(**void** *arg) { 
2.   printf("thread: pid = %d, tid = %lu\n", getpid(), pthread_self()); 
3.   **return** NULL; 
4. } 
5.  
6. **int** main(**int** argc, **char** *argv[]) 
7. { 
8.   pthread_t tid; 
9.   **int** ret = pthread_create(&tid, NULL, tfn, NULL); 
10.   **if** (ret != 0) { 
11. ​    fprintf(stderr, "pthread_create error: %s\n", strerror(ret)); 
12. ​    exit(1); 
13.   } 
14.   ret = pthread_detach(tid);       // 设置线程分离` 线程终止,会自动清理pcb,无需回收 
15.   **if** (ret != 0) { 
16. ​    fprintf(stderr, "pthread_detach error: %s\n", strerror(ret)); 
17. ​    exit(1); 
18.   } 
19.  
20.   sleep(1); 
21.  
22.   ret = pthread_join(tid, NULL); 
23.   printf("join ret = %d\n", ret); 
24.   **if** (ret != 0) { 
25. ​      //perror("pthread_join error"); //perror方法无法检查线程错误
26. ​    fprintf(stderr, "pthread_join error: %s\n", strerror(ret)); 
27. ​    exit(1); 
28.   } 
29.  
30.   printf("main: pid = %d, tid = %lu\n", getpid(), pthread_self()); 
31.   pthread_exit((**void** *)0); 
32. } 



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631268965777-3bf8b33f-a11c-40ab-8f29-ff9435514381.png)



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316948916-e4daebef-9c6e-4635-b874-cdb6e9588813.png)



## 160P-线程分离pthread_detach



上一节的出错，是因为线程分离后，系统会自动回收资源，用pthread_join去回收已经被系统回收的线程，那个线程号就是无效参数。

## 161P-进程和线程控制原语对比



线程控制原语					进程控制原语

pthread_create()				fork();

pthread_self()					getpid();

pthread_exit()					exit(); 		/ return 

pthread_join()					wait()/waitpid()

pthread_cancel()				kill()

pthread_detach()



## 162P-线程属性设置分离线程



pthread_attr_函数

设置分离属性。

```
pthread_attr_t attr;
```

创建一个线程属性结构体变量

```
pthread_attr_init(&attr);
```

初始化线程属性



```
pthread_attr_setdetachstate(&attr,  PTHREAD_CREATE_DETACHED);
```

设置线程属性为 【分离态】



```
pthread_create(&tid, &attr, tfn, NULL);
```

借助修改后的 设置线程属性 创建为分离态的新线程

```
pthread_attr_destroy(&attr);
```

销毁线程属性

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631269098906-11d84b79-21b1-4ba7-bdb6-2eb5723f14a9.png)



## 163P-线程使用注意事项

1. 主线程退出其他线程不退出，主线程应该调用pthread_exit
2. 避免僵尸线程

1. pthread_join
2. pthread_detach
3. pthread_create指定分离属性
4. 被join线程可能在join函数返回前就释放自己的所有内存资源，所以不应当返回被回收线程栈中的值

1. malloc和mmap申请的内存可以被其他线程释放
2. 应避免在多线程中调用fork，除非马exec，子线程中只有调用fork的线程存在，其他线程在子进程中均pthread_exit
3. 信号的复杂语义很难和多线程共存，在多线程中避免使用信号机制

## 164P-总结





# 十

## 165P-线程同步概念



线程同步

协同步调，对公共区域数据【按序】访问。防止数据混乱，产生与时间有关的错误。



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316950525-5b181358-c642-4f40-96b7-3682472d9867.png)

数据混乱的原因：

1. 资源共享(独享资源则不会)
2. 调度随机(意味着数据访问会出现竞争)
3. 线程间缺乏必要同步机制



## 166P-锁使用的注意事项

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631263244005-9420b14b-7a06-4947-9b47-1768be1e649f.png)



锁的使用

建议锁！对公共数据进行保护。所有线程【应该】在访问公共数据前先拿锁再访问。但，锁本身不具备强制性。

## 167P-借助互斥锁管理共享数据实现同步

例子

数据共享导致的混乱



1. **void** *tfn(**void** *arg) { 
2.   srand(time(NULL));  
3.   **while** (1) { 
4. ​    printf("hello "); 
5. ​    sleep(rand() % 3); /*模拟长时间操作共享资源，导致cpu易主，产生与时间有关的错误*/ 
6. ​    printf("world\n"); 
7. ​    sleep(rand() % 3); 
8.   } 
9.   **return** NULL; 
10. } 
11.  
12. **int** main(**void**) 
13. { 
14.   pthread_t tid; 
15.   srand(time(NULL)); 
16.   pthread_create(&tid, NULL, tfn, NULL); 
17.   **while** (1) { 
18. ​    printf("HELLO "); 
19. ​    sleep(rand() % 3); 
20. ​    printf("WORLD\n"); 
21. ​    sleep(rand() % 3); 
22.   } 
23.   pthread_join(tid, NULL); 
24.   **return** 0; 
25. } 



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316951588-303fb52a-19df-47cb-b5fc-fc9bcf040047.png)

主线程和子线程在访问共享区时交叉输出



pthread_mutex_函数

```
pthread_mutex_t mutex;
```



5个函数的返回值都是成功返回0，失败返回错误号

```
int pthread_mutex_init(pthread_mutex_t ***restrict** mutex, const pthread_mutexattr_t *restrict attr);
```

创建

```
int pthread_mutex_destory(pthread_mutex_t *mutex);
```

销毁

```
int pthread_mutex_lock(pthread_mutex_t *mutex);
```

上锁

```
int pthread_mutex_trylock(pthread_mutex_t *mutex);
```

try锁

```
int pthread_mutex_unlock(pthread_mutex_t *mutex);
```

解锁



restrict关键字

用来限定指针变量。被该关键字限定的指针变量所指向的内存操作，必须由本指针完成。

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631264791021-d1f43a1c-afd2-456e-af0a-54de3e677907.png)

pthread_mutex_t 类型，其本质是一个结构体。为简化理解，应用时可忽略其实现细节，简单当成整数看待

pthread_mutex_t mutex；变量mutex只有两种取值：0,1



使用mutex(互斥量、互斥锁)一般步骤：

1. pthread_mutex_t lock;  创建锁

2  pthread_mutex_init; 初始化		1

3. pthread_mutex_lock;加锁		1--	--> 0
4. 访问共享数据（stdout)		
5. pthrad_mutext_unlock();解锁		0++	--> 1
6. pthead_mutex_destroy；销毁锁



初始化互斥量：

pthread_mutex_t mutex;

1. 动态初始化：pthread_mutex_init(&mutex, NULL);   			
2. 静态初始化：pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;	



练习

修改上面的代码，使用锁实现互斥访问共享区：



1. pthread_mutex_t mutex;   // 定义一把互斥锁 
2.  
3. **void** *tfn(**void** *arg) { 
4.   srand(time(NULL)); 
5.   **while** (1) { 
6. ​    pthread_mutex_lock(&mutex);   // 加锁 
7. ​    printf("hello "); 
8. ​    sleep(rand() % 3); // 模拟长时间操作共享资源，导致cpu易主，产生与时间有关的错误 
9. ​    printf("world\n"); 
10. ​    pthread_mutex_unlock(&mutex);  // 解锁 
11. ​    sleep(rand() % 3); 
12.   } 
13.   **return** NULL; 
14. } 
15.  
16. **int** main(**void**) 
17. { 
18.   pthread_t tid; 
19.   srand(time(NULL)); 
20.   **int** ret = pthread_mutex_init(&mutex, NULL);  // 初始化互斥锁 
21.   **if**(ret != 0){ 
22. ​    fprintf(stderr, "mutex init error:%s\n", strerror(ret)); 
23. ​    exit(1); 
24.   } 
25.   pthread_create(&tid, NULL, tfn, NULL); 
26.   **while** (1) { 
27. ​    pthread_mutex_lock(&mutex);   // 加锁 
28. ​    printf("HELLO "); 
29. ​    sleep(rand() % 3); 
30. ​    printf("WORLD\n"); 
31. ​    pthread_mutex_unlock(&mutex);  // 解锁 
32. ​    sleep(rand() % 3); 
33.   } 
34.   pthread_join(tid, NULL); 
35.   pthread_mutex_destory(&mutex);   // 销毁互斥锁 
36.   **return** 0; 
37. } 



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316952579-8c9068ff-ff63-4ea2-a55e-7ae288bd114d.png)



## 168P-互斥锁使用技巧

注意事项：

尽量保证锁的粒度， 越小越好。（访问共享数据前，加锁。访问结束【立即】解锁。）

互斥锁，本质是结构体。 我们可以看成整数。 初值为 1。（pthread_mutex_init() 函数调用成功。）



加锁： --操作， 阻塞线程。

解锁： ++操作， 唤醒阻塞在锁上的线程。

try锁：尝试加锁，成功--。失败，返回。同时设置错误号 EBUSY

## 169P-try锁

try锁：尝试加锁，成功--，加锁失败直接返回错误号(如EBUSY)，不阻塞

## 170P-读写锁操作函数原型



pthread_rwlock_函数

成功返回0，失败返回错误号

pthread_rwlock_t  rwlock;

pthread_rwlock_init(&rwlock, NULL);

pthread_rwlock_rdlock(&rwlock);		try

pthread_rwlock_wrlock(&rwlock);		try

pthread_rwlock_unlock(&rwlock);

pthread_rwlock_destroy(&rwlock);



pthread_rwlock_t 类型	用于定义一个读写锁变量

pthread_rwlock_t	rwlock

## 171P-两种死锁



是使用锁不恰当导致的死锁



1. 对一个锁反复lock。
2. 两个线程，各自持有一把锁，请求另一把。

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630545294010-34e781cc-feb0-4ef5-8477-e8dc9a3f165b.png)



## 172P-读写锁原理

读写锁

- 锁只有一把。以读方式给数据加锁——读锁。以写方式给数据加锁——写锁。
- **读共享，写独占。**
- **写锁优先级高。**
- 相较于互斥量而言，当读线程多的时候，提高访问效率



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631284933894-95b3b57d-4a0b-4389-ae5e-9f906f26502e.png)

## 173P-rwlock



例子

 ![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631285584164-ae334a11-1c43-4a5d-bb25-6527b78a4427.png)

核心

- 读共享，写独占。
- 写锁优先级高。

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631285548097-a8da1c07-9840-49bf-bb95-58432b978a1c.png)

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631285555448-af43f086-f918-43dd-80e7-04ef68375193.png)



## 174P-午后复习



## 175P-静态初始化条件变量和互斥量



条件变量：本身不是锁！  但是通常结合锁来使用。 mutex



pthread_cond_函数

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631285968399-44e93599-96ab-4f77-8f5e-70d374ec9482.png)



初始化条件变量

pthread_cond_t cond;

1. 动态初始化：pthread_cond_init(&cond, NULL);
2. 静态初始化：pthread_cond_t cond = PTHREAD_COND_INITIALIZER;



## 176P-条件变量和相关函数wait

pthread_cond_wait函数

阻塞等待条件

```
pthread_cond_wait(&cond, &mutex);
```

作用：	

1） 阻塞等待条件变量满足

2） 解锁已经加锁成功的信号量 （相当于 pthread_mutex_unlock(&mutex)），12两步为一个原子操作

3) 当条件满足，函数返回时，解除阻塞并重新申请获取互斥锁。重新加锁信号量 （相当于， pthread_mutex_lock(&mutex);）



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631286577233-54b98edc-3d03-413b-aa9b-aeca408ff227.png)



## 177P-条件变量的生产者消费者模型分析



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631326871264-06e1cead-4e83-4e86-b953-3d3da3d9428c.png)

## 178P-条件变量生产者消费者代码预览

例子

借助条件变量模拟 生产者-消费者 问题

## 179P-条件变量实现生产者消费者代码



1. **void** err_thread(**int** ret, **char** *str) { 
2.   **if** (ret != 0) { 
3. ​    fprintf(stderr, "%s:%s\n", str, strerror(ret)); 
4. ​    pthread_exit(NULL); 
5.   } 
6. } 
7.   /*链表作为公享数据,需被互斥量保护*/ 
8. **struct** msg { 
9.   **int** num; 
10.   **struct** msg *next; 
11. }; 
12. **struct** msg *head; 
13.  
14. pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;   // 定义/初始化一个互斥量 
15. pthread_cond_t has_data = PTHREAD_COND_INITIALIZER;   // 定义/初始化一个条件变量 
16.  
17. **void** *produser(**void** *arg) { 
18.   **while** (1) { 
19. ​    **struct** msg *mp = malloc(**sizeof**(**struct** msg)); 
20.  
21. ​    mp->num = rand() % 1000 + 1;            // 模拟生产一个数据` 
22. ​    printf("--produce %d\n", mp->num); 
23.  
24. ​    pthread_mutex_lock(&mutex);             // 加锁 互斥量 
25. ​    mp->next = head;                  // 写公共区域 
26. ​    head = mp; 
27. ​    pthread_mutex_unlock(&mutex);            // 解锁 互斥量 
28.  
29. ​    pthread_cond_signal(&has_data);           // 唤醒阻塞在条件变量 has_data上的线程. 
30.  
31. ​    sleep(rand() % 3); 
32.   } 
33.  
34.   **return** NULL; 
35. } 
36.  
37. **void** *consumer(**void** *arg) { 
38.   **while** (1) { 
39. ​    **struct** msg *mp; 
40.  
41. ​    pthread_mutex_lock(&mutex);             // 加锁 互斥量 
42. ​    **if** (head == NULL) { 
43. ​      pthread_cond_wait(&has_data, &mutex);      // 阻塞等待条件变量, 解锁 
44. ​    }                          // pthread_cond_wait 返回时, 重写加锁 mutex 
45.  
46. ​    mp = head; 
47. ​    head = mp->next; 
48.  
49. ​    pthread_mutex_unlock(&mutex);            // 解锁 互斥量 
50. ​    printf("---------consumer:%d\n", mp->num); 
51.  
52. ​    free(mp); 
53. ​    sleep(rand()%3); 
54.   } 
55.  
56.   **return** NULL; 
57. } 
58.  
59. **int** main(**int** argc, **char** *argv[]) 
60. { 
61.   **int** ret; 
62.   pthread_t pid, cid; 
63.  
64.   srand(time(NULL)); 
65.  
66.   ret = pthread_create(&pid, NULL, produser, NULL);      // 生产者 
67.   **if** (ret != 0)  
68. ​    err_thread(ret, "pthread_create produser error"); 
69.  
70.   ret = pthread_create(&cid, NULL, consumer, NULL);      // 消费者 
71.   **if** (ret != 0)  
72. ​    err_thread(ret, "pthread_create consuer error"); 
73.  
74.   pthread_join(pid, NULL); 
75.   pthread_join(cid, NULL); 
76.  
77.   **return** 0; 
78. } 



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316957790-bd5585b6-294d-48c0-af93-ca1fa42caaf6.png)



## 180P-多个消费者使用while做

先看一个手动创建多个消费者的代码：

1. **int** main(**int** argc, **char** *argv[]) 
2. { 
3.   **int** ret; 
4.   pthread_t pid, cid; 
5.   srand(time(NULL)); 
6.  // 生产者 
7.   ret = pthread_create(&pid, NULL, produser, NULL);      
8.   **if** (ret != 0)  err_thread(ret, "pthread_create produser error"); 
9.  // 消费者 
10.   ret = pthread_create(&cid, NULL, consumer, NULL);      
11.   **if** (ret != 0)  err_thread(ret, "pthread_create consuer error"); 
12.   ret = pthread_create(&cid, NULL, consumer, NULL);
13.   **if** (ret != 0)  err_thread(ret, "pthread_create consuer error"); 
14.   ret = pthread_create(&cid, NULL, consumer, NULL);
15.   **if** (ret != 0)  err_thread(ret, "pthread_create consuer error"); 
16.  
17.   pthread_join(pid, NULL); 
18.   pthread_join(cid, NULL); 
19.  
20.   **return** 0; 
21. } 





![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316958223-cc3cdc39-c33e-4746-ac58-9932feee4278.png)



这个代码是有问题的

1. 两个消费者都阻塞在条件变量上，就是说没有数据可以消费。
2. 完事儿都把锁还回去了，生产者此时生产了一个数据，会同时唤醒两个因条件变量阻塞的消费者，完事儿两个消费者去抢锁。
3. 结果就是A消费者拿到锁，开始消费数据，B消费者阻塞在锁上（如下图）。
4. 之后A消费完数据，把锁归还，B被唤醒，然而此时已经没有数据供B消费了。
5. 所以这里有个逻辑错误，消费者阻塞在条件变量那里应该使用while循环。这样A消费完数据后，B做的第一件事不是去拿锁，而是判定条件变量。



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631328758769-7e3f8ae2-808b-4f67-a724-f89cfb3f2f8c.png)



## 181P-条件变量signal注意事项

pthread_cond_signal(): 唤醒阻塞在条件变量上的 (至少)一个线程。

pthread_cond_broadcast()： 唤醒阻塞在条件变量上的 所有线程。

## 182P-信号量概念及其相关操作函数



信号量

- 应用于线程、进程间同步。
- 相当于 初始化值为 N 的互斥量。  N值，表示可以同时访问共享数据区的线程数。



sem_函数

`sem_t sem;`	定义类型。



```
int sem_init(sem_t *sem, int pshared, unsigned int value);
```

参数：

​	sem： 信号量 

​	pshared：	0： 用于线程间同步

​			1： 用于进程间同步

​	value：N值。（指定同时访问的线程数）



```
sem_destroy();
```



```
sem_wait();
```

一次调用，做一次-- 操作， 当信号量的值为 0 时，再次 -- 就会阻塞。 （对比 pthread_mutex_lock）



```
sem_post();
```

一次调用，做一次++ 操作. 当信号量的值为 N 时, 再次 ++ 就会阻塞。（对比 pthread_mutex_unlock）



## 183P-信号量实现的生产者消费者



等待资源wait

释放资源post



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631287917802-4a2dbe1c-0856-4ad0-8902-c68354165de7.png)



1. /*信号量实现 生产者 消费者问题*/ 
2. \#define NUM 5         
3.  
4. **int** queue[NUM];                   //全局数组实现环形队列 
5. sem_t blank_number, product_number;         //空格子信号量, 产品信号量 
6.  
7. **void** *producer(**void** *arg) { 
8.   **int** i = 0; 
9.   **while** (1) { 
10. ​    sem_wait(&blank_number);          //生产者将空格子数--,为0则阻塞等待 
11. ​    queue[i] = rand() % 1000 + 1;        //生产一个产品 
12. ​    printf("----Produce---%d\n", queue[i]);     
13. ​    sem_post(&product_number);         //将产品数++ 
14.  
15. ​    i = (i+1) % NUM;              //借助下标实现环形 
16. ​    sleep(rand()%1); 
17.   } 
18. } 
19.  
20. **void** *consumer(**void** *arg) { 
21.   **int** i = 0; 
22.   **while** (1) { 
23. ​    sem_wait(&product_number);         //消费者将产品数--,为0则阻塞等待 
24. ​    printf("-Consume---%d\n", queue[i]); 
25. ​    queue[i] = 0;                //消费一个产品  
26. ​    sem_post(&blank_number);          //消费掉以后,将空格子数++ 
27.  
28. ​    i = (i+1) % NUM; 
29. ​    sleep(rand()%3); 
30.   } 
31. } 
32.  
33. **int** main(**int** argc, **char** *argv[]) 
34. { 
35.   sem_init(&blank_number, 0, NUM);        //初始化空格子信号量为5, 线程间共享 -- 0 
36.   sem_init(&product_number, 0, 0);        //产品数为0 
37. 
38.   pthread_t pid, cid; 
39.   pthread_create(&pid, NULL, producer, NULL); 
40.   pthread_create(&cid, NULL, consumer, NULL); 
41.  
42.   pthread_join(pid, NULL); 
43.   pthread_join(cid, NULL);  
44.   sem_destroy(&blank_number); 
45.   sem_destroy(&product_number); 
46.   **return** 0; 
47. } 



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630316959900-a4831bee-c8f8-46c2-8a57-d0e09ea1da30.png)

## 184P-总结



# 一

## 01P-复习-Linux网络编程

https://www.bilibili.com/video/BV1iJ411S7UA

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631330452605-6521bfe0-e4f4-42da-9c88-8e784278e736.png)

## 02P-信号量生产者复习