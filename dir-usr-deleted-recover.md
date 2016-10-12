###centos7误删usr目录

centos7 usr目录被删除，所有命令和库文件都将不起作用，在不使用恢复磁盘工具时可使用以下方法。

1. 新建虚拟机（语言和版本一致），安装某些常用应用如git、vnc等，也可以在后面安装。
2. 将虚拟机usr目录打包拷贝原来机器，中间可使用U盘制作try ubuntu作为工具。
3. 解压到根目录。
此时恢复基本系统功能，但是系统其他包在usr的文件还是缺失，可以参考下面方法。

```
# rpm -qaV >all
# grep missing all | awk '{print $NF}' > missfile
# for i in `cat missfile`;do rpm -qf $i >> missrpm ;done
# cat missrpm | uniq > reinstallrpms
# for i in `cat reinstallrpms`;do yum --disablerepo=* --enablerepo=dvd reinstall $i;done
# 剩下解决其他源的缺失
```
>缺失的文件所在的rpm包的版本和源里的版本一致才可以`reinstall`


---------------------

预备知识：
rpm包的各种信息，包括包名，安装日期、文件的md5校验信息等，都是存放在/var/lib/rpm的各个文件当中的，
所以只要这个目录不被删掉，我们可以从中读出我们需要的信息进行恢复被误删的文件。

步骤：
1）用安装光盘进入rescue模式，校验所有系统中的安装包，找出那些文件丢失：
```
rpm -qaV --root /mnt/sysimage >/tmp/rpm_qaV.log
```

2) 找到所有校验结果为missing的文件：
```
grep missing /tmp/rpm_qaV.log|awk '{print $NF}' >/tmp/missing_file
```

3，查询每一个被删除的文件是属于那个rpm包：

```
for line in `cat /tmp/missing_file`
do
	rpm -qf $line --root /mnt/sysimage >>/tmp/missing_rpm
done
```

4) 步骤3中生成的missing_rpm文件有很多是重复的，需要处理一下：
```
sort /tmp/missing_rpm |sort -u >/tmp/rpm_reinstall
cp /tmp/rpm_reinstall /mnt/sysimage/tmp
```

到此为止，我们在救援模式下得到了系统所有被删除的文件所在的rpm包，
下一步的工作就是把这些rpm包重新安装，被删除丢失的文件也就找回来了。

5) 启动到单用户模式，挂载光盘，从安装光盘里拷贝拷贝需要的rpm包到硬盘准备安装：
```
mkdir /rpms
mount /dev/cdrom /mnt
cd /mnt/Server
for line in `cat /tmp/rpm_reinstall`
	cp $line* /rpms
done
```

6，重新用安装光盘启动到救援模式，安装rpm包：
```
rpm -ivh /mnt/sysimage/rpms/* --root /mnt/sysimage --nodeps --force
```


####参考
http://bbs.chinaunix.net/thread-1997935-1-1.html
http://bbs.chinaunix.net/thread-1998787-1-1.html
https://my.oschina.net/liuhuan0927/blog/656622?p=1