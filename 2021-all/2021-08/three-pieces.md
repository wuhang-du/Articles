# Operating Systems: Three Easy Pieces 

TODO：以单机维度整理每一篇内容所处的位置；

#### 虚拟化；并发；持久化；

##### 4
process：a running program;
程序只是一堆硬盘上的指令；跑起来才有意义；

time-sharing: 必须依靠 context-switching;
调度策略；

时分对应空分；空分则是类似硬盘这种；

###### 4.1 抽象
程序的组成部分：
1.存储；或者是可寻址空间；
指令在这里；数据也在这里；堆或者栈；

2.寄存器；
特别需要关注的：
the program counter (PC) (sometimes
called the instruction pointer or IP)
a stack pointer
a frame pointer： ？

3.io information： fd表 ？

###### 4.2 API
一个操作系统应该提供哪些接口？
create()
distory()
wait() 这个接口没太懂？有点像是一个事件通知？
control()

status()

###### 4.3 create

Programs initially reside on disk (or, in some
modern systems, flash-based SSDs) in some kind of executable format
从硬盘加载进来，准备好堆、栈，打开文字描述符，开始cpu的表演吧；

###### 4.4 process state
状态机：
running-ready-blocked

Time Process0 Process1 Notes
1 Running Ready
2 Running Ready
3 Running Ready Process0 initiates I/O
4 Blocked Running Process0 is blocked,
5 Blocked Running so Process1 runs
6 Blocked Running
7 Ready Running I/O done
8 Ready Running Process1 now done
9 Running –
10 Running – Process0 now done


###### 4.5 data struct


// the information xv6 tracks about each process
// including its register context and state
struct proc {
char *mem; // Start of process memory
uint sz; // Size of process memory
char *kstack; // Bottom of kernel stack
// for this process
enum proc_state state; // Process state
int pid; // Process ID
struct proc *parent; // Parent process
void *chan; // If !zero, sleeping on chan
int killed; // If !zero, has been killed
struct file *ofile[NOFILE]; // Open files
struct inode *cwd; // Current directory
struct context context; // Switch here to run process
struct trapframe *tf; // Trap frame for the
// current interrupt
};
pcb
内核栈与程序栈？

###### 4.6 summary
the low-level
mechanisms needed to implement processes, and the higher-level policies required to schedule them in an intelligent way.


##### 5. API

###### 5.1 Fork()
横空出世，创造一个process；

###### 5.2 Wait()
确实是信号传输

###### 5.3 Exec()
启动了一个程序

##### 6. 执行一个程序？

虚拟化cpu的一种方式：time-sharing
操作系统的目标是什么：高性能与控制都要；

##### 17

###### 17.3
管理freeMemory的原则：
目的是：提高利用率是吧
原则1：避免碎片化；办法：整合；
原则2：避免一次申请过多，但是不使用；办法：sbrk
原则3：申请速度要快；办法：链表这个数据结构；

申请速度快而且碎片少的分配策略；

这里是说的分配策略：
策略1：best-Fit：所有满足的里面找最小的
策略2：first-Fit: 它容易对碎片敏感，因此：对于链表的管理顺序比较敏感；
两个联合起来；

增加：worst-fit: 所有满足的里面找最大的；
增加：next-fit: 记录上次找的最后一个位置； 


