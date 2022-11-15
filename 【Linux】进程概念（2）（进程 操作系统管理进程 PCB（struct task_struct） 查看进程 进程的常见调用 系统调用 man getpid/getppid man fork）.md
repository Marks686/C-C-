# 进程
## 概念
* **课本概念：程序的一个执行实例，正在执行的程序等。**
* **内核观点：担当分配系统资源（CPU时间，内存）的实体。**


一个运行起来（加载到内存）的程序 --- 进程
在内存中的程序 --- 进程 
进程和程序相比，进程具有动态属性


#  操作系统管理进程


> 操作系统如何管理进程？


先描述再组织，要管理这些进程，操作系统首先将这些进程描述，所以就引出了一个叫做PCB的概念，

## PCB（struct task_struct）结构体
![在这里插入图片描述](https://img-blog.csdnimg.cn/4ad4260e7ff942099b9f04972fb1cd99.png)
操作系统为了管理进程，给PCB里面写上包含了进程的所有属性，理所当然，这里的属性有几百个，我们通过每个进程对应的ID来进行管理。进程控制块里面保存的是进程被创建时的所有属性，这些属性不在我们曾经的可执行程序里面，可执行程序里面只有我们的代码数据和逻辑。

```cpp
struct task_struct
{
    //进程的所有属性
    //该进程对应的代码和属性地址
    struct task_struct *next;
 }
```

`struct  tack_struct *next`包含了大堆的指针信息，对我们来讲，描述每一个进程都是一个`task_struct`。

> **task_struct内容分类:**

* 标示符: 描述本进程的唯一标示符，用来区别其他进程。
* 状态: 任务状态，退出代码，退出信号等。
* 优先级: 相对于其他进程的优先级。
* 程序计数器: 程序中即将被执行的下一条指令的地址。
* 内存指针: 包括程序代码和进程相关数据的指针，还有和其他进程共享的内存块的指针
* 上下文数据: 进程执行时处理器的寄存器中的数据[休学例子，要加图CPU，寄存器]。
* I／O状态信息: 包括显示的I/O请求,分配给进程的I／O设备和被进程使用的文件列表。
* 记账信息: 可能包括处理器时间总和，使用的时钟数总和，时间限制，记账号等。
* 其他信息




每一个程序加载进内存，每一个进程对应的代码和数据在加载的时候，操作系统要进行操作，然后就初始化完了一个`task_struct`结构体，

![在这里插入图片描述](https://img-blog.csdnimg.cn/ead90c4f88b84d93925185680f428d9b.png)


![](https://img-blog.csdnimg.cn/5f61acd3ee8241679032e8dea69c5aa3.png)


其中每一个进程都要做这个工作，所以在系统里面，除了加载进程到内存里，操作系统给每一个可执行程序匹配一个进程控制块，说白了就是`task_struct`所对应的结构体对象或者变，然后它们每一个都指向自己的代码，到这里就完成了先描述，然后再组织，操作系统把所有的进程的PCB，结构体对象链接起来，拿着`process_header`这个头，CPU要调度某个进程，操作系统就遍历所有进程的PCB，然后找到优先级最高的进程，比如说`process_header`指向的那个，然后把代码交给CPU，CPU就可以执行对应进程的代码了，若以后有一个进程想退出了，我们只需要确定PCB属性里面有没有一个进程的状态是死亡的，死亡的话操作系统遍历链表，把状态是死亡的节点找到，然后把对应的代码和数据释放掉，然后把PCB释放掉，此时这个进程就会释放了。
![在这里插入图片描述](https://img-blog.csdnimg.cn/bf3c86b7119c4e0eaa4ff381c07b05f5.png)


最后我们有了先描述再组织这样的理念，我们得出一个结论：**所谓的对进程管理，变成了对进程对应的PCB进行相关的管理。对进程管理本质不是对进程的可执行程序管理，而是对应的PCB结构做管理。
**对进程管理——转化成立对链表的增删查~~改~~ ！**


`struct task_struct`是一个内核结构体，是操作系统给我们提供的用来描述进程的结构体，当加载进程的时候会创建对应的内核对象`task_struct 对象`（内核变量），并且将结构和我们的代码和数据关联起来从而完成先描述再组织的工作。
* **进程 = 内核数据结构（task_struct）+ 进程对应的磁盘代码**

---


# 查看进程

```cpp
-rw-rw-r-- 1 test_106 test_106   76 Nov 11 17:09 Makefile
-rwxrwxr-x 1 test_106 test_106 8408 Nov 11 17:16 myproc
-rw-rw-r-- 1 test_106 test_106  135 Nov 11 17:16 myproc.c
```

进程在运行，本质是进程读取代码，依次做执行，进程在被调度运行的时候，进程就具有动态属性。

`[test_106@VM-4-13-centos 20221111]$    man getpid`
`[test_106@VM-4-13-centos 20221111]$ ps ajx | head -1 && ps axj | grep 'myproc'`


## 另一种查看进程的方式：
一个进程每一次被启动，就意味着每一次被重新加载到内存，每一次被重新加载到内存就意味这操作系统每一次都要给这个进程创建`task_struct`，重新分配PID，所以PID不断变化。
在Linux当中有一个目录结构：`proc`,它是一个内存级的目录
`[test_106@VM-4-13-centos 20221111]$ ls /proc/`
![在这里插入图片描述](https://img-blog.csdnimg.cn/6199ca856f5d42dea0f407d5614c6e32.png)

我们可以看到有一些目录是以数字开头，其实那些数字就是对应进程的PID
![在这里插入图片描述](https://img-blog.csdnimg.cn/37007df4836548eb9d9053cca9c9c03a.png)
在Linux下，一个进程可以被当做文件来看待，最后我们可以通过`proc`这个目录来查到对应的属性信息。
`[test_106@VM-4-13-centos 20221111]$ ls /proc/5660 -d
`
![在这里插入图片描述](https://img-blog.csdnimg.cn/facc2c6c5ea4460883f3924d034e317e.png)
进程一退出，目录就自动删除了。

一个进程被加载到内存，这个进程已经跑起来了，现在把它在磁盘中的二进制程序删了，请问进程还能不能正常运行？
结果是还能。
![在这里插入图片描述](https://img-blog.csdnimg.cn/ce88433312d74759bc3f088552189563.png)


# 进程的常见调用
## 系统调用
### man getpid
![在这里插入图片描述](https://img-blog.csdnimg.cn/887a4d8b592040449c71fb6f0bdf87b1.png)

返回值`pid_t`是系统给我们封装的类型，其实就是整数。提供的接口，谁调用就返回调它进程的PID。


```cpp
  1 #include<stdio.h>
  2 #include<unistd.h>
  3 #include<sys/types.h>                                                                                                                             
  4 int main()
  5 {
  6   while(1)
  7   {
  8     printf("我是一个进程!,我的ID是：%d\n",getpid());
  9     sleep(1);
 10   }
 11   return 0;
 12 }

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/ee923b81d1e847dcb16ce3008cd8a930.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/6d6b906dfdf944989d49ac8cf7e49dd6.png)
杀掉进程：`kell -9 1193`



### man getppid

第一个`p`表示父，`main getppid`表示获取父进程ID，

```cpp
  1 #include<stdio.h>
  2 #include<unistd.h>
  3 #include<sys/types.h>
  4 int main()
  5 {
  6   while(1)
  7   {
  8     printf("我是一个进程!,我的ID是：%d,父进程的pid：%d\n",getpid(),getppid());                                                 
  9     sleep(1);                                                                                      
 10   }                                                                                                
 11   return 0;                                                                                        
 12 }  
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/c261d1d254cb463282794c77440b2eac.png)
关掉进程，再次打开，我们发现子进程一直在变，父进程`pid`不变化,我们查看：
`[test_106@VM-4-13-centos 20221111]$ ps ajx | grep 7651`


![在这里插入图片描述](https://img-blog.csdnimg.cn/95ac0f86d62a4c5c9328925a0c098796.png)

我们发现`7651`进程的PID叫做`bash`。
**命令行上启动的进程，一般它的父进程没有特殊情况的话，都是bash！**

### man fork
* 运行 man fork 认识fork
* fork有两个返回值
* 父子进程代码共享，数据各自开辟空间，私有一份（采用写时拷贝）
![在这里插入图片描述](https://img-blog.csdnimg.cn/910d59c21ba34180b84ac69e4ba50cc4.png)
创建子进程：`fork - create a child process` `fork` 是一个函数.
函数执行前：只有一个父进程，函数执行后：父进程+子进程

```cpp
  1 #include<stdio.h>
  2 #include<unistd.h>
  3 #include<sys/types.h>
  4 int main()
  5 {
  6   fork();
  7   printf("我是一个进程，pid:%d,ppid:%d\n",getpid(),gitppid());
  8   sleep(2);                                                                                                                     
 14   return 0;
 15 }  
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/85ed56f935384da98763e193dadcd3bd.png)

```cpp
  1 #include<stdio.h>
  2 #include<unistd.h>
  3 #include<sys/types.h>
  4 int main()
  5 {
  6   pid_t id = fork();
  7   printf("我是一个进程，pid: %d ,ppid:%d,id: %d\n",getpid(),getppid(),id);                                                     
  8   sleep(2);                                                                                                                     
 14   return 0;                                                          
 15 } 
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/d0d8eb18b58140fe88d9772c8c38f23c.png)
同一个变量id，在后续不会被修改的情况下，竟然有不同的内容！

```cpp
  1 #include<stdio.h>
  2 #include<unistd.h>
  3 #include<sys/types.h>
  4 int main()
  5 {
  6   pid_t id = fork();
  7   if(id == 0)
  8   {
  9     //子进程
 10     while(1)
 11     {
 13      printf("子进程，pid: %d ,ppid:%d,id: %d\n",getpid(),getppid(),id);
 14       sleep(1);
 15     }
 16   }
 17   else if(id > 0)
 18   {
 19     //parent
 20     while(1)
 21     {
 22 
 23       printf("父进程，pid: %d ,ppid:%d,id: %d\n",getpid(),getppid(),id);
 24          sleep(1);
 25     }
 26   }
 27   else{
 29   }                                                                                                                            
 37   return 0;
 38 }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/d1a61d0ff21e4e6f80bc191e22900be6.png)
创建出来了多进程，在C语言中if和else if不能同时条件成立，以前写的代码，两个死循环不能同时跑，证明`fork`之后会有父进程+子进程两个进程在执行后续代码，`fork`后续的代码，被父子进程共享。通过返回值不同，让父子进程执行后续共享的代码的一部分！
