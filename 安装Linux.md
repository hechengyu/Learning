

系统 镜像下载

[http://mirrors.bupt.edu.cn/centos/](http://mirrors.bupt.edu.cn/centos)

网络设置

```shell
cd /etc/sysconfig/network-scripts
vi ifcfg-ethXX
# 修改自启动
ONBOOT= yes
# 静态IP模式：
BOOTPROTO=static
# 添加静态IP信息
IPADDR=192.168.10.6
# 子网掩码
NETMASK=255.255.255.0
# 子网网关 
GATEWAY=192.168.10.2

service network restart

ping www.baidu.com

# 经查修改下如下配置文件即可
vim /etc/resolv.conf
# 添加如下内容：
nameserver 8.8.8.8

# 查看防火墙状态
systemctl status firewalld
# 防火墙开通端口
firewall-cmd --zone=public --add-port=888/tcp --permanent
# 加载防火墙配置文件
firewall-cmd   --reload
# 防火墙关闭端口
firewall-cmd --zone=public --remove-port=888/tcp --permanent
```



配置yum

[http://mirrors.aliyun.com/repo/Centos-7.repo](http://mirrors.aliyun.com/repo/Centos-7.repo )

```shell
cd /etc/yum.repos.d
# 备份旧的配置文件
mv CentOS-Base.repo CentOS-Base.repo.bak

# 清理缓存
yum clean all

# 重新生成缓存
yum makecache

# 再次查看yum源信息
yum repolist

yum install net-tools
yum install wget
```



修改主机名和ip

```shell
vi /etc/hostname
```



安装工具

```shell
yum install cpp -y 
yum install binutils -y
yum install glibc-kernheaders -y
yum install glibc-common -y
yum install glibc-devel -y
yum install gcc -y
yum install make -y
```



