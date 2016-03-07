#rpm autoreq

##问题

安装rpm包时，系统缺少了某个库文件，例如libudev.so.0,但是系统中存在libudev.so.1。
解决:
	1.spec文件中增加  `Autoreq:    0`  或
	2.增加`Provides libudev.so.0()(64bit)`

###automatic dependencies
rpm使用ldd每个%file处的可执行程序来决定rpm包需要的共享库，具体使用的脚本是find-requires。
```locate
[root@localhost rpm]# locate find-requires | grep /find-requires$
/usr/lib/rpm/find-requires
/usr/lib/rpm/redhat/find-requires
```
使用此脚本可以查看某些程序需要的包
```
[root@localhost rpm]# ./find-requires 
/bin/ls
libacl.so.1()(64bit)
libcap.so.2()(64bit)
libc.so.6()(64bit)
libc.so.6(GLIBC_2.14)(64bit)
libc.so.6(GLIBC_2.17)(64bit)
libc.so.6(GLIBC_2.2.5)(64bit)
libc.so.6(GLIBC_2.3.4)(64bit)
libc.so.6(GLIBC_2.3)(64bit)
libc.so.6(GLIBC_2.4)(64bit)
libselinux.so.1()(64bit)
```

`[root@localhost rpm]# rpm -qf /bin/ls | xargs rpm -q --provides`

对应的还有provides功能，参考[wiki](http://www.rpm.org/max-rpm-snapshot/s1-rpm-depend-auto-depend.html)。

###参考
官网wiki  [Maximum RPM](http://www.rpm.org/max-rpm-snapshot/s1-rpm-depend-auto-depend.html)

==以下截取WIKI==
####The autoreqprov, autoreq, and autoprov Tags — Disable Automatic Dependency Processing

There may be times when RPM's automatic dependency processing is not desired. In these cases, the autoreqprov, autoreq, and autoprov tags may be used to disable it. This tag takes a **yes/no** or **0/1** value. For example, to disable automatic dependency processing, the following line may be used:

`AutoReqProv: no`

_ _ _


###The Provides Tag
`what is the Provides tag used for?`
#####Virtual Packages
Enter the virtual package. A virtual package is nothing more than a name specified with the Provides tag. Virtual packages are handy when a package requires a certain capability, and that capability can be provided by any one of several packages. Here's an example:

In order to work properly, sendmail needs a local delivery agent to handle mail delivery. There are a number of different local delivery agents available — sendmail will work just fine with any of them.

In this case, it doesn't make sense to force the use of a particular local delivery agent; as long as one's installed, sendmail's requirements will have been satisfied. So sendmail's package builder adds the following line to sendmail's spec file:

`Requires: lda`
            
There is no package with that name available, so sendmail's requirements must be met with a virtual package. The creators of the various local delivery agents indicate that their packages satisfy the requirements of the lda virtual package by adding the following line to their packages' spec files:

`Provides: lda`
            
(Note that virtual packages may not have version numbers.) Now, when sendmail is installed, as long as there is a package installed that provides the lda virtual package, there will be no problem.

**Notes**

As long as the requiring and the providing packages are installed using the same invocation of RPM, the dependency checking will succeed. For example, the command rpm -ivh *.rpm will properly check for dependencies, even if the requiring package ends up being installed before the providing package.






