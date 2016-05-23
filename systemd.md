##systemd unit编写

详细文档：
```
# man systemd.unit
# man systemd.service
# man systemd.mount
```
文件共享Unit和Install俩个部分，不同服务有不同的其他段，如Service、Mount。
服务文件存放在以下路径，首先被搜索到的会覆盖后面路径的同名服务：
```
/etc/systemd/system
/run/systemd/system
/usr/lib/systemd/system
```

####常用说明

**[Unit]**

* Requires=
代表本服务依赖于其他服务，和After=或Before=没有必要联系，见After=、Before=。
（1）如果本服务被激活，那么Requires=后面的服务也被激活。
（2）如果Requires=后面的服务被停止或者无法启动，那么本服务也停止。

* Wants=
相当于弱依赖，和Requires不同的是被列出的服务无法启动时不会影响到本服务的启动，大多数情况推荐使用Wants=。

* Conflicts=
代表依赖冲突，后启动的服务起作用。
（1）当本服务启动时，将停止列出的服务。
（2）当列出的服务启动时，将停止本服务。

* After=，Before=
代表服务启动顺序
（1）如果本服务包含After=，那么本服务将等待被列出的服务启动后再启动，在停止时候会先停止本服务，再停止被列出的服务。
（2）Before=相反。

**[Install]**

* WantedBy=, RequiredBy=
用于systemctl enable XXX时在/etc/systemd/system/对应的.wants/或者.requires/下生成本文件的软连接。结果就是对列出的服务增加了Wants=或者Requires=依赖，在被列出的服务启动时候，本服务就会启动。


**[Mount]**

* What=
要挂载的磁盘或文件，绝对路径。

* Where=
要挂载到何处，绝对路径，如果路径不存在就会被创建出来，此处的路径会影响这个服务的名字，例如/mnt/iso/,那么这个文件必须命名mnt-iso.mount。

**[Service]**

* Type=
1、simple 默认服务类型，Type=和BusName=都不设置,但是设置了ExecStart=，ExecStart=之后的程序是服务的主进程。
2、forking 用于ExecStart=之后的程序会调用fork()，父进程退出，子进程为主进程。
3、oneshot
字面意思就是执行完就退出，常搭配RemainAfterExit=
4、dbus
5、notify
6、idle

* ExecStart=
服务执行命令，如果不是oneshot类型，只能执行一条命令，可执行文件为绝对路径。

* ExecStop=
可选选项，不存在这个选项时systemctl stop会直接停止主进程。

* ExecStartPre=, ExecStartPost=
字面意思。

**简单示例**
```iso.service
[Unit]
Description=hello world

[Service]
ExecStart=/opt/hello

[Install]
WantedBy=multi-user.target
```

```mnt-iso.mount
[Unit]
Description=Mount iso

[Mount]
What=/dev/sr0
Where=/mnt/iso

[Install]
WantedBy=multi-user.target
```

```
[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/usr/sbin/ip tunnel add he-ipv6 mode sit remote 66.220.18.42 local 108.170.7.158 ttl 255 ; \
          /usr/sbin/ip link set he-ipv6 up ; \
          /usr/sbin/ip addr add 2001:470:c:1184::2/64 dev he-ipv6 ; \
          /usr/sbin/ip route add ::/0 dev he-ipv6 ; \
          /usr/sbin/ip -6 addr
ExecStop=/usr/sbin/ip route delete ::/0 dev he-ipv6 ; \
         /usr/sbin/ip -6 addr del 2001:470:c:1184::2/64 dev he-ipv6 ; \
         /usr/sbin/ip link set he-ipv6 down ; \
         /usr/sbin/ip tunnel del he-ipv6
[Install]
WantedBy=multi-user.target 
```


####参考
https://zh.opensuse.org/index.php?title=openSUSE:How_to_write_a_systemd_service&variant=zh#.E7.BC.96.E5.86.99_Systemd_service

