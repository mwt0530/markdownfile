##kgdb

https://kernel.org/pub/linux/kernel/people/jwessel/kdb/index.html
**kgdb over console**

####内核配置
``` 
# CONFIG_DEBUG_RODATA is not set
CONFIG_FRAME_POINTER=y
CONFIG_KGDB=y
CONFIG_KGDB_SERIAL_CONSOLE=y
...
```
####kgdbwait
The Kernel command line option **kgdbwait** makes kgdb wait for a debugger connection during booting of a kernel. You can only use this option you compiled a kgdb I/O driver into the kernel and you specified the I/O driver configuration as a kernel command line option. The kgdbwait parameter should **always follow** the configuration parameter for the kgdb I/O driver in the kernel command line else the I/O driver will not be configured prior to asking the kernel to use it to wait.

The kernel will stop and wait as early as the I/O driver and architecture allows when you use this option. If you build the kgdb I/O driver as a loadable kernel module kgdbwait will not do anything.

####启动项
内核启动项增加**kgdboc=ttyS0,115200 kgdbwait**

####xml配置
添加类似配置
```
<serial type='tcp'>
    <source mode='bind' host='127.0.0.1' service='6666'/>
    <protocol type='raw'/>
    <target port='0'/>
</serial>
```
####gdb连接
在启动虚拟机后会停止显示kgdb waiting for connection from remote gdb...字样。
```
gdb vmliux  //带符号的镜像
set remotebaud 115200
target remote :6666
```
