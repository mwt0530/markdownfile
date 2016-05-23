###LVM

```
# lvscan
# vgscan
```
扩展分区

```
# lvextend -L +10G /dev/centos/root
```
此时fdisk查看分区大小已修改，但是df查看文件系统大小没有修改，
需要将文件系统扩展到新的空间中。

```
# xfs_growfs /dev/centos/root    //xfs文件系统
或者
# resize2fs -f /dev/centos/root  //ext文件系统
```
