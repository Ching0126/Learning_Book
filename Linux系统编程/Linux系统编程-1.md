# Linux系统编程-1

# 一

## 01P-Linux命令基础习惯-Linux系统编程

`cat /etc/shells`	查看当前可使用的shell

```bash
$ cat /etc/shells
# /etc/shells: valid login shells
/bin/sh
/bin/bash
/bin/rbash
/bin/dash
/bin/zsh
/usr/bin/zsh
```



主键盘快捷键：

上					Ctrl-p	

下					Ctrl-n	

左					Ctrl-b	

右					Ctrl-f	

Del					Ctrl-d	delete		光标后面的

Home				Ctrl-a				first letter

End					Ctrl-e				end

Backspace	 		Backspace    	delete光标前面的单个字符

清除整行   		Ctrl-u    

删除光标到行末   	Ctrl-k

显示上滚    		Shift-PgUp

显示下滚    		Shift-PgDn

增大终端字体		Ctrl-Shift-+

减小终端字体		Ctrl- -

新打开一个终端 	Ctrl-Alt-T

清屏				Ctrl-l      直接用clear也行

## 02P-类Unix系统目录

Linux系统目录：

​	bin：存放二进制可执行文件

​	boot：存放开机启动程序

​	dev：存放设备文件： 字符设备、块设备

​	home：存放普通用户

​	etc：用户信息和系统配置文件 passwd、group

​	lib：库文件：libc.so.6

​	root：管理员宿主目录（家目录）

​	usr：用户资源管理目录  unix software resource

Linux下“所见皆文件”

鼠标设备文件

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508053759-1d7e0ed4-d370-4491-b244-ddd7a351c986.png)



## 03P-目录和文件操作1

Linux系统文件类型： 7/8 种

​	普通文件：-

​	目录文件：d

​	字符设备文件：c

​	块设备文件：b

​	软连接：l

​	管道文件：p

​	套接字：s

​	未知文件。

文件权限说明

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508057153-ef275b5b-d2ef-4ae2-85d0-6693029aa9d9.png)

目录项详细信息

文件权限  硬链接计数  所有者  所属组  大小  时间  文件名/文件夹名

权限具体展开

-rw-r—r—

1234567890

1代表文件类型

234代表所有者读写执行权限

567代表同组用户读写执行权限

890代表其他人读写执行权限



`which` instruct查看instruct命令所在目录位置

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508057436-1a7fc65e-2da3-442b-b7c4-9f314294f573.png)



隐藏终端中的路径

`vi ~./bash`    打开使用的shell环境配置文件

末尾添加 PS1=$  保存退出，重启终端即可

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508057663-99c7d7ea-46ec-4816-bfe5-e6da1a33b790.png)



效果如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508057976-18ffeae7-f785-4137-8442-99a6495ce9ba.png)



`cp -a dirname1 dirname2` 复制目录1及其下所有文件到目录2

`cp -r dirname1 dirname2` 递归复制目录1到目录2

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508060090-35d1c427-c5c7-49fb-9a5a-987ad4f56ced.png)

这里-a和-r的差别在于，-a是完全复制，文件权限，改动时间什么的也完全相同。



## 04P-目录和文件操作2



`more filename`  和cat差不多，但是对于大文件查看很强势

空格翻页，回车一行

用q或者Ctrl-c退出



`less filename`  也和cat差不多

空格翻页，回车一行

用q或者Ctrl-c退出



`head -n filename`  查看文件前n行

不加-n参数默认查看10行



`tail -n filename` 查看文件后n行

默认查看10行，顺序显示的，不是倒序显示，1-10查看后4行是78910



`tree` 命令，查看当前目录结构树

需要安装tree

## 05P-软链接和硬链接



`ln -s file file.s` 创建一个软链接

软链接就像windows下的快捷方式

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631073340327-ffdbede1-46f6-4836-83bb-7570aa7155ad.png)

这里软链接的大小是文件路径大小



Linux下的软链接行为和windows下的快捷方式差不多，但是如果是用相对路径创建的软链接，在软链接移动之后就会失效，无法访问。这一点和windows快捷方式不同，windows快捷方式随便放哪里都行。



失效的软链接

所以，创建软链接最好使用绝对路径，移动后，绝对路径创建的软链接不会失效

注意，软链接的权限指的是这个软链接本身的权限，不是软链接指向文件的权限



`ln file file.h`  创建一个硬链接

创建硬链接后，文件的硬链接计数+1

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631078962606-a60c96cb-9528-4034-91b2-d38c052d1120.png)

可以看到文件和硬链接的Inode是相同的，每个文件都有唯一的Inode，直观理解起来就像洗佳佳里面的引用，对于同一个文件，无论有多少引用，在访问时，都是这个文件，所以修改就是同步的。



当删除一个硬链接时，文件的硬链接计数-1，当这个计数减为0时，才会删除这个文件

即使删除硬链接指向的文件，也只会让硬链接计数-1



## 06P-创建修改用户和用户组

`whoami` 查看当前用户



`chmod` 修改权限操作



第一种，文字设定法

```
chmod [who] [+|-|=] [mode] filename
```

操作对象who可以是下述字母中的任一个或者它们的组合

u 		表示”用户(user)”，即文件或目录的所有者

g 		表示”同组(group)用户”，即与文件所有者有相同组ID的所有用户

o		表示”其他(others)用户”

a		表示”所有(all)用户”，它是系统默认值



操作符号可以是：

+	添加某个权限

- 取消某个权限

=	赋予给定权限并取消其他所有权限（如果有的话）



第二种，数字设定法

`chmod 操作码 filename`  直接用操作码修改文件权限



对于file2的权限

-rw-rw-r—

 421421421

三个组的权限都用二进制编号，比如要设置当前用户对文件的读写和执行权限，则当前用户的操作权限为4（读）+ 2（写）+ 1（执行） = 7

用户组和其他用户的权限设置也是一样的



对于file2的权限-rw-rw-r—

我们设置如下：

所有者 		rwx = 7

所有者所在组	rw = 6

其他用户		r = 4

操作码就是764



修改文件所属：

chown 用户名 文件



修改组：chgrp 组名 文件名

创建用户：sudo adduser 新用户名

删除用户： sudo deluser 用户名

删除组：sudo delgroup 用户组名



## 07P-find命令1

find命令：找文件

​	-type 按文件类型搜索  d/p/s/c/b/l/ f:文件

​	-name 按文件名搜索

​		find ./ -name "*file*.jpg"

​	-maxdepth 指定搜索深度。应作为第一个参数出现。

​		find ./ -maxdepth 1 -name "*file*.jpg"

​	-size 按文件大小搜索. 单位：k、M、G

​		find /home/itcast -size +20M -size -50M

​	-atime、mtime、ctime 天  amin、mmin、cmin 分钟。

​	-exec：将find搜索的结果集执行某一指定命令。

​		find /usr/ -name '*tmp*' -exec ls -ld {} \;

​	-ok: 以交互式的方式 将find搜索的结果集执行某一指定命令

​	-xargs：将find搜索的结果集执行某一指定命令。  当结果集数量过大时，可以分片映射。

​		find /usr/ -name '*tmp*' | xargs ls -ld 

​	-print0：

​		find /usr/ -name '*tmp*' -print0 | xargs  -0 ls -ld 



-atime、mtime、ctime 天  amin、mmin、cmin 分钟。

按照时间搜索

a	表示最近访问时间

m	表示最近更改时间，指更改文件属性一类的

c	表示最近改动时间，指更改文件内容



## 08P-午后复习

## 09P-find命令2

-exec：将find搜索的结果集执行某一指定命令。

​		`find /usr/ -name '*tmp*' -exec ls -ld {} \;`

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508073667-4d53ae9a-dd12-4192-8e42-4e91fffa3d55.png)



-ok: 以交互式的方式 将find搜索的结果集执行某一指定命令

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508073971-9c537c29-9288-4f2b-aca3-2aa20d1d59a9.png)



## 10P-grep和xargs

grep命令：找文件内容

grep -r 'copy' ./ -n

-n参数：:显示行号



ps监控后台进程工作情况，默认只显示当前可以和用户交互的进程



ps aux | grep 'cupsd'  -- 检索进程结果集。

下图显示和内核相关进程

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508074765-13709755-d1e9-4819-a105-21ebff745367.png)

使用grep搜索进程，有一条结果是搜索进程本身



find … | xargs ls -l 对find操作的结果集进行操作

等价于

find … -exec ls -l {} \；

两者差别在于当结果集合很大的时候，xargs会对结果进行分段处理，所以性能好些

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631080505786-1e6cd36f-111a-4634-b4ea-fd8ff03839b3.png)

-xargs：将find搜索的结果集执行某一指定命令。  当结果集数量过大时，可以分片映射。

find /usr/ -name '*tmp*' | xargs ls -ld 



创建名字带空格的文件方法：

- 第一个方法，文件名加引号
- 第二个方法，转义



但xargs也有缺陷，xargs默认用【空格】来分割结果集，当文件名有空格的时候，会因为文件名被切割失效

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508074980-2ee210b5-f1d6-4200-a6e6-737fa10d96e8.png)

xargs缺陷演示

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508076210-69645e68-93fa-4d0f-8f2e-9d8d40a73e9d.png)



解决xargs的缺陷，指定切分符号位null

-print0：

​	`find /usr/ -name '*tmp*' -print0 | xargs  -print0 ls -l`

   第一个print0指定结果集分隔为null，第二个print0指定xargs分隔为null

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508076505-9dfd7102-ed2c-4b5c-8409-b04a323f4da0.png)



## 11P-xargs加强和awk说明

## 12P-软件包安装

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631081618506-11ccc31e-4940-42ef-84e5-fa42bf9723b1.png)

使用安装包进行软件安装

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508078066-1f942d63-fbeb-42b3-b72c-f32d43009c26.png)



## 13P-压缩命令gzip和bzip2

两者都是配合tar打包命令使用

这两个压缩的缺陷都是只能对单个文件进行压缩，一来不能压目录，二来不能打包

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631084261950-485433f5-ac02-4259-9d57-af72ef4fcfbe.png)

tar压缩：

​	1. tar -zcvf 	要生成的压缩包名	压缩材料。

​		tar zcvf  test.tar.gz  file1 dir2   使用 gzip方式压缩。

​		tar jcvf  test.tar.bz2  file1 dir2   使用 bzip2方式压缩。



tar解压：

​	将 压缩命令中的 c --> x

​		tar zxvf  test.tar.gz   使用 gzip方式解压缩。

​		tar jxvf  test.tar.bz2   使用 bzip2方式解压缩。





所以tar zcvf 是两条指令的结合版本

对zcvf进行解释：

z:zip，压缩

c:create，创建

v:vision，显示压缩过程，可以去掉，直接用zcf，但这样不显示压缩过程

f:file，文件



file filename 文件照妖镜

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508079999-7c0517cf-9ef1-4a23-ac13-396b447d9e49.png)

## 14P-rar压缩和zip压缩



rar压缩，需要安装rar

```
rar a -r newdir dir
```

打包，把dir压缩成newdir.rar，如果压缩材料里没有目录，-r参数可以省去

`unrar x newdir.rar`  解压rar文件



`sudo aptitude show softname`  查看软件安装信息

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508083147-8c0c2ddf-bebb-4d5d-b03f-4d6484b8d044.png)



zip压缩：

```
zip -r dir.zip dir 
```

zip解压

```
unzip dir.zip
```

zip文件在windows和linux下通用

## 15P-其他命令

```
who
```

查看当前在线上的用户情况

```
whoami
```

查看当前用户，不带有进程

```
jobs
```

查看操作系统当前运行了哪些用户作业

```
env
```

环境变量

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508085634-387a574d-bca5-4005-b8a6-9410a58ba4b2.png)

```
top
```

文字版任务管理器

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631083114455-a547ecaf-c7a5-402e-bbc1-92a5644bca1e.png)

```
umask
```

设置文件掩码

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631083394738-effeb023-7411-4244-9fe3-18ac54d3a531.png)

```
ifconfig
```

查看网卡信息

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508086669-3c8470a7-5052-4d05-bd05-b4ced0205e9b.png)



## 16P-总结

## 17P-复习

创建一个目录，大小默认是4096k

# 二

## 18P-vim的三种工作模式

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630539326204-aef863f9-faaf-407c-ad53-f62c28408c01.png)

## 19P-vim基本操作-跳转和删字符

i 进入编辑模式，光标前插入字符					I 进入编辑模式，光标所在行的行首插入

a 进入编辑模式，光标后插入字符					A 进入编辑模式，光标所在行的行末插入字符

o 进入编辑模式，光标所在行的下一行插入			O 进入编辑模式，光标所在行的上一行插入字符

s 删除光标所在字符并进入编辑模式				S 删除光标所在行并进入编辑模式





x 删除光标所在【字符】，工作模式不变

dw 删除光标所在【单词】，要求光标在首字母上，如果不在首字母，只会删除当前位置到单词末，工作模式不变

D  删除光标所在位置到行末，工作模式不变

0(数字) 光标移到【行首】，工作模式不变

$ 光标移到【行尾】，工作模式不变

d0 删除光标所在位置到行首，工作模式不变

d$ 删除光标所在位置到行末，工作模式不变



命令模式下行跳转

line-G  			缺点是没有回显

:line-回车		末行模式下行跳转

gg （命令模式）	跳转首行

G  （命令模式）	跳转末行

gg=G （命令模式）无回显

% （命令模式）	 光标处于左大括号时，使用%跳转到对应右大括号，再按%跳回去



自动缩进——在这之前要进行vimrc修改，不然自动缩进是8个空格

- ubuntu的vimrc位置在/etc/vim/vimrc
- 在文件末尾添加三行：

set tabstop=4    //设置制表符宽度为4

set softtabstop=4  // 设置软制表符宽度为4

set shiftwidth=4    // 设置缩进空格数为4



## 20P-vim基本操作-删除

替换单个字符

r 命令模式下替换光标选中字符



一段删除，即删除指定区域

光标选中要删除的首字符，按v进入可视模式，再使用hjkl移动到要删除的末尾，按d删除



删除整行：

dd，删除光标所在行

n+dd ，删除从光标开始的n行



## 21P-vim基本操作-复制粘贴

yy 复制光标所在行

小p  向后粘贴剪切板内容，如果复制整行，这里是粘贴在光标所在位置的下一行

大P  向前粘贴剪切板内容，如果是整行，这里是粘贴在光标所在位置的上一行



这里提一下，上一节里的dd，不是删除，而是剪切，小时的内容去了剪切板，而不是删掉了

p和P粘贴会出现换行，主要原因是复制整行时，会把行末的换行符也复制下来。



n-yy  复制光标所在位置的n行，包括光标所在行



## 22p-vim基本操作-查找和替换



查找

/+findname   命令模式下查找

按回车键启动查找后，按n，会自动找下一个，N跳到上一个

查找光标所在单词

光标在目标单词上时，*或者#查找下一个，这里不要求光标必须在首字母上





替换：末行模式下进行

单行替换  

光标置于待替换行，   :s /待替换词/替换词

全文替换

:%s /待替换词/替换词           这个默认替换每行的首个，一行有多个目标词时，后面的不会变

:%s /待替换词/替换词/g       真正意义上的全局替换

区域替换

:24,35s /待替换词/替换词/g     替换24-35行之间的目标词



末行模式下历史命令

Ctrl-p 上一条命令

Ctrl-n 下一条命令



## 23P-vim基本操作-其他

命令模式下

u  撤销操作

Ctrl-r  反撤销



分屏，末行模式下

:sp   水平分屏

:vsp  竖直分屏

分屏命令+filename，分屏并打开这个文件

分屏后屏幕切换，Ctrl-w-w

使用:q退出光标所在窗口

使用:qall退出所有窗口



从vim中跳转manpage，命令模式下

将光标放在待查看单词上，按K，默认看第一卷

n+K，查看第n卷



查看宏定义：命令模式

​	光标放在待查看词上，[+d即可查看



vim下使用shell命令：末行模式

​	:! + 命令

操作后，会切换至终端显示结果，出现如下画面，按Enter后回到vim界面

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508089475-da654f0c-ae2b-41dd-883e-707e69168a13.png)



## 24P-vim配置思路



两个vim配置文件

1. /etc/vim/vimrc
2. ~/.vimrc

其中，第二个配置文件会优先加载，属于用户配置



## 25P-gcc编译4步骤

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630539595971-7be3c275-25a2-43a4-9b83-98b64dc3947b.png)

## 26P-gcc编译常用参数



```
gcc -I ./hellodir hello.c -o hello
```

- 当头文件和源码不在一个目录下时，需要指定头文件
- 其中-I参数指定头文件所在位置，位置可以在编译文件前，也可以在后面



-I			指定头文件所在目录位置

-c 			只做预处理，编译，汇编。得到二进制文件 !!!!!!!

-g 			编译时添加调试文件，用于gdb调试

-Wall 		显示所有警告信息

-D  			向程序中“动态”注册宏定义

-l			指定动态库库名

-L			指定动态库路径



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508091141-5ae44f5a-eb53-4d86-a3c4-ea3da712de06.png)



## 27P-午后复习



## 28P-动态库和静态库理论对比

- 静态库在文件中静态展开，所以有多少文件就展开多少次，非常吃内存，100M展开100次，就是1G，但是这样的好处就是静态加载的速度快
- 使用动态库会将动态库加载到内存，10个文件也只需要加载一次，然后这些文件用到库的时候临时去加载，速度慢一些，但是很省内存



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508091594-0e96a592-c2f7-42ab-b6a5-329e084dfbcc.png)



## 29P-静态库制作与使用

静态库名字以lib开头，以.a结尾

例如：libmylib.a



静态库制作及使用步骤：

​	1. 将 .c 生成 .o 文件

​		gcc -c add.c -o add.o

​	2. 使用 ar 工具制作静态库

​		ar rcs  lib库名.a  add.o sub.o div.o

​	3. 编译静态库到可执行文件中：

​		gcc test.c lib库名.a -o a.out

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631089179227-8f3e1a2c-97c9-453d-aedf-7765c527d042.png)

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631089202973-bf55ed54-b891-438f-8d01-de87a9ad4133.png)

## 30P-静态库使用及头文件对应



上一节出现的警告，用编译器隐式声明来解决的

编译器只能隐式声明返回值为int的函数形式：int add(int ,int )；

如果函数不是返回的int，则隐式声明失效，会报错



在test.c中加入函数声明：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508093704-fe36441a-8389-4f74-b9a1-3395fe0ccf93.png)



再次进行编译，就不会报错了：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508094059-23b584ec-9106-4dbe-bc6c-989b6d4d9b4b.png)



上面这个方法，需要库的使用者知道库里的函数，完事儿一个一个加到代码里，不太行



下面使用头文件的方法来加载静态库

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508094356-c2be6922-1c8f-4937-9f37-129c0b7ce0f0.png)

左边的define为头文件守卫，防止在代码中多次include头文件，多次展开静态库，带来的额外开销



这样也不会报错了，而且更加科学

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508094722-28225179-edfe-45c0-bd61-da4473bd6f66.png)



下面将静态库和头文件分别放至其他目录下

运行过程如下

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631089956787-11bb6300-6b5b-4f03-9069-158b4ea638d5.png)



## 31P-动态库制作-生成与位置无关代码

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508095297-81589af4-af1e-4136-8699-f66218187e5b.png)

- 写在源代码里的函数，相对main函数偏移是一定的，链接时，回填main函数地址之后，其他源代码里的函数也就得到了地址。
- 动态库里的函数会用一个@plt来标识，当动态库加载到内存时，再用加载进去的地址将@plt替换掉。



## 32P-动态库制作-演示



制作动态库的步骤

1. 生成位置无关的.o文件

gcc -c add.c -o add.o -fPIC

使用这个参数过后，生成的函数就和位置无关，挂上@plt标识，等待动态绑定

1. 使用 gcc -shared制作动态库

​	gcc -shared -o lib库名.so add.o sub.o div.o

1. 编译可执行程序时指定所使用的动态库。-l:指定库名 -L:指定库路径

​	gcc test.c -o a.out -l mymath -L ./lib

1. 运行可执行程序./a.out



过程演示：

步骤一：生成位置无关的.o文件

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508095757-9a8c63c2-a4d1-47d8-8884-843147689adb.png)



步骤二：制作动态库 gcc -shared -o lib库名.so add.o sub.o div.o

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508096135-4df57bf5-1747-483e-b45a-0c6deaed6f5b.png)



步骤三：编译程序

文件分布如下：动态库在lib目录下，头文件在inc目录下

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508096428-803faac2-189a-4111-a278-b665205cf0f0.png)

下面编译文件

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508096863-c9e73553-3527-41e8-913d-0df5c2e0fba2.png)



步骤四：执行文件，出错

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508097195-ee5f9625-6536-4d11-afe3-19e139714fb6.png)



## 33P-动态库加载错误原因及解决方式

出错原因分析：

连接器：		工作于链接阶段，工作时需要 -l 和 -L

动态链接器：	工作于程序运行阶段，工作时需要提供动态库所在目录位置



指定动态库路径并使其生效，然后再执行文件

通过环境变量指定动态库所在位置：

```
export LD_LIBRARY_PATH=动态库路径
```



当关闭终端，再次执行a.out时，又报错。

这是因为，环境变量是进程的概念，关闭终端之后再打开，是两个进程，环境变量发生了变化。

要想永久生效，需要修改bash的配置文件：`vi ~./bashrc`

修改后要使配置文件立即生效：. .bashrc    或者  source .bashrc  或者重开终端让其自己加载

这下再执行a.out就不会报错了



## 34P-动态库加载错误原因及解决方式2

解决方式：				

【1】 通过环境变量：  export LD_LIBRARY_PATH=动态库路径

​	./a.out 成功！！！  （临时生效， 终端重启环境变量失效）

【2】 永久生效： 写入 终端配置文件。  .bashrc  建议使用绝对路径。

​	1) vi ~/.bashrc

​	2) 写入 export LD_LIBRARY_PATH=动态库路径  保存

​	3）. .bashrc/  source .bashrc / 重启 终端  ---> 让修改后的.bashrc生效

​	4）./a.out 成功！！！ 

【3】 拷贝自定义动态库 到 /lib (标准C库所在目录位置)

【4】 配置文件法

​	1）sudo vi /etc/ld.so.conf

​	2) 写入 动态库绝对路径  保存

​	3）sudo ldconfig -v  使配置文件生效。

​	4）./a.out 成功！！！--- 使用 ldd  a.out 查看



## 35P-扩展讲解-数据段合并

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508097866-2cab3a87-b24e-4b11-b3a3-7448b32c3a44.png)



## 36P-总结

## 37P-复习



# 三

## 38P-gdb调试基础指令

使用gdb之前，要求对文件进行编译时增加-g参数，加了这个参数过后生成的编译文件会大一些，这是因为增加了gdb调试内容

gdb调试工具：   大前提：程序是你自己写的。  ---逻辑错误



基础指令：

​	-g：使用该参数编译可以执行文件，得到调试表。

​	gdb ./a.out

​	list： list 1  列出源码。根据源码指定 行号设置断点。

​	b：	b 20	在20行位置设置断点。

​	run/r:	运行程序

​	n/next: 下一条指令（会越过函数）

​	s/step: 下一条指令（会进入函数）

​	p/print：p i  查看变量的值。

​	continue：继续执行断点后续指令。

​	finish：结束当前函数调用。 

​	quit：退出gdb当前调试。



gcc gdbtest.c -o a.out -g

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508098254-a7c1a9e7-6d8f-4d8c-ae68-5599b93ac88f.png)

gdb test  启动对test的调试

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508098585-3943a4d5-5624-4b48-ba0f-fc073fe18215.png)

l/list n  从第n行开始显示程序，后续继续输入list/l，就可以显示后面的代码

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508098903-573966e8-35ab-4b6f-90e7-32eef85befdf.png)

break/b n   在第n行设置断点，断点那一行不会执行

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508099142-432dd47a-0b71-4ee6-95db-643888f19c27.png)



run/r 		运行程序

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508099363-5ac60efb-3eea-4ce7-830e-c6a538de6597.png)



接下来按next/n/step/s继续向下执行

next/n :下一个，调用函数就跑

step/s :单步，会进入调用的函数

要注意的是，如果是系统函数，按s就出不来了，这时用until+行号直接执行到行号处

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508099641-ccc54278-db7e-4c31-afec-e30144d74709.png)



出不来的示例

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508099995-08eb39bd-6561-4c50-a428-1de41b6385a8.png)



使用until出来

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508100321-6f911360-599c-4f43-a0d0-25b9494b7aa4.png)



print/p i		查看i变量的值

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508100584-d7b040e3-c9e9-43e6-8750-4ded6c7416b1.png)



continue  直接运行到结束

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508100981-9b78c268-2317-4372-b77b-1e73658022eb.png)



## 39P-gdb调试其他指令



其他指令：

​	run：使用run查找段错误出现位置。

​	set args： 设置main函数命令行参数 （在 start、run 之前）

​	run 字串1 字串2 ...: 设置main函数命令行参数

​	info b: 查看断点信息表

​	b 20 if i = 5：	设置条件断点。

​	ptype：查看变量类型。

​	bt：列出当前程序正存活着的栈帧。

​	frame： 根据栈帧编号，切换栈帧。

​	display：设置跟踪变量

​	undisplay：取消设置跟踪变量。 使用跟踪变量的编号。

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631106765084-8ac23495-2945-451d-89ab-422b1f425e54.png)

## 40P-gdb常见错误说明



没有符号被读取—编译时没加-g参数

file后面加使用-g编译的文件，可以不用退出，gdb直接读取后进行调试。



## 41P-makefile基础规则

makefile： 管理项目。

​	命名：makefile	 Makefile  --- make 命令



1 个规则	2 个函数	3 个自动变量



1 个规则：

目标：依赖条件

​	（一个tab缩进）命令

```c
hello:hello.c
	gcc hello.c -o hello
```

1. 目标的时间必须晚于依赖条件的时间，否则，更新目标
2. 依赖条件如果不存在，找寻新的规则去产生依赖条件。



```c
hello:hello.o
	gcc hello.c -o hello
hello:hello.o
	gcc -c hello.o -0 hello.o
```

执行make指令

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508101658-b00a8b07-dd04-47e7-864f-f4c147d5c6c8.png)

运行hello，没有问题

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508102019-b405d3db-8d96-4327-ae7e-b3b4555dea4b.png)



## 42P-makefile的一个规则



修改hello.c如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508102265-079ef8f8-d4a3-476e-b86d-0f20380d9f75.png)



此时要进行编译，则需要【多文件联合编译】：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508102483-c4eb7b1d-0661-48bc-a2b5-84be2afe910f.png)



对这个新的代码，写出下面的makefile

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508102685-1770cedf-2e23-48f1-89c5-25b073f0eaae.png)



执行：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508103078-041a5a4b-6c77-423e-9657-59656b1bbc96.png)



此时，修改add.c为下图

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508103264-77f7ad09-12ec-440a-a921-301b02dd2916.png)

此时，再使用make，发现了问题

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508103538-68202d4c-de6f-4e5e-ac1d-b02194e56984.png)

可以看到，只修改add.c，但是编译的时候，其他.c文件也重新编译了。。。。。

明明只改了一个，全部都重新编译了



于是将makefile改写如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508104171-d7dfc90f-ccaf-45c5-adfb-c50c1975f7c9.png)



修改divi1.c，执行make指令如下

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631109036377-fce4f1fc-66da-4524-806b-dda06f0c36ee.png)

makefile检测原理：

- 修改文件后，文件的【修改时间】发生变化，会出现目标文件的时间早于作为依赖材料的时间，出现这种情况的文件会重新编译。
- 修改sub.c后，sub.o的时间就早于sub.c ，a.out的时间也早于sub.o的时间了，于是重新编译这俩文件了。



ALL来指定终极目标

- makefile的依赖是从上至下的，换句话说就是目标文件是第一句里的目标，如果不满足执行依赖，就会继续向下执行。如果满足了生成目标的依赖，就不会再继续向下执行了。 
- make会自动寻找规则里需要的材料文件，执行规则下面的行为生成规则中的目标。



关于makefile指定目标问题，先修改makefile如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508106773-b75e7456-8647-4205-8bd3-f3dbc4718aa1.png)

只是将a.out放在了文件末尾

执行make，如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508106968-e4b6578f-a976-4407-924e-89a0bd7ba865.png)

这是因为，makefile默认第一个目标文件为终极目标，生成就跑路，这时候可以用【ALL来指定终极目标】

指定目标的makefile

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508107279-b1fd70b8-9b37-423b-b231-ccafc5f846aa.png)





## 43P-午后回顾



## 44P-makefile两个函数和clean



1. `src = $(wildcard *.c)`

匹配当前工作用户下的所有.c文件。将文件名组成列表，赋值给变量src。

找到当前目录下所有后缀为.c的文件，赋值给src



1. `obj = $(patsubset %.c,%.o, $(src))`

将参数3中，包含参数1的部分，替换成参数2

把src变量里所有后缀为.c的文件替换成.o



1. 加了`clean`部分

模拟执行clean部分

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508108873-8521f9b2-da68-4577-8812-811fe1959c4c.png)

rm前面的`-`，代表出错依然执行。

删除不存在文件不加这`-`，就会报错，告诉你有一个文件找不到。加了`-`就不会因为这个报错。



由于没有文件变动，a.out的时间戳晚于所有依赖文件，这里make就没干活

于是，执行时加新指令，先模拟执行`clean`部分

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1631148055106-b0bea89d-deb8-4be3-985f-cf3b05da8eff.png)



## 45P-makefile3个自动变量和模式规则



3个自动变量

` $@ `：在规则命令中，表示规则中的目标

` $< `：在规则命令中，表示规则中的第一个条件，如果将该变量用在模式规则中，它可以将依赖条件列表中的依赖依次取出，套用模式规则

` $^ `：在规则命令中，表示规则中的所有条件，组成一个列表，以空格隔开，如果这个列表中有重复项，则去重



用自动变量修改makefile，如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508110464-d5d40f8e-ac49-4ecd-bb0e-972fcaf187a0.png)



上面这个makefile，可扩展性不行。

比如，要添加一个乘法函数，就需要在makefile里面增加乘法函数的部分。不科学，所以，模式规则就来了



```c
%.o:%.c
	gcc -c $< -o $@
```



修改makefile，如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508111473-f47b86fc-801f-42de-88a0-74101dca364e.png)



继续优化makefile，使用静态模式规则，就是指定模式规则给谁用，这里指定模式规则给obj用，以后文件多了，文件集合会有很多个，就需要指定哪个文件集合用什么规则

```c
$(obj):%.o:%.c
	gcc -c $< -o $@
```

修改后makefile如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508113234-ffe668a9-dbdc-497b-87fa-a742d97811e0.png)



再来一个扩展

当前文件夹下有ALL文件或者clean文件时，会导致makefile瘫痪，如下所示，make clean没有工作

用伪目标来解决，添加一行   

```c
.PHONY: clean ALL
```

还有一个扩展就是，编译时的参数，`-g,-Wall, ... `这些，可以放在makefile里面

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508115746-f6c52927-1611-458a-b76c-79056c204b99.png)



## 46P-习题和作业



- 源码add.c,sub.c这些在src目录下，.o文件要放在obj目录下，头文件head.h在inc目录下。如下所示：

- 将hello.c中的头文件单独拿出来



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508116716-de9ba6a5-1ce3-45f3-a96f-5cf30d93fcad.png)

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508117230-9531bc3b-8b50-4636-85af-693dffe52278.png)



- 修改makefile如下，主要是注意%的匹配理解，只匹配文件名，目录位置要手动添加



![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508117814-715af9b7-1325-4573-b11a-f88e85131e2a.png)



- 调用clean删除文件，直接用就行

![img](https://cdn.nlark.com/yuque/0/2021/png/12581134/1630508118821-df6a74a0-20da-4f51-95e1-ea54da783304.png)



- 如果makefile的名字变化一下，比如，叫m6

用m6执行makefile，     make -f m6

用m6执行clean 			make -f m6 clean