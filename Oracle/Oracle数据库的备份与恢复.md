# Oracle数据库的备份与恢复

# 恢复目标主机
## 查看磁盘空间
`df -h`

## 查看数据库目录

`cd /`

`ll /home/`

## 恢复数据库
`tar -zxvf home.tar.gz /home`


`umount /home`


是一个在类Unix系统中使用的命令，用于显示正在使用指定文件系统或设备的进程。这个命令特别有用，当您尝试卸载一个文件系统但失败时，可以用来找出哪些进程正在使用该文件系统。
`fuser -m /dev/mapper/vg_jingyi-lv_home`


`kill -9 11622`

`umount /home`

`lvremove /dev/vg_jingyi/lv_home`

`lvextend -l +100%FREE /dev/vg_jingyi/lv_root`

`df -TH`

`resize2fs /dev/vg_jingyi/lv_root`


`tar -zxvf home.tar.gz`

`vi /etc/fstab`

### 将`/dev/vg_jingyi/lv_home`加上`#`注释

`sed -i 's#SELINUX=.*#SELINUX=disabled#g' /etc/selinux/config`

`cat /etc/selinux/config`

`cat /etc/redhat-release`

停止iptables防火墙服务。
`service iptables stop`

设置iptables服务在系统启动时不自动启动。
`chkconfig iptables off`

将SELinux设置为宽容模式（Permissive），即不强制执行SELinux策略。
`setenforce 0`

查看当前SELinux模式（Enforcing或Permissive）。
`getenforce`

`hostname`

编辑网络配置文件，通常用于设置主机名和网络相关配置。
`vi /etc/sysconfig/network`


`vi /etc/hosts`



---

Connect SFTP Session

传输Oracle安装包

# 备份目标主机

`cd /rman/rmanbak/`