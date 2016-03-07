##buffer.c

>buffer.c 高速缓冲区操作
bitmap.c 文件系统的逻辑块位图和i节点位
truncate.c 把文件数据长度截断为0
inode.c 文件系统i节点信息的访问管理
namei.c 完成从一个从给定文件路径名寻找并加载对应i节点信息的功能
>super.c 文件系统的超级块访问和管理

缓冲块哈希表函数
```
#define NR_HASH 307
#define _hashfn(dev,block) (((unsigned)(dev^block))%NR_HASH)
#define hash(dev,block) hash_table[_hashfn(dev,block)]
```
高速缓冲获取空闲缓冲块主要实现getblk()

sync_inodes()作用是把i节点表的inode_table中的i节点信息和磁盘同步，但只会走到高速缓冲区这一步，剩下的由缓冲区管理摸块完成。
所有操作都会经过高速缓冲区再同步到磁盘。
1、数据结构与高速缓冲区的同步到高速缓冲区，由驱动程序负责。
2、高速缓冲区中的数据块与磁盘对应块同步，由缓冲区管理程序负责。


##bitmap.c
`int new_block(int dev)`
根据设备号分配新的逻辑块
流程：获取超级块，通过超级块获取bit为0的逻辑块，置位1，再高速缓存中保存，高速缓冲区中的b_uptodate和b_dirt都为1。
