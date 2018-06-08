# 虚拟化那些事情
安装mcord真的是很糟心很糟心很糟心的过程，有很多名词都没有听说过，找起bug来也是非常费劲，索性就学习一下
## libvert
库 libvirt 是支持不同的虚拟化技术的统一接口。

Libvirt 是一组软件的汇集，提供了管理虚拟机和其它虚拟化功能（如：存储和网络接口等）的便利途径。这些软件包括：一个长期稳定的 C 语言 API、一个守护进程（libvirtd）和一个命令行工具（virsh）。Libvirt 的主要目标是提供一个单一途径以管理多种不同虚拟化方案以及虚拟化主机，包括：KVM/QEMU，Xen，LXC，OpenVZ 或 VirtualBox hypervisors 

我们这里需要管理的就是KVM

Libvirt 的一些主要功能如下：

VM management（虚拟机管理）：各种虚拟机生命周期的操作，如：启动、停止、暂停、保存、恢复和迁移等；多种不同类型设备的热插拔操作，包括磁盘、网络接口、内存、CPU等。

Remote machine support（支持远程连接）：Libvirt 的所有功能都可以在运行着 libvirt 守护进程的机器上执行，包括远程机器。通过最简便且无需额外配置的 SSH 协议，远程连接可支持多种网络连接方式。

Storage management（存储管理）：任何运行 libvirt 守护进程的主机都可以用于管理多种类型的存储：创建多种类型的文件镜像（qcow2，vmdk，raw，...），挂载 NFS 共享，枚举现有 LVM 卷组，创建新的 LVM 卷组和逻辑卷，对裸磁盘设备分区，挂载 iSCSI 共享，以及更多......

Network interface management（网络接口管理）：任何运行 libvirt 守护进程的主机都可以用于管理物理的和逻辑的网络接口，枚举现有接口，配置（和创建）接口、桥接、VLAN、端口绑定。

Virtual NAT and Route based networking（虚拟 NAT 和基于路由的网络）：任何运行 libvirt 守护进程的主机都可以管理和创建虚拟网络。Libvirt 虚拟网络使用防火墙规则实现一个路由器，为虚拟机提供到主机网络的透明访问
``` 
zhangsiyu@zhangsiyu-Lenovo-Product:~/cord/build$ vagrant --version
Vagrant 1.9.3
zhangsiyu@zhangsiyu-Lenovo-Product:~/cord/build$ vagrant box list
ubuntu/trusty64 (libvirt, 20180604.0.0)
ubuntu/trusty64 (virtualbox, 20180604.0.0)
```
```
zhangsiyu@zhangsiyu-Lenovo-Product:~/cord/build$ vagrant global-status
id       name    provider state   directory                                 
----------------------------------------------------------------------------
b081467  head1   libvirt running /home/zhangsiyu/cord/build/scenarios/cord 
98242ad  corddev libvirt running /home/zhangsiyu/cord/build/scenarios/cord 

```
参考：
https://wiki.archlinux.org/index.php/libvirt_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)

