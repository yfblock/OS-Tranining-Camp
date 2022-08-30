# 实验

## 实验一报告
### 实现功能

1. 为`TaskControlBlock`添加`task_syscall_times`和`task_start_time`记录任务执行`syscall`调用次数和任务第一次被调度的`time`。

2. 为`TaskManager`添加`get_current_task_info`和`update_current_task_syscall`，用于获取`TaskInfo`和更新`syscall`次数

3. 完善`sys_task_info`系统调用，在系统调用中将`*mut TaskInfo`转为`&mut TaskInfo`并传递给`get_current_task_info`进行数据填充

4. 在`TaskManager`的`run_first_task`中为`task0`更新第一次被调用的时间，并且在`run_next_task`中判断任务`task_start_time`是否被更新过（任务是否被调用过），如果没被更新过则更新为`get_time()`获取到的时间

5. 在`timer`中添加函数`time_to_ms`将系统的`time`转换为`ms`

### 简答作业

#### 1.出错行为

```rust
[ERROR] [kernel] PageFault in application, core dumped.
[ERROR] [kernel] IllegalInstruction in application, core dumped.
[ERROR] [kernel] IllegalInstruction in application, core dumped.
```

出错行为分别为`PageFault`由访问没有权限的内存页触发，`IllegalInstruction`在U态中使用`sret`指令和操作`sstatus`指令触发

#### 2.trap.S

答1：在进入`__restore`时，`a0`中的值为`trap_handler`函数的返回值，即`&mut TrapContext`（TrapContext的地址）。

答2：

```riscv
ld t0, 32*8(sp)
ld t1, 33*8(sp)
ld t2, 2*8(sp)
csrw sstatus, t0
csrw sepc, t1
csrw sscratch, t2
```

上述代码特殊处理了`sstatus`、`sepc`、`sscratch`三个寄存器。分别保存了调用之前CPU所处的状态（U/S），发生调用的地址也是恢复后返回地址、作为暂存用户态程序栈的地址。

答3：`x2`也是`sp`寄存器，如果恢复`x2`后面对于`sp`的读取将出现异常。`x4`寄存器在保存时没有保存，即不使用也不读取。

答4：L63`csrrw sp, sscratch, sp`之后`sp`保存用户栈地址，`sscratch`保存内核栈地址。

答5：状态切换发生在`sret`，`ecall`指令从`U-Mode`进入`S-Mode`，`sret`返回`U-Mode`，

答6：L13`csrrw sp, sscratch, sp`之后`sp`保存内核栈地址，`sscratch`保存用户栈地址。

答7：`ecall`指令

## 实验二报告

### 完成内容

1. 完成map和unmap，并自动判重
2. 在第一张的基础上添加函数对来自用户空间的地址进行转化，方便内核进行内存读写
3. 对第三章完成内容进行小幅度修改

### 问答作业

1. 请列举 SV39 页表页表项的组成，描述其中的标志位有何作用？
   答：SV39页表项组成如下:

   | Reserved | Physical Page Number | RSW  |  D   |  A   |  G   |  U   |  X   |  W   |  R   |  V   |
   | :------: | :------------------: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
   |  63..54  |        53..10        | 9..8 |  7   |  6   |  5   |  4   |  3   |  2   |  1   |  0   |

   `Reserved`为保留位

   `Physical Page Number`也称为`PPN`为物理页号

   `RSW`为`supervisor`保留位

   `D` 为 `Dirty`, 用于判断是否被修改过

   `A` 为 `Accessed`，用于判断是否被访问过

   `G` 为 `Global`，全局映射

   `U`为`User`，控制是否为用户态可访问

   `X` 为 `Executable`，控制是否可执行

   `W` 为 `Writable`，控制是否可写

   `R` 为 `Readable`，控制是否可读

   `V` 为 `Valid`, 用于判断页表项是否有效

2. 缺页

   + 缺页指的是进程访问页面时页面不在页表中或在页表中无效的现象，此时 MMU 将会返回一个中断， 告知 os 进程内存访问出了问题。os 选择填补页表并重新执行异常指令或者杀死进程。
     - 请问哪些异常可能是缺页导致的？
       答：缺页可能导致 `PageFault` 异常，通常由访问不存在的页产生。
     - 发生缺页时，描述相关重要寄存器的值，上次实验描述过的可以简略。
       答：略
   + 缺页有两个常见的原因，其一是 Lazy 策略，也就是直到内存页面被访问才实际进行页表操作。 比如，一个程序被执行时，进程的代码段理论上需要从磁盘加载到内存。但是` os` 并不会马上这样做， 而是会保存 .text 段在磁盘的位置信息，在这些代码第一次被执行时才完成从磁盘的加载操作。
     + 这样做有哪些好处？
       答：这样可以节省内存空间，有些内存被用户申请后永远都不会访问，使用`Lazy`策略可以节省系统性能。
   + 缺页的另一个常见原因是 swap 策略，也就是内存页面可能被换到磁盘上了，导致对应页面失效。
     - 此时页面失效如何表现在页表项(PTE)上？
       答：页表项的`V`位为0

3. 双页表与单页表

   + 为了防范侧信道攻击，我们的 `os` 使用了双页表。但是传统的设计一直是单页表的，也就是说， 用户线程和对应的内核线程共用同一张页表，只不过内核对应的地址只允许在内核态访问。 (备注：这里的单/双的说法仅为自创的通俗说法，并无这个名词概念，详情见 [KPTI](https://en.wikipedia.org/wiki/Kernel_page-table_isolation) )
     - 在单页表情况下，如何更换页表？
       答：切换任务时进行更换。
     - 单页表情况下，如何控制用户态无法访问内核页面？
       答：对内核所拥有的页面 `U` 位置零。
     - 单页表有何优势？（回答合理即可）
       答：无需频繁的更换表，不必频繁刷新快表。
     - 双页表实现下，何时需要更换页表？假设你写一个单页表操作系统，你会选择何时更换页表（回答合理即可）
       答：双页表时每次特权级切换都对页表进行更换，单页表时仅在切换任务时更换页表。

## 实验三报告

### 完成内容

1. 在`task`中添加`spawn`功能，加载`elf`添加任务
  
2. 完善`sys_call`
  
3. 完善调度程序
  

### 问答作业

1. stride 算法原理非常简单，但是有一个比较大的问题。例如两个 stride = 10 的进程，使用 8bit 无符号整形储存 pass， p1.pass = 255, p2.pass = 250，在 p2 执行一个时间片后，理论上下一次应该 p1 执行。
  
  - 实际情况是轮到 p1 执行吗？为什么？
    答：不是 `p2`的 pass 移除变为 `4`，成为下一个选择
2. 我们之前要求进程优先级 >= 2 其实就是为了解决这个问题。可以证明， **在不考虑溢出的情况下** , 在进程优先级全部 >= 2 的情况下，如果严格按照算法执行，那么 PASS_MAX – PASS_MIN <= BigStride / 2。
  
  - 为什么？尝试简单说明（不要求严格证明）。
    答：在执行一段时间后 PASS_MAX - PASS_MIN < BigStride / 2 + n。0 < A <= stride。所以减去一个stride，所以PASS_MAX - PASS_MIN <= BigStride / 2.
    
  - 已知以上结论，**考虑溢出的情况下**，可以为 pass 设计特别的比较器，让 BinaryHeap<Pass> 的 pop 方法能返回真正最小的 Pass。补全下列代码中的 `partial_cmp` 函数，假设两个 Pass 永远不会相等。
    

```rust
use core::cmp::Ordering;

struct Pass(u64);

impl PartialOrd for Pass {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        // ...
    }
}

impl PartialEq for Pass {
    fn eq(&self, other: &Self) -> bool {
        false
    }
}
```

## 实验四报告

### 编程作业

1. 添加之前的系统调用
  
2. 修改`spawn`适配文件系统
  
3. 针对文件系统进行实现相应的系统调用
  

### 问答作业

1. 在我们的easy-fs中，root inode起着什么作用？如果root inode中的内容损坏了，会发生什么？
  答：`ROOT_INODE`是根节点，方便对其他节点的索引，如果`ROOT_INODE`的内容损坏，无法正常访问其他文件。
  
2. 举出使用 pipe 的一个实际应用的例子。
  答：`Pipe`用于多线程通信。
  
3. 如果需要在多个进程间互相通信，则需要为每一对进程建立一个管道，非常繁琐，请设计一个更易用的多进程通信机制。
  答：可否创建一个全局的`FD`存于内核中，在需要时由进程进行申请，对`FD`进行特征编号，标识由哪个进程创建，方便进行筛选。同样在写入数据时统一写入，内核对每一个进程添加一个`offset`，表示当前进程的读取位置，防止一个进程读取后其他进程无法读取，也可以添加相应的标志位，选择性将所有内容进行非重复性读取。


## 实验五报告

### 编程作业

1. 添加死锁相关数据结构，存取申请内存需要的内存
  
2. 对所有线程的资源进行计算判断是否产生死锁
  
3. 完善死锁相关系统调用
  

### 问答作业

1. 在我们的多线程实现中，当主线程 (即 0 号线程) 退出时，视为整个进程退出， 此时需要结束该进程管理的所有线程并回收其资源。 - 需要回收的资源有哪些？ - 其他线程的 TaskControlBlock 可能在哪些位置被引用，分别是否需要回收，为什么？
  答：需要回收的资源有`TaskUserRess`，回收内存，回收`fd_table`，回收`子进程`相关的资源。`TaskControlBlock`可能在锁中被引用，但是不用回收，因为资源已经回收。
  
2. 对比以下两种 `Mutex.unlock` 的实现，二者有什么区别？这些区别可能会导致什么问题？
  答：第一种先解锁，然后弹出等待锁的任务，第二种判断是否由等待的任务，如果有则弹出，否则解锁。会导致死锁。
  

```rust
impl Mutex for Mutex1 {
    fn unlock(&self) {
        let mut mutex_inner = self.inner.exclusive_access();
        assert!(mutex_inner.locked);
        mutex_inner.locked = false;
        if let Some(waking_task) = mutex_inner.wait_queue.pop_front() {
            add_task(waking_task);
        }
    }
}

impl Mutex for Mutex2 {
    fn unlock(&self) {
        let mut mutex_inner = self.inner.exclusive_access();
        assert!(mutex_inner.locked);
        if let Some(waking_task) = mutex_inner.wait_queue.pop_front() {
            add_task(waking_task);
        } else {
            mutex_inner.locked = false;
        }
    }
}
```