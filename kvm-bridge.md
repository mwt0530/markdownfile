##kvm桥接设置

####设置桥接
本机的物理网卡为enp4s0

```
# brctl addbr br0
# cd /etc/sysconfig/network-scripts/
# cp ifcfg-enp4s0 /opt/ifcfg-enp4s0.bk //备份
# cp ifcfg-enp4s0 ifcfg-br0

```
修改两个文件
ifcfg-enp4s0:注释必要行
```
HWADDR=60:02:92:54:CB:11
TYPE=Ethernet
BOOTPROTO=static
NAME=enp4s0
#UUID=4b9f6157-9872-45a5-a3fd-92a6fba16d27
ONBOOT=yes
#IPADDR=1.1.1.2
#NETMASK=255.255.255.0
NM_CONTROLLED=no
BRIDGE=br0
```

ifcfg-br0：注意修改为DEVICE=br0
```
TYPE=Bridge
BOOTPROTO=static
DEVICE=br0
ONBOOT=yes
IPADDR=1.1.1.2
NETMASK=255.255.255.0
NM_CONTROLLED=no
```
修改xml文件，手动修改或者在virt-manger图形修改
```
    <interface type='bridge'>
      <mac address='52:54:00:2b:a8:9c'/>
      <source bridge='br0'/>
      <model type='virtio'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0'/>
    </interface>
```

验证结果
```
# systemctl restart network
# brctl show
bridge name	bridge id		STP enabled	interfaces
br0		8000.60029254cb11	no		enp4s0
									vnet0
docker0		8000.0242f2c93d01	no		
virbr0		8000.000000000000	yes	
```

####恢复NAT

恢复原来的设置
```
# ifconfig br0 down
# brctl delbr br0
# mv /etc/sysconfig/network-scripts/ifcfg-br0 /opt/
# vim /etc/sysconfig/network-scripts/ifcfg-enp4s0
//打开注释，注释BRIDGE=br0
```
修改虚拟机设置
```
    <interface type='network'>
      <mac address='52:54:00:2b:a8:9c'/>
      <source network='default'/>
      <model type='virtio'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0'/>
    </interface>
```

####参考
http://www.chenyudong.com/archives/libvirt-kvm-bridge-network.html


