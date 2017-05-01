# dracut调整模块加载顺序

---

- 配置
  两台centos7主机通过HBA卡连接JBOD存储，60块硬盘，主机是两块600G硬盘，使用LSI RAID卡做RAID1。

- 问题
  通过IPMI virtual storage挂载ISO安装centos7.3，偶尔会出现系统重启后系统盘符不是sda的情况。

- 解决
  通过测试，可得系统盘符的分配和驱动加载顺序相关，若先加载HBA卡驱动mpt3sas，则系统盘的盘符不为sda，若先加载RAID卡驱动megaraid_sas，则系统盘符固定为sda。所以可以通过固定两块卡驱动加载顺序解决。具体方法：
  1. 修改启动参数，具体可查看`man dracut`和`man dracut.cmdline`,修改/boot/grub2/grub.cfg，在linux16行末添加`rd.driver.pre=megaraid_sas`，下次重启就会优先加载RAID驱动。
> 测试可行
  2. /etc/modprobe.d/下创建raid-first.conf，填写`install pata_acpi /sbin/modprobe megaraid_sas; /sbin/modprobe --ignore-install mpt3sas`，重新生成initramfs。`$ dracut XXX.img`

- 参考链接
https://bugs.mageia.org/show_bug.cgi?id=3395
http://www.udpwork.com/item/10047.html
https://www.google.com/patents/CN104461402A?cl=zh
