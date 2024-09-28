# CiscoPackerTracer实验记录

## 实验一：交换机的Telnet远程登录和配置

**实验工具:Cisco Packet Tracer**

**实验目的:模拟实现交换机的Telnet远程登录和配置**

**实验过程:**

​	1、搭建拓扑图：交换机2960   PC机   

![72744354750](assets/1727443547500.png)

​	2、实验背景：交换机和PC机要在同一网段

​	3、实验过程：

```
# 交换机开启后，PC打开Terminal输入下述命令
en										# 进入特权模式  用户在该模式下可以查看并修改Cisco设备的配置
conf t                                	   # 进入全局配置模式 用户在该模式下可修改交换机的全局配置。如修改主机名
interface vlan 1                           # 进入接口模式，针对某个接口进行配置 这里是配置虚拟局限网vlan接口1
ip address 192.168.1.1 255.255.255.0       # 设置交换机的IP地址
no shutdown                                # 开启交换机
exit                                       # 返回上一级
enable password 123456                     # 设置登录特权模式的密码
line vty 0 4                               # 进入line配置模式 对虚拟终端（vty）和控制台端口进行配置 vty 0-4线路
password 5ijfj                             # 设置虚拟终端和控制台的用户级登录密码
login                                      # 让设置的密码生效
end
show running                               # 查看配置
```

接着设置PC的IP地址，要与交换机在同一网关

![72744432110](assets/1727444321108.png)

设置好IP之后，打开终端尝试ping 交换机的IP，看看能否ping通

![72744439214](assets/1727444392143.png)

实验成功

## 实验二：交换机划分VLAN配置

**实验工具:Cisco Packet Tracer**

**实验目的:交换机划分VLAN配置**

**实验过程:**

​	1、什么是VLAN？什么是Port VLAN？什么是Tag VLAN

​		VLAN是指在一个物理网段内，进行逻辑的划分，划分成若干个虚拟局域网。VLAN可以进行灵活的划分。相同的VLAN内的主机可以相互直接通信，不同的VLAN间的主机之间互相访问必须经由路由设备进行转发。广播数据包只可以在本VLAN内进行广播，不能传输到其他的VLAN中。

​		Port VLAN是实现VLAN的方式之一，它利用交换机的端口进行VLAN的划分，一个端口只能属于一个VLAN。

​		Tag VLAN是基于交换机端口的另外一种类型，主要用于使交换机的相同的VLAN内的主机之间可以直接访问，同时对于不同VLAN的主机进行隔离。

​	2、在CPT中搭建拓扑图

![72745187347](assets/1727451873479.png)

​	3、设置PC0-PC3的IP

​		分别为192.168.1.2/3/4/5 

​		默认网关均为192.168.1.1

​	4、配置交换机

```
en
conf t
vlan 2						#划分VLAN2
exit 
vlan 3						#划分VLAN3
exit
interface fa 0/1		 	 #配置0/1端口
switchport access vlan 2      #vlan2与access接口相连  access接口一般用于和不能识别Tag的用户终端（如用户主机、服务器等）相连
exit
interface fa 0/2			 #配置0/2端口
switchport access vlan 3      #vlan3也与access接口相连
exit
interface fa 0/24             #配置0/24端口
switchport mode trunk         #与Trunk接口相连 Trunk接口一般用于连接交换机、路由器、AP以及可同时收发Tagged帧和Untagged帧的语音终端
end
show vlan                      #查看配置
```

交换机0

![72745233098](assets/1727452330987.png)

交换机1

![72745263131](assets/1727452631315.png)

​	5、PING

​		PC0尝试ping PC1 和 PC3 的IP，结果如下

​		显然ping pc1 不通，但是pc3能通，因为都在vlan2网段下

![72745279359](assets/1727452793594.png)

​	根据这个实验结果，猜测PC1能ping通PC3，ping不通PC3

![72745299387](assets/1727452993879.png)

猜测正确，实验成功



**实验总结**

​	同一VLAN下的用户可以互相通信，就像在同一个局域网下，且不会广播到其他的VLAN中去。若没有路由，不同的两个VLAN不能够互相通信



