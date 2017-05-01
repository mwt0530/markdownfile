# 解压initramfs遇到的问题

### 问题

前面在centos7遇到驱动模块加载顺序的问题，原本打算通过修改initramfs中两个模块之间的依赖关系来解决，在对initramfs修改过程中出现了一个小问题。

查看initramfs属性
```
[root@qsr07n01 init]# file initramfs-3.10.0-514.el7.x86_64.img
initramfs-3.10.0-514.el7.x86_64.img: ASCII cpio archive (SVR4 with no CRC)
```
结果显示系统中initramfs是cpio文件，一般系统的initramfs应该为gz压缩文件。

解压：
```
[root@qsr07n01 microcode]# ll -h ../initramfs-3.10.0-514.el7.x86_64.img
-rw-------. 1 root root 18M 4月  26 04:35 ../initramfs-3.10.0-514.el7.x86_64.img
[root@qsr07n01 microcode]# cpio -imdv < ../initramfs-3.10.0-514.el7.x86_64.img
.
kernel
kernel/x86
kernel/x86/microcode
cpio: kernel/x86/microcode/GenuineIntel.bin not created: newer or same age version exists
kernel/x86/microcode/GenuineIntel.bin
cpio: early_cpio not created: newer or same age version exists
early_cpio
34 blocks
```
解压出的文件和普通initramfs也不同，文件大小18M，解压时只解压了34blocks，每个blocks默认大小是512字节，所以并没有完全解压。

### 解决

```
[root@qsr07n01 init]# dd if=initramfs-3.10.0-514.el7.x86_64.img of=actualfiles.img.gz skip=34 [bs=512]
```
得到真正的包含真实文件的gz文件。

### 重新合成

```
find | cpio -H newc -o > /opt/my_archive.cpio
gzip /opt/my_archive.cpio
```
将第一部分的CPIO文件和第二部分的gz文件合并
```
cat my_microcode_image.cpio /tmp/my_archive.cpio.gz > mynewinitrd.img
```

### 参考
https://unix.stackexchange.com/questions/163346/why-is-it-that-my-initrd-only-has-one-directory-namely-kernel
https://www.centos.org/forums/viewtopic.php?t=51621
