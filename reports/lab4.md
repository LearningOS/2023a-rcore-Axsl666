# ch6 实验报告

## 编程作业

### 任务

- 实现三个系统调用 sys_linkat、sys_unlinkat、sys_stat

### 实验过程

#### sys_linkat

- 给 File trait 增加 fstat 接口
- 扩展 efs 中 DiskInode 接口
  - get_inode_id： get_disk_inode_pos的逆过程
- 在 vfs 中 Inode 增加接口
  - create_link
    - 新建目录项，(new_name, old_inode_id)
- 完成系统调用 sys_linkat

#### sys_unlinkat

- 给 File trait 增加 fstat 接口
- 扩展 efs 中 DiskInode 接口
  - get_inode_id： get_disk_inode_pos的逆过程
- 在 vfs 中 Inode 增加接口
  - delete_link
    - 遍历目录下所有目录项，找到与对应文件名相同的inode
    - 删除（清空）对应目录项
- 完成系统调用 sys_unlinkat

#### sys_stat

- 给 File trait 增加 fstat 接口
- 扩展 efs 中 DiskInode 接口
  - get_inode_id： get_disk_inode_pos的逆过程
- 在 vfs 中 Inode 增加接口
  - inode_id: 实现获取自身 inode id
  - isdir：判读是 inode 否为目录
  - linknum: 获取 root inode 下的某一个 inode id 对应的硬链接数量
- 完成系统调用 sys_stat

## 问答题

在我们的easy-fs中，root inode起着什么作用？如果root inode中的内容损坏了，会发生什么？

- 根目录 inode
- 由于我们的文件系统设计很简单，没有冗余和备份，根目录损坏会导致整个文件系统无法正常访问。

## 荣誉准则

1. 在完成本次实验的过程（含此前学习的过程）中，我曾分别与以下各位就（与本次实验相关的）以下方面做过交流，还在代码中对应的位置以注释形式记录了具体的交流对象及内容：

2. 此外，我也参考了以下资料 ，还在代码中对应的位置以注释形式记录了具体的参考来源及内容：

3. 我独立完成了本次实验除以上方面之外的所有工作，包括代码与文档。 我清楚地知道，从以上方面获得的信息在一定程度上降低了实验难度，可能会影响起评分。

4. 我从未使用过他人的代码，不管是原封不动地复制，还是经过了某些等价转换。 我未曾也不会向他人（含此后各届同学）复制或公开我的实验代码，我有义务妥善保管好它们。 我提交至本实验的评测系统的代码，均无意于破坏或妨碍任何计算机系统的正常运转。 我清楚地知道，以上情况均为本课程纪律所禁止，若违反，对应的实验成绩将按“-100”分计。