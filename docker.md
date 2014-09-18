# docker安装

*   Yum配置epel与puias repo 

http://dl.fedoraproject.org/pub/epel/7/x86_64/

http://puias.princeton.edu/data/puias/7/x86_64/os/

*   yum install docker docer-registry

*   注册docker hub

https://hub.docker.com/

*   启动docker服务进程

systemctl enable docker.service

systemctl start docker.service

*   docker进程重要流程

建立bridge docker0

建立100G的dm thin pool（backend为dm，如果是brtfs，aufs则不同）

添加udev规则避免桌面自动mount改blk设备

查看docker信息

*   拉取docker hub官方registry的centos镜像

docker pull centos:latest

# docker虚拟机运行

*   docker run –i –t centos /bin/bash进入centos镜像的虚拟机

*   Docker进程建立veth pair并添加进bridge

*   Docker虚拟机veth动态获得bridge网段ip

*   Docker进程建立10G的thin volume

# docker启动运行特定业务的虚拟机

*   Docker镜像封装理念

使用差分镜像实现container堆叠，每个container都封装单一任务

差分存储后端选择

*   从docker registry下载nginx镜像

*   手动制作基于base的定制镜像，Docker虚拟机镜像制作章节介绍

*   查看虚拟机运行状况

docker ps

docker inspect $dock_uuid

*   查看运行状态虚拟机内部状态

docker ps获得虚拟机uuid

docker ps获得虚拟机uuid

nsenter -m –u -n -i -p -t $pid /bin/bash进入虚拟机

# docker虚拟机镜像制作

*   现有image修改

docker run启动虚拟机，完成修改

docker ps –l找到当前修改虚拟机的uuid

docker commit -m $message -a $auth $uuid $name

docker image确认

（resolv.conf、hostname、hosts等文件是只读的，无法修改）

*   使用dockerfile生成

# Docker虚拟机隔离实现

*   PID namespace隔离进程ID

同一个进程，HOST与虚拟机中PID不同

不同虚拟机中可以有相同的PID

进程的pid namespace查询

*   Mnt namespace隔离进程所见目录

Host mount的目录虚拟机1不可见

虚拟机1 mount的目录虚拟机2不可见

mount命令查询/etc/mtab，可以通过cat /proc/mounts规避

* Net namespace隔离进程的网络协议栈

隔离各个虚拟机的snmp、route table、socket、procfs、sysfs、firewall、rule、net device

但是ip netns show看不到新建的ns ？

* Ipc namespace隔离进程间IPC通信

System V IPC ： message queue、semaphore、shared memory， POSIX message queue

* Uts namespace隔离虚拟机的主机名

* User namespace隔离虚拟机用户权限

隔离虚拟机内用户的uid、gid、capability

echo “namespace_first_uid host_first_uid number_of_uids” > /proc/pid/(u|g)id_map可以修改虚拟机中用户ID在host中的映射

Docker中“/proc/pid/(u|g)id_map”实现为“0 0 4294967295”

* Kernel namespace实现分析

增加nsproxy中间层，实现了一个广义的进程组概念

增加了uid_map、gid_map，实现了user namespace

增加新的syscall或者现有syscall的flag，为用户态提供接口

1 Clone()启动新进程，新的namespace

2 Unshare()使旧进程，进入新启动的namespace

3 Setns()使旧进程，进入已经启动的namespace

# Docker虚拟机QoS实现

* 使用Linux cgroup控制CPU、memory、IO带宽

使用blkio、devices、memory、cpu、cpuacct

使用cgroup的多挂载点方式

docker run –c 500 –m 2G –i –t /bin/bash

* Linux cgroup实现 [TODO]

* quota限制虚拟机使用的块设备容量

目前未实现，已经加入开发计划

* tc限制虚拟机使用的网络设备带宽

目前未实现

# Docker虚拟机安全加强

* SELinux/apparmor

* Grsecurity

* Seccomp

* LXC capability drop

* Cgroup device deny

# 参考资料

Redhat    Get Started with Docker Containers in RHEL 7

Dockfile   http://blog.tankywoo.com/docker/2014/05/08/docker-2-dockerfile.html
