##firewalld&iptables

系统中只能使用firewalld和iptables其中一个。


####firewalld基础
配置文件`/etc/firewalld/`和`/usr/lib/firewalld/`
**target**：可选值：default、ACCEPT、%%REJECT%%、DROP，不设置默认为default，设置在zone的XML文件中，代表zone的默认动作。
**service**：服务，简单理解为将特殊端口命名
**port**：端口，使用port可以不通过service而直接对端口进行设置
**interface**：接口，可以理解为网卡
**source**：源地址，可以是IP地址也可以是ip地址段
**icmp-block**：icmp报文阻塞，可以按照icmp类型进行设置
**masquerade**：ip地址伪装，按照源网卡地址进行NAT转发
**forward-port**：端口转发
**rule**：自定义规则

按照以下顺序判断：
```
source，源地址
interface，接收请求的网卡
firewalld.conf中配置的默认zone
```

####基本命令
1.**zone**操作
firewalld包括Drop、Block、Public、External、DMZ、Work、Home、Internal、Trusted域。
```
# firewall-cmd --get-zones
# firewall-cmd --get-default-zone
# firewall-cmd --set-default-zone=XXXX
# firewall-cmd --list-all-zones //查询包含每个域的详细信息
# firewall-cmd --get-zone-of-interface=enp4s0
# firewall-cmd --get-active-zones //返回设置了interface or source的zone信息
```

2.service
设置自定义service,一般从系统定义的服务复制然后修改。
```
# cd /etc/firewalld/services/
# cp /usr/lib/firewalld/services/ssh.xml /etc/firewalld/services/myservice.xml
# vim myservice.xml
# firewall-cmd --reload //非永久设置选项将丢失
# firewall-cmd --get-services
# firewall-cmd --add-service=myservice
# firewall-cmd --zone=public --remove-service=myservice
```
3.常用设置
```
# firewall-cmd --permanent --add-source=192.168.0.0/24
# firewall-cmd --permanent --add-port=24800/tcp
# firewall-cmd --add-forward-port=port=80:proto=tcp:toaddr=192.168.122.211:toport=8000
//端口转发，将本机80端口接受的请求转发至192.168.122.211的8000端口
```
>--permanent代表永久设置，写进配置文件,不会立即生效，需要reload生效。

4.Rich Rules
```
# firewall-cmd --add-rich-rule 'rule family="ipv4" source address="192.168.0.0/24" service name="http" accept' 
# firewall-cmd --add-rich-rule 'rule family="ipv4" source address="192.168.0.0/24" service name="http" accept' --permanent
```
5.panic模式
进入panic模式，incoming and outgoing packets are dropped
```
# firewall-cmd --panic-on
# firewall-cmd --panic-off
# firewall-cmd --query-panic
```

********
####iptables基础知识
iptables内置4个表（实际5张表，security），即raw表、filter表、nat表和mangle表，用于实现包过滤，网络地址转换和包重构的功能，一般使用**filter表、nat表**。规则表之间的顺序如下：

（1）RAW表
只使用在PREROUTING链和OUTPUT链上,因为优先级最高，从而可以对收到的数据包在连接跟踪前进行处理。一旦用户使用了RAW表,在 某个链上,RAW表处理完后，将跳过NAT表和 ip_conntrack处理,即不再做地址转换和数据包的链接跟踪处理了。

（2）mangle表
主要用于对指定数据包进行更改，在内核版本2.4.18 后的linux版本中该表包含的链为：INPUT链（处理进入的数据包），RORWARD链（处理转发的数据包），OUTPUT链（处理本地生成的数据包）POSTROUTING链（修改即将出去的数据包），PREROUTING链（修改即将到来的数据包）

（3）nat表
主要用于网络地址转换NAT，该表可以实现一对一，一对多，多对多等NAT 工作，iptables就是使用该表实现共享上网的，NAT表包含了PREROUTING链（修改即将到来的数据包），POSTROUTING链（修改即将出去的数据包），OUTPUT链（修改路由之前本地生成的数据包）

（4）filter表
主要用于过滤数据包，该表根据系统管理员预定义的一组规则过滤符合条件的数据包。对于防火墙而言，主要利用在filter表中指定的规则来实现对数据包的过滤。Filter表是默认的表，如果没有指定哪个表，iptables 就默认使用filter表来执行所有命令，filter表包含了INPUT链（处理进入的数据包），RORWARD链（处理转发的数据包），OUTPUT链（处理本地生成的数据包）在filter表中只能允许对数据包进行接受，丢弃的操作，而无法对数据包进行更改。


####iptables操作
1.查看对应表的规则
```
# iptables -nvL --line-numbers
# iptables -t nat -nvL --line-numbers
```
2.重置防火墙规则，-F：一条一条删除所有。-X：删除用户定义的非默认的链。
```
# iptables -F
# iptables -X
# iptables -t nat -F
# iptables -t nat -X
# iptables -t mangle -F
# iptables -t mangle -X
# iptables -t raw -F
# iptables -t raw -X
# iptables -t security -F
# iptables -t security -X
# iptables -P INPUT ACCEPT
# iptables -P FORWARD ACCEPT
# iptables -P OUTPUT ACCEPT
```


