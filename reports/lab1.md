# ch3 实验报告

## 编程作业

### 任务

ch3 中，我们的系统已经能够支持多个任务分时轮流运行，我们希望引入一个新的系统调用 `sys_task_info` 以获取当前任务的信息。

### 实验过程

- 扩展 `TaskControlBlock`：
  - `start_time`
  - `started`
  - `syscall_times`
- 设计 `TaskManager` 的公共接口
  - `add_syscall_times`
  - `get_current_task_info`
- 在相应函数中更新 TCB 数据, 记录开始时间：
  - `run_first_task`
  - `run_next_task`
- 在系统调用处理函数中调用 `add_syscall_times` 更新 TCB 中的记录
- 完成 `sys_task_info` 系统调用
  - 主要是调用 `get_current_task_info` 并返回 `TaskInfo`。

## 问答题

### 1

```bash
[rustsbi] RustSBI version 0.3.0-alpha.2, adapting to RISC-V SBI v1.0.0
.______       __    __      _______.___________.  _______..______   __
|   _  \     |  |  |  |    /       |           | /       ||   _  \ |  |
|  |_)  |    |  |  |  |   |   (----`---|  |----`|   (----`|  |_)  ||  |
|      /     |  |  |  |    \   \       |  |      \   \    |   _  < |  |
|  |\  \----.|  `--'  |.----)   |      |  |  .----)   |   |  |_)  ||  |
| _| `._____| \______/ |_______/       |__|  |_______/    |______/ |__|
[rustsbi] Implementation     : RustSBI-QEMU Version 0.2.0-alpha.2


[kernel] PageFault in application, bad addr = 0x0, bad instruction = 0x804003c4, kernel killed it.
[kernel] IllegalInstruction in application, kernel killed it.
[kernel] IllegalInstruction in application, kernel killed it.
```

- 非法地址访问: `0x0`
- 非法指令: U mode 下的非法指令 `sret`
- 非法指令: U mode 下不允许访问 `sstatus` 寄存器

### 2

1. `a0` 指向当前任务的 Trap Context。`__restore` 的两种使用情景
   1. 用于特权级切换时恢复上下文。
   2. 执行第一个任务时，用于切换到到用户态并转跳到第一个任务。
2. `sstatus`， `sepc`， `sscratch` 寄存器, 更改`sstatus`意味着恢复CPU状态，`sepc` 保存了中断前要执行的指令，`sscratch`包存了用户栈指针
3. `x2` 为sp寄存器，其值已经保存到 `sscratch`中 ，`x4` 为 tp(Thread Pointer)寄存器，目前的系统还没有实现线程，无需使用和保存
4. `sp` 恢复为了用户栈， `sscratch` 则保存了内核栈
5. `sret`指令。 该指令执行时，会修改 `sstatus` 中的 `SPP` 恢复为之前的特权级，同时将 `pc` 设置为 `sepc` 中的值以恢复到用户程序执行
6. `sp` 恢复内核栈，`sscratch` 保存用户栈
7. `ecall` 指令。

## 荣誉准则

1. 在完成本次实验的过程（含此前学习的过程）中，我曾分别与以下各位就（与本次实验相关的）以下方面做过交流，还在代码中对应的位置以注释形式记录了具体的交流对象及内容：

2. 此外，我也参考了以下资料 ，还在代码中对应的位置以注释形式记录了具体的参考来源及内容：

3. 我独立完成了本次实验除以上方面之外的所有工作，包括代码与文档。 我清楚地知道，从以上方面获得的信息在一定程度上降低了实验难度，可能会影响起评分。

4. 我从未使用过他人的代码，不管是原封不动地复制，还是经过了某些等价转换。 我未曾也不会向他人（含此后各届同学）复制或公开我的实验代码，我有义务妥善保管好它们。 我提交至本实验的评测系统的代码，均无意于破坏或妨碍任何计算机系统的正常运转。 我清楚地知道，以上情况均为本课程纪律所禁止，若违反，对应的实验成绩将按“-100”分计。
