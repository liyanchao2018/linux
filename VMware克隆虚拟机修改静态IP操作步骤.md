# Centos7虚拟机克隆复制后的一些网卡和IP修改

本文链接：<https://blog.csdn.net/greenplum_xiaofan/article/details/94150397>

> 亲测有效



### 文章目录

- [一、修改主机名和HOSTS文件](https://blog.csdn.net/greenplum_xiaofan/article/details/94150397#HOSTS_1)
- [二、修改网卡名（可选）](https://blog.csdn.net/greenplum_xiaofan/article/details/94150397#_11)
- [三、克隆虚拟机](https://blog.csdn.net/greenplum_xiaofan/article/details/94150397#_32)



# 一、修改主机名和HOSTS文件

[root@localhost ~]# hostnamectl set-hostname vm01
或者
[root@localhost ~]# vi /etc/hostname #重启后永久生效
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190702115454577.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dyZWVucGx1bV94aWFvZmFu,size_16,color_FFFFFF,t_70)
修改hosts文件，配置IP和主机名映射
[root@localhost ~]# vi /etc/hosts
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190629133654169.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dyZWVucGx1bV94aWFvZmFu,size_16,color_FFFFFF,t_70)
重启：reboot

# 二、修改网卡名（可选）

CentOS 7.x系统中网卡命名规则被重新定义，可能会是”ifcfg-eno16777736”，下面我们把网卡改为ifcfg-eth0这种。
[root@vm01 ~]# cd /etc/sysconfig/network-scripts/
[root@vm01 network-scripts]# mv ifcfg-eno16777736 ifcfg-eth0
[root@vm01 network-scripts]# vi ifcfg-eth0 
[root@vm01 network-scripts]# vi /etc/sysconfig/grub
在”GRUB_CMDLINE_LINUX“变量中添加一句”net.ifnames=0 biosdevname=0“

运行命令：grub2-mkconfig -o /boot/grub2/grub.cfg #重新生成grub配置并更新内核参数
[root@vm01 network-scripts]# grub2-mkconfig -o /boot/grub2/grub.cfg

添加udev的规则
在”/etc/udev/rules.d“目录中创建一个网卡规则”70-persistent-net.rules“,并写入下面的语句:
SUBSYSTEM==“net”,ACTION==“add”,DRIVERS=="?*",ATTR{address}“00:0c:29:b5:c7:b3”,ATTR｛type｝“1” ,KERNEL=="eth*",NAME=“eth0”

注意：#ATTR{address}=="00:0c:29:b5:c7:b3"是网卡的MAC地址	
可以通过ifconfgi命令查看mac地址

reboot重启
最后查看已经更名成功
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190629141027198.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dyZWVucGx1bV94aWFvZmFu,size_16,color_FFFFFF,t_70)

# 三、克隆虚拟机

克隆出来的虚拟机，因为和原来的配置是一模一样的，所以需要修改网卡和IP地址
右键虚拟机vm01-管理-克隆
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190629153448254.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dyZWVucGx1bV94aWFvZmFu,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190629153506735.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dyZWVucGx1bV94aWFvZmFu,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190629153519106.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dyZWVucGx1bV94aWFvZmFu,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/201906291535323.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dyZWVucGx1bV94aWFvZmFu,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190629153645348.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dyZWVucGx1bV94aWFvZmFu,size_16,color_FFFFFF,t_70)
大概等待1-2分钟，克隆成功
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190629153736362.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dyZWVucGx1bV94aWFvZmFu,size_16,color_FFFFFF,t_70)
启动vm02虚拟机，同时也启动vm01虚拟机（下面全是配置vm02）
生成新的mac地址
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190629154041797.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dyZWVucGx1bV94aWFvZmFu,size_16,color_FFFFFF,t_70)
修改IP地址
[root@vm02 Desktop]# vi /etc/sysconfig/network-scripts/ifcfg-eth0
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190629154319157.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dyZWVucGx1bV94aWFvZmFu,size_16,color_FFFFFF,t_70)
[root@vm02 Desktop]# cd /etc/udev/rules.d/
删除70-persistent-ipoib.rules
[root@vm02 rules.d]# rm 70-persistent-ipoib.rules

修改主机名
[root@vm02 rules.d]# hostnamectl set-hostname vm02
[root@vm02 rules.d]# vi /etc/sysconfig/network
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190629154601153.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dyZWVucGx1bV94aWFvZmFu,size_16,color_FFFFFF,t_70)
reboot重启
然后ping vm01的IP地址
[root@vm02 rules.d]# ping 192.168.1.10
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190629154748662.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dyZWVucGx1bV94aWFvZmFu,size_16,color_FFFFFF,t_70)

小惊喜：
如果发现无法获取IP地址，尝试执行下面命令（注意大写）
[root@vm01 ~]# systemctl stop NetworkManager
[root@vm01 ~]# systemctl disable NetworkManager

再重启网卡
[root@vm01 ~]# service network restart
或者
[root@vm01 ~]# systemctl restart network