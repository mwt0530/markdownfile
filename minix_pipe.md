#minix pipe
----
####sys_pipe
实际申请的是两个当前进程task->filp[NR_OPEN]，用int fd[2]文件描述符表示。实际读写中用到的是filp指针指向的inode节点，例如task->filp[fd[0]]，指向申请到的file结构，最后寻找到管道用到的inode节点。
* **流程**
从全局的file_table中申请两个file结构，并用两个指针指向，从当前进程的filp结构中寻找两个空闲变量指向申请到的file结构，此时读写操作用到的文件描述符就是filp空闲数组的序号，current->filp[fd[0]=i]。
申请一个pipe类型的inode节点，其中大小为1页，4096B，并设置current->filp[fd[0|1]]指向此节点，并更改,
```
current->filp[fd[0]]->f_mode=1(读)
current->filp[fd[1]]->f_mode=2(写)
```

>管道申请结束

---
####write_pipe
最大管道缓冲4K，设置PIPE_HEAD，PIPE_TAIL，根据剩余空闲缓冲进行循环写。

####read_pipe
与write呼应。
