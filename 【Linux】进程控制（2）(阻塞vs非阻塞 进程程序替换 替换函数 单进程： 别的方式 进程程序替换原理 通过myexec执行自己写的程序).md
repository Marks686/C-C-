
## 进程等待


### 阻塞vs非阻塞
1. waitpid调用成功 && 子进程没退出子进程没有退出，我的waitpid没有等待失败，仅仅是没有检测到子进程没有退出。
2. waitpid调用成功 && 子进程退出了。
3. waitpid调用失败。



`WNOHANG`： 非阻塞->子进程没有退出，父进程检测的时候立即返回。


![在这里插入图片描述](https://img-blog.csdnimg.cn/e8ea1325a94a44c490fe71faa7ef77ad.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/5e7d690e3d2148d291d4544c32b3e183.png)
## 进程程序替换

创建子进程的目的：
a.想让子进程执行父进程代码的一部分。（执行父进程对应的磁盘代码中的一部分）
b. 想让子进程执行一个全新的程序。（让子进程想办法，**加载磁盘上指定的程序，执行新程序代码和数据**）->这种操作我们称为进程的程序替换！


见见进程替换：
### 替换函数

```cpp
#include <unistd.h>`
int execl(const char *path, const char *arg, ...);
int execlp(const char *file, const char *arg, ...);
int execle(const char *path, const char *arg, ...,char *const envp[]);
int execv(const char *path, char *const argv[]);
int execvp(const char *file, char *const argv[]);
int execve(const char *path, char *const argv[], char *const envp[]);
```


打开man手册，查看execl
![在这里插入图片描述](https://img-blog.csdnimg.cn/ce11d9d8e90946abaa9a527a87c4b878.png)
execl这样的函数**将指定的程序加载到内存中，让指定进程进行执行**。

`int execl(const char *path, const char *arg, ...);`
如何执行？
想要把指定的程序加载到指定内存必须得解决两个问题：1.先找到执行哪一个程序，环境变量path的意义就是帮助我们找到程序，第一个参数就解决了这个问题。2.如何执行？第二个参数解决这个问题。（在命令行中怎么执行，就怎么传参）


`...`是C语言中的可变参数列表，可变参数列表它是一个可以给C语言函数传递不同个数的参数。
例如：
`man 3 printf`
![在这里插入图片描述](https://img-blog.csdnimg.cn/c77c11d2d00a4423b3f74d88ef52780c.png)

`man 3 scanf`

![在这里插入图片描述](https://img-blog.csdnimg.cn/af54dfc8d15c4832b2462b2f9f830d82.png)
它们都是可变参数。

#### 单进程：


我们要执行user路径下bin路径下的ls，第一个参数就是`"/user/bin/ls"`第二个参数的传参方法：在命令行中怎么执行，就怎么传参。不想带选项就以NULL结尾(all exec* end of NULL)。如下：

```cpp
execl("/user/bin/ls","ls",NULL);  
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/ef1edb0863cf43eca5df867d7630eb7a.png)
编译运行我们所写的程序，

![在这里插入图片描述](https://img-blog.csdnimg.cn/b13c2efcc9124ede9a11b55e5745a789.png)

发现达到了我们输入`ls`命令的目的。
继续补充代码:

```cpp
 execl("/usr/bin/ls","--color=auto","-a","-l","ls",NULL);
```
得到结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/a7f3bdf3b3014ee6bcf2f44d00d8dbdc.png)

**我们可以让我们的程序用C语言把别人的程序全部调用起来这就叫做程序替换**。


再比如：

```cpp
 execl("/usr/bin/top","top",NULL);
```

把任务管理器调用起来了。


![在这里插入图片描述](https://img-blog.csdnimg.cn/52d83e29d7624d9e8bc245a432ed3298.png)


---

![在这里插入图片描述](https://img-blog.csdnimg.cn/9a398fcdb0284d88a6009592d3c8b47f.png)
l：list：将参数一个一个的传入exec*

---


#### 别的方式
![在这里插入图片描述](https://img-blog.csdnimg.cn/a9d26dc244f34d98b84dcbaa7e3155a3.png)

```cpp
 execlp("ls","ls",NULL);
```


p：path：如何找到程序的功能，带p字符的函数，不用告诉exec*程序的路径，只要告诉是谁就行，exec*会自动在环境变量PATH，进行可执行程序的查找。


两个ls不重复，一个是告诉系统我是谁？一个告诉系统，我想怎么执行。


---

![在这里插入图片描述](https://img-blog.csdnimg.cn/f5cdd7ca22c34bae928fd5798a2b8c2b.png)
```cpp
char *const argv_[] = {
     "ls",
     "-a",
     "-l",
     "--color=auto",
     NULL
};
 execv("usr/bin/ls",argv_);
```


v：vector：可以将所有的执行参数，放入数组中，统一传递，而不用进行使用可变参数方案。


---

![在这里插入图片描述](https://img-blog.csdnimg.cn/9c7d2c05d0834bf88339e592f79709b8.png)

```cpp
char *const argv_[] = {
     "ls",
     "-a",
     "-l",
     "--color=auto",
     NULL
};
 execvp("ls",argv_);
```


v和p上面都解释了。

---
![在这里插入图片描述](https://img-blog.csdnimg.cn/a2f0a91afa3749cfbc691cb48c6b09a1.png)

```cpp
char *const envp_[] = {
     (char*)"MYENV=1111222233334444"，
     NULL;
};
 execle("./mybin","mybin",NULL,envp_);
```
l：list：将参数一个一个的传入exec*
e：自定义环境变量。





---



真正的执行程序替换的系统调用接口就一个：

![在这里插入图片描述](https://img-blog.csdnimg.cn/601741cf25bc4519926db11040b271b9.png)


**程序替换中execve是系统调用，其他的都是封装，为了让我们有很多的选择性**。


---


#### 进程程序替换原理

进程在运行期间，地址空间被指向，它对应的代码区等等，进入页表，映射到物理内存，如下图，进程数据和进程代码，可执行程序最根上也由代码和数据两部分构成，在物理内存中，正常执行代码，访问数据，就一直被CPU调度，进程就一直在运行,当我们实际在调用exec 的时候，就是把可执行程序对应的数据和代码 、程序替换，替换的时候就是把我们的代码和数据load到物理内存对应的位置，所以当我们调用exec*系列的函数本质就是用我们对应的可执行程序的代码和数据来覆盖曾经的代码和数据，必要的时候可能要修改页表。所以程序替换的本质：**就是将指定程序的代码和数据加载到指定的位置！（覆盖自己的代码和数据）**



**进程替换的时候没有创建新的进程，它只是把当前进程的代码和数据做替换**。

![在这里插入图片描述](https://img-blog.csdnimg.cn/de5746f29b5f400e8c40d07c8aeb8bd2.png)
下面我们看这个问题：我们发现在执行的时候第一句打印了，最后一句为什么没有执行呢？
![在这里插入图片描述](https://img-blog.csdnimg.cn/ef1edb0863cf43eca5df867d7630eb7a.png)


**`printf`也是代码，实在`execl`之后的，`execl`执行完毕的时候，代码已经全部被覆盖了，开始执行新的程序代码了，所以`printf`就无法执行了**。

继续修改代码：

![在这里插入图片描述](https://img-blog.csdnimg.cn/88defca2ea584400bf3cb3f2ce5441f9.png)

修改成一个不可能存在的指令，运行：
![在这里插入图片描述](https://img-blog.csdnimg.cn/b01c14f4e9004e5286aaab0f3d4d2b3b.png)


发现当前运行到结束了，**只要是一个函数调用就有可能失败，就是没有替换成功，就是没有替换**。

![在这里插入图片描述](https://img-blog.csdnimg.cn/154634f8680141ff8ab95700c166a61e.png)
上面手册上说这个函数只会在出错的时候返回return -1。

exec*为什么没有成功返回值呢？
因为因为它调用成功的时候后续代码都不会被执行了，成功了就和接下来的代码无关了，判断返回值这样的逻辑就毫无意义，所以不需要返回值。

execl只要返回了，一定是错了。



看看下面的问题：
![在这里插入图片描述](https://img-blog.csdnimg.cn/4914b579d7914d559aaedf7d12372898.png)

解释：

![在这里插入图片描述](https://img-blog.csdnimg.cn/d433f11939c64010aebff7c00232ba75.png)
虚拟地址空间+页表保证进程独立性，一旦有执行流想替换代码或者数据，发生写时拷贝。

---

#### 通过myexec执行自己写的程序
我想通过myexec去执行我自己写的程序：

![在这里插入图片描述](https://img-blog.csdnimg.cn/1830077effb64496b6e115c5568c571b.png)


修改Makefile：
![在这里插入图片描述](https://img-blog.csdnimg.cn/8ec7b936ed3a423d8db89db9ecf1534f.png)

此时make就一次形成两个可执行。
![在这里插入图片描述](https://img-blog.csdnimg.cn/e6d730fe7a664d8b842d50847b43cb0c.png)
这里我们的目的是让myexec去调用mybin。
![在这里插入图片描述](https://img-blog.csdnimg.cn/7d8b470cef66425cb0ef774e30bf210d.png)

代码如下：

```cpp
  1 #include <stdio.h>
  2 #include <unistd.h>
  3 #include <stdlib.h>
  4 #include <assert.h>
  5 #include <sys/types.h>
  6 #include <sys/wait.h>
  7 
  8 
  9 int main()
 10 {
 11   printf("process is running...\n");
 12   pid_t id = fork();
 13   assert(id != -1);
 14   if(id == 0)
 15   {
 16     sleep(1);
 17     execl("./mybin","mybin",NULL);
 18     exit(1);
 19   }
 20   int status = 0;
 21   pid_t ret = waitpid(id,&status,0);
 22   if(ret > 0) 23   { 24     printf("wait success: exit code: %d,sig: %d\n",(status>>8)    &0xFF,status & 0x7F);
 25   }                                                           
 26   //printf("process is running...\n");
 27                                       
 28   //execl("/usr/bin/ls","ls",NULL);   
 29   //execl("/usr/bin/ls","ls",NULL);   
 30                                       
 31   // printf("process running done...\n");
 32                                          
 33   return 0;                              
 34 }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/0b3e1ba23a9a4c10b8cd9036f36341c4.png)



编译运行myexec，它就用myexec把mybin调用起来了。
![在这里插入图片描述](https://img-blog.csdnimg.cn/9eff79b9b10c4006b7be5a288a27720a.png)
现在我们想用C把C++语言调用起来，
![在这里插入图片描述](https://img-blog.csdnimg.cn/421ae8dec5174fae923e63f740715032.png)
创建：mycpp.cc
![在这里插入图片描述](https://img-blog.csdnimg.cn/0cff935cab2f4d49a89604d75bc6b747.png)

编译运行;
![在这里插入图片描述](https://img-blog.csdnimg.cn/ee65a5bc82104751a38236052d415189.png)
继续修改myexec.c：和上面的一样只需要改一行代码：
`execl("./mycpp","mycpp",NULL);  `

```cpp
  1 #include <stdio.h>
  2 #include <unistd.h>
  3 #include <stdlib.h>
  4 #include <assert.h>
  5 #include <sys/types.h>
  6 #include <sys/wait.h>
  7 
  8 
  9 int main()
 10 {
 11   printf("process is running...\n");
 12   pid_t id = fork();
 13   assert(id != -1);
 14   if(id == 0)
 15   {
 16     sleep(1);
 17     execl("./mycpp","mycpp",NULL);
 18     exit(1);
 19   }
 20   int status = 0;
 21   pid_t ret = waitpid(id,&status,0);
 22   if(ret > 0) 23   { 24     printf("wait success: exit code: %d,sig: %d\n",(status>>8)    &0xFF,status & 0x7F);
 25   }                                                           
 26   //printf("process is running...\n");
 27                                       
 28   //execl("/usr/bin/ls","ls",NULL);   
 29   //execl("/usr/bin/ls","ls",NULL);   
 30                                       
 31   // printf("process running done...\n");
 32                                          
 33   return 0;                              
 34 }
```


编译运行：此时就把C++代码运行起来了。
![在这里插入图片描述](https://img-blog.csdnimg.cn/264ea7d197d247748e04916f52b91e27.png)

 **结论**：**可以使用程序替换调用任何后端语言对应的可执行程序**。


--- 
