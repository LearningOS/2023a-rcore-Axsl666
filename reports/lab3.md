# ch5 实验报告

## 编程作业

### 任务

- 实现系统调用 spawn，用以创建一个新进程。
- 实现一种带优先级的调度算法：stride 调度算法

### 实验过程

#### 实现系统调用 spawn

- 扩展 Task 公共接口
  - spawn
    - MemorySet::from_elf 新建地址空间，用户栈，程序入口点
    - 分配 pid 与内核栈
    - 建立 TCB
    - 加入父进程的子进程链表
    - 准备 TrapContext
    - 返回新子进程的TCB
- 完成系统调用功能
  - sys_spawn

#### 实现 stride 调度算法

- 扩展 TCB
  - stride
  - priority
- 扩展 Task 公共接口
  - set_priority
- 修改 TaskManager 调度算法
  - fetch 修改为 stride 调度算法
- 完成系统调用功能
  - sys_set_priority 调用 set_priority

## 问答题

- 不是轮到p1执行
  - 8bit 无符号整数的最大值为 255
  - 实际上 p2 的 250 + 10 溢出后 p2.stride = 4，p2先执行

- 证明 STRIDE_MAX – STRIDE_MIN <= BigStride / 2：

首先证明：STRIDE_MAX – STRIDE_MIN <= PASS_MAX  
反证法：若 STRIDE_MAX – PASS_MAX > STRIDE_MIN  
则可以知道达到 STRIDE_MAX 为进程 P，即 P.stride = STRIDE_MAX，则上一次 P 被选择时 P.laststride = P.stride - pass  
又因为 pass <= PASS_MAX  
所以 P.laststride = P.stride - pass >= STRIDE_MAX – PASS_MAX > STRIDE_MIN  
而 P.laststride > STRIDE_MIN 显然不可能成立，因为只能选择stride最小的进程进行调度。  
故 STRIDE_MAX – PASS_MAX > STRIDE_MIN 不成立

则有 LAST_STRIDE_MIN + PASS_MAX <= STRIDE_MAX  
易知 STRIDE_MIN <= LAST_STRIDE_MIN  
可推出 STRIDE_MIN + PASS_MAX <= STRIDE_MAX
即 STRIDE_MAX – STRIDE_MIN <= PASS_MAX  
又因为 prio >= 2 和 pass = BigStride / priority  
所以 pass <= BigStride / 2  
于是 PASS_MAX = BigStride / 2  
所以 STRIDE_MAX – STRIDE_MIN <= PASS_MAX = BigStride / 2  
得证

```rust
use core::cmp::Ordering;
struct Stride(u64);

const BIG_STRIDE:u8 = 255;
const PASSMAX:u8 = BIG_STRIDE/2;
/// 一旦
impl PartialOrd for Stride {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        // BigStride = 255, PASSMAX = BigStride/2 = 127
        // let BigStride = 255u8;
        let u1 = self.0 as u8;
        let u2 = other.0 as u8;
        if u1 > u2 {
            let diff = u1 - u2;
            if diff > PASSMAX {
                Some(Ordering::Less)
            }else {
                Some(Ordering::Greater)
            }
        } else {
            let diff = u2 - u1;
            if diff > PASSMAX {
                Some(Ordering::Greater)
            }else {
                Some(Ordering::Less)
            }
        }

    }
}

impl PartialEq for Stride {
    fn eq(&self, other: &Self) -> bool {
        false
    }
}
```

## 荣誉准则

1. 在完成本次实验的过程（含此前学习的过程）中，我曾分别与以下各位就（与本次实验相关的）以下方面做过交流，还在代码中对应的位置以注释形式记录了具体的交流对象及内容：

2. 此外，我也参考了以下资料 ，还在代码中对应的位置以注释形式记录了具体的参考来源及内容：

3. 我独立完成了本次实验除以上方面之外的所有工作，包括代码与文档。 我清楚地知道，从以上方面获得的信息在一定程度上降低了实验难度，可能会影响起评分。

4. 我从未使用过他人的代码，不管是原封不动地复制，还是经过了某些等价转换。 我未曾也不会向他人（含此后各届同学）复制或公开我的实验代码，我有义务妥善保管好它们。 我提交至本实验的评测系统的代码，均无意于破坏或妨碍任何计算机系统的正常运转。 我清楚地知道，以上情况均为本课程纪律所禁止，若违反，对应的实验成绩将按“-100”分计。