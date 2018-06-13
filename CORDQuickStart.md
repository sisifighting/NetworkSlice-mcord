# CORD-in-a-Box Quick Start Guide
遇到问题：

1. Cannot get https://gerrit.googlesource.com/git-repo/clone.bundle

参考：https://blog.csdn.net/xiaokeweng/article/details/46743409

解决方案： 在CORD-in-a-Box文件中 init repo命令中加入--repo-url:https://gerrit-google.tuna.tsinghua.edu.cn/git-repo

2. unknown hostname https://gerrit-google.tuna.tsinghua.edu.cn/git-repo

解决方案：这个时候有点怀疑人生，在考虑是不是域名换了，果然……换成了 https://mirrors.tuna.tsinghua.edu.cn/git/git-repo

参考：https://github.com/tuna/issues/issues/360

3. Could not fetch specs from http://gems.hashicorp.com/

解决方案：没得办法，只能科学上网了，设置虚拟机使用主机代理，但仍然不可以，还需要配置环境变量

参考：https://stackoverflow.com/questions/33109186/how-to-install-the-proxy-plugin-behind-a-proxy-for-vagrant

4. Could not fetch specs from https://rubygems.org/

解决方案：安装ruby-bundler后重试，可能是因为网络问题

5. 
``` 
<p>Setting up nfs-common (1:1.2.8-9ubuntu12.1) ...
dpkg: error processing package nfs-common (--configure):
 subprocess installed post-installation script returned error exit status 10
dpkg: dependency problems prevent configuration of nfs-kernel-server:
 nfs-kernel-server depends on nfs-common (= 1:1.2.8-9ubuntu12.1); however:
  Package nfs-common is not configured yet.

dpkg: error processing package nfs-kernel-server (--configure):
 dependency problems - leaving unconfigured
Errors were encountered while processing:
 nfs-common
 nfs-kernel-server</p>
 ```
 
 解决方案：用了一天的时间，各种apt-get 的更新重装命令
 参考了一些：
 https://askubuntu.com/questions/448961/subprocess-installed-post-installation-script-returned-error-exit-status-247
 https://bugs.launchpad.net/ubuntu/+source/nfs-utils/+bug/1023382
 https://askubuntu.com/questions/216287/unable-to-install-files-with-apt-get-unable-to-locate-package
 http://www.cnblogs.com/ghostwu/p/9102749.html
 后来发现是/var/lib/dpkg/info中恢复的时候版本有冲突，最后的解决方案是：
 
 备份 /var/lib/dpkg/info ，然后删除该文件夹
 然后运行update和强制install，再把新产生的info文件夹拷贝到备份的老文件夹上，再把老的改回info的名字。
 
 6. 
 ``` 
 task path: /home/zhangsiyu/cord/build/platform-install/roles/prereqs-common/tasks/main.yml:20
Tuesday 05 June 2018  09:18:13 +0800 (0:00:00.559)       0:00:00.590 ********** 
Pausing for 10 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
The full traceback is:
Traceback (most recent call last):
  File "/usr/lib/python2.7/dist-packages/ansible/executor/task_executor.py", line 138, in run
    res = self._execute()
  File "/usr/lib/python2.7/dist-packages/ansible/executor/task_executor.py", line 561, in _execute
    result = self._handler.run(task_vars=variables)
  File "/usr/lib/python2.7/dist-packages/ansible/plugins/action/pause.py", line 202, in run
    tty.setraw(stdout.fileno())
  File "/usr/lib/python2.7/tty.py", line 20, in setraw
    mode = tcgetattr(fd)
error: (25, 'Inappropriate ioctl for device')
fatal: [localhost]: FAILED! => {
    "msg": "Unexpected failure during module execution.", 
    "stdout": ""
}
 ``` 
 打开Makefile文件，运行命令
  ``` 
 ansible-playbook -vvv --skip-tags "set_compute_node_password,switch_support,reboot,interface_config" -i ./genconfig/inventory.ini --extra-vars @./genconfig/config.yml ./platform-install/prereqs-check-playbook.yml 2>&1 | tee -a ./logs/prereqs-chec
  ``` 
  
  是tee命令报的错误，没有解决，只好修改LOGCMD 不做重定向
  
  7.
 ```   
  VAGRANT_CWD=./scenarios/cord/ vagrant up corddev head1 --provider libvirt 2>&1 
Error while connecting to libvirt: Error making a connection to libvirt URI qemu:///system?no_verify=1&keyfile=/home/zhangsiyu/.ssh/id_rsa:
Call to virConnectOpen failed: Failed to connect socket to '/var/run/libvirt/libvirt-sock': Permission denied
 ``` 
 修改脚本，在VAGRANT_CWD前加入sudo
 8.
 ```
 sudo ansible-playbook --skip-tags "set_compute_node_password,switch_support,reboot,interface_config" -i ./genconfig/inventory.ini --extra-vars @./genconfig/config.yml ./platform-install/copy-cord-playbook.yml 2>&1 

PLAY [Include vars] **************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************
Wednesday 06 June 2018  15:53:28 +0800 (0:00:00.027)       0:00:00.027 ******** 
fatal: [head1]: UNREACHABLE! => {"changed": false, "msg": "Failed to connect to the host via ssh: ssh: Could not resolve hostname head1: Name or service not known\r\n", "unreachable": true}
        to retry, use: --limit @/home/zhangsiyu/cord/build/platform-install/copy-cord-playbook.retry

PLAY RECAP ***********************************************************************************************************************************************
head1                      : ok=0    changed=0    unreachable=1    failed=0   

Wednesday 06 June 2018  15:53:56 +0800 (0:00:27.514)       0:00:27.541 ******** 
=============================================================================== 
Gathering Facts ---------------------------------------------------------------------------------------------------------------------------------- 27.51s
Makefile:219: recipe for target 'milestones/copy-cord' failed
make: *** [milestones/copy-cord] Error 4

 ```
因为出现了问题7，就将所有的命令都改成了sudo
出现了上述问题，去掉sudo
 ```
 ansible-playbook --skip-tags "set_compute_node_password,switch_support,reboot,interface_config" -i ./genconfig/inventory.ini --extra-vars @./genconfig/config.yml ./platform-install/copy-cord-playbook.yml 2>&1

PLAY [Include vars] **************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************
Wednesday 06 June 2018  15:58:02 +0800 (0:00:00.026)       0:00:00.026 ******** 
fatal: [head1]: FAILED! => {"msg": "Cannot write to ControlPath /home/zhangsiyu/.ansible/cp"}
        to retry, use: --limit @/home/zhangsiyu/cord/build/platform-install/copy-cord-playbook.retry

PLAY RECAP ***********************************************************************************************************************************************
head1                      : ok=0    changed=0    unreachable=0    failed=1   

Wednesday 06 June 2018  15:58:02 +0800 (0:00:00.181)       0:00:00.208 ******** 
=============================================================================== 
Gathering Facts ----------------------------------------------------------------------------------------------------------------------------------- 0.18s
 ```
 解决方案：
 https://stackoverflow.com/questions/42421774/ansible-permission-problems
 
 修改 /home/zhangsiyu/.ansible/cp权限
 
 8. 
 解决方案：查看vagrant虚机相关配置，查看ssh配置是否正确
 ```
zhangsiyu@zhangsiyu-Lenovo-Product:~/cord/build$ vagrant global-status
id       name    provider state   directory                                 
----------------------------------------------------------------------------
b081467  head1   libvirt running /home/zhangsiyu/cord/build/scenarios/cord 
98242ad  corddev libvirt running /home/zhangsiyu/cord/build/scenarios/cord 


zhangsiyu@zhangsiyu-Lenovo-Product:~/cord/build$ sudo vagrant ssh b081467  
[sudo] password for zhangsiyu: 
Welcome to Ubuntu 14.04.5 LTS (GNU/Linux 3.13.0-149-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Jun  7 03:22:01 UTC 2018

  System load:  0.07              Processes:           163
  Usage of /:   3.6% of 39.34GB   Users logged in:     0
  Memory usage: 1%                IP address for eth0: 192.168.121.116
  Swap usage:   0%                IP address for eth1: 10.100.198.201

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


Last login: Thu Jun  7 03:22:26 2018 from 192.168.121.1

```
原因：vagrant相关命令由于权限限制用了root权限，而ansible命令未用root权限，导致无法连接
更改：修改.ssh文件夹中的配置，并修改~/cord/build/scenarios/cord/.vagrant/machines中的创建用户。
9.下载镜像的时候发现镜像地址http://www.vicci.org/cord 访问不了

解决方案：查看platform-install/profile_manifests/mcord-ng40.yml文件中配置的镜像地址，翻墙下载，并在本地搭建http服务器，将下载路径改到本地服务器上，通过本地下载。

10.
 ```
fatal: [corddev]: FAILED! => {"cache_update_time": 1528443151, "cache_updated": false, "changed": false, "msg": "'/usr/bin/apt-get -y -o \"Dpkg::Options::=--force-confdef\" -o \"Dpkg::Options::=--force-confold\"     install 'docker-ce=17.06.*'' failed: E: Failed to fetch https://download.docker.com/linux/ubuntu/dists/trusty/pool/stable/amd64/docker-ce_17.06.2~ce-0~ubuntu_amd64.deb  Operation timed out after 0 milliseconds with 0 out of 0 bytes received\n\nE: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?\n", "rc": 100, "stderr": "E: Failed to fetch https://download.docker.com/linux/ubuntu/dists/trusty/pool/stable/amd64/docker-ce_17.06.2~ce-0~ubuntu_amd64.deb  Operation timed out after 0 milliseconds with 0 out of 0 bytes received\n\nE: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?\n", "stderr_lines": ["E: Failed to fetch https://download.docker.com/linux/ubuntu/dists/trusty/pool/stable/amd64/docker-ce_17.06.2~ce-0~ubuntu_amd64.deb  Operation timed out after 0 milliseconds with 0 out of 0 bytes received", "", "E: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?"], "stdout": "Reading package lists...\nBuilding dependency tree...\nReading state information...\nThe following extra packages will be installed:\n  aufs-tools cgroup-lite git git-man liberror-perl libsystemd-journal0\nSuggested packages:\n  git-daemon-run git-daemon-sysvinit git-doc git-el git-email git-gui gitk\n  gitweb git-arch git-bzr git-cvs git-mediawiki git-svn\nThe following NEW packages will be installed:\n  aufs-tools cgroup-lite docker-ce git git-man liberror-perl\n  libsystemd-journal0\n0 upgraded, 7 newly installed, 0 to remove and 0 not upgraded.\nNeed to get 24.0 MB of archives.\nAfter this operation, 119 MB of additional disk space will be used.\nGet:1 http://archive.ubuntu.com/ubuntu/ trusty-updates/main libsystemd-journal0 amd64 204-5ubuntu20.28 [50.8 kB]\nGet:2 http://archive.ubuntu.com/ubuntu/ trusty/universe aufs-tools amd64 1:3.2+20130722-1.1 [92.3 kB]\nGet:3 http://archive.ubuntu.com/ubuntu/ trusty/main liberror-perl all 0.17-1.1 [21.1 kB]\nGet:4 http://archive.ubuntu.com/ubuntu/ trusty-updates/main git-man all 1:1.9.1-1ubuntu0.8 [701 kB]\nGet:5 http://archive.ubuntu.com/ubuntu/ trusty-updates/main git amd64 1:1.9.1-1ubuntu0.8 [2672 kB]\nGet:6 http://archive.ubuntu.com/ubuntu/ trusty/main cgroup-lite all 1.9 [3918 B]\nErr https://download.docker.com/linux/ubuntu/ trusty/stable docker-ce amd64 17.06.2~ce-0~ubuntu\n  Operation timed out after 0 milliseconds with 0 out of 0 bytes received\nFetched 3541 kB in 2min 0s (29.5 kB/s)\n", "stdout_lines": ["Reading package lists...", "Building dependency tree...", "Reading state information...", "The following extra packages will be installed:", "  aufs-tools cgroup-lite git git-man liberror-perl libsystemd-journal0", "Suggested packages:", "  git-daemon-run git-daemon-sysvinit git-doc git-el git-email git-gui gitk", "  gitweb git-arch git-bzr git-cvs git-mediawiki git-svn", "The following NEW packages will be installed:", "  aufs-tools cgroup-lite docker-ce git git-man liberror-perl", "  libsystemd-journal0", "0 upgraded, 7 newly installed, 0 to remove and 0 not upgraded.", "Need to get 24.0 MB of archives.", "After this operation, 119 MB of additional disk space will be used.", "Get:1 http://archive.ubuntu.com/ubuntu/ trusty-updates/main libsystemd-journal0 amd64 204-5ubuntu20.28 [50.8 kB]", "Get:2 http://archive.ubuntu.com/ubuntu/ trusty/universe aufs-tools amd64 1:3.2+20130722-1.1 [92.3 kB]", "Get:3 http://archive.ubuntu.com/ubuntu/ trusty/main liberror-perl all 0.17-1.1 [21.1 kB]", "Get:4 http://archive.ubuntu.com/ubuntu/ trusty-updates/main git-man all 1:1.9.1-1ubuntu0.8 [701 kB]", "Get:5 http://archive.ubuntu.com/ubuntu/ trusty-updates/main git amd64 1:1.9.1-1ubuntu0.8 [2672 kB]", "Get:6 http://archive.ubuntu.com/ubuntu/ trusty/main cgroup-lite all 1.9 [3918 B]", "Err https://download.docker.com/linux/ubuntu/ trusty/stable docker-ce amd64 17.06.2~ce-0~ubuntu", "  Operation timed out after 0 milliseconds with 0 out of 0 bytes received", "Fetched 3541 kB in 2min 0s (29.5 kB/s)"]}
 ```
 
 No matching distribution found for python-mistralclient===3.6.0 (from -c /opt/stack/requirements/upper-constraints.txt (line 389))



2018-06-13 14:53:43.325 DEBUG tacker.api.v1.base [req-7e112240-98ad-4043-9031-a186288838c6 admin admin] Request body: {u'scale': {u'policy': u'SP1', u'type': u'out'}} from (pid=31272) prepare_request_body /opt/stack/tacker/tacker/api/v1/base.py:510
2018-06-13 14:53:43.333 DEBUG tacker.db.vnfm.vnfm_db [req-7e112240-98ad-4043-9031-a186288838c6 admin admin] vnf_db <tacker.db.vnfm.vnfm_db.VNF[object at 7f11f48ceed0] {tenant_id=u'3dceca7c16ea41d0a178d5c9a1485af2', id=u'e2718c39-8079-42c0-9b18-18400eb11067', created_at=datetime.datetime(2018, 6, 13, 6, 50, 21), updated_at=None, deleted_at=datetime.datetime(1, 1, 1, 0, 0), vnfd_id=u'c4b66bfe-8952-4930-b709-4ba606a909ab', name=u'VNFSCALE3', description=u'sample-tosca-vnfd-scaling', instance_id=u'48ec1539-9b37-4999-be5a-52cd4b82f8a9', mgmt_url=u'{"VDU1": ["192.168.120.9"], "VDU2": ["192.168.120.16"]}', status=u'ACTIVE', vim_id=u'c75df318-b349-4a50-a4b2-2625a7037ae5', placement_attr={u'vim_name': u'openstack1'}, error_reason=None}> from (pid=31272) _make_vnf_dict /opt/stack/tacker/tacker/db/vnfm/vnfm_db.py:219
2018-06-13 14:53:43.337 DEBUG tacker.db.vnfm.vnfm_db [req-7e112240-98ad-4043-9031-a186288838c6 admin admin] vnf_db attributes [<tacker.db.vnfm.vnfm_db.VNFAttribute[object at 7f11f48a4bd0] {id=u'03c482d7-6d09-4266-90d7-8b5213f0ab6a', vnf_id=u'e2718c39-8079-42c0-9b18-18400eb11067', key=u'scaling_group_names', value=u'{"SP1": "SP1_group"}'}>, <tacker.db.vnfm.vnfm_db.VNFAttribute[object at 7f11f48a4750] {id=u'4f790b44-1a47-4530-9680-e931870b5667', vnf_id=u'e2718c39-8079-42c0-9b18-18400eb11067', key=u'SP1_res.yaml', value=u"heat_template_version: 2013-05-23\ndescription: Tacker Scaling template\nresources:\n  VDU1:\n    type: OS::Nova::Server\n    properties:\n      user_data_format: SOFTWARE_CONFIG\n      availability_zone: nova\n      image: cirros-0.3.5-x86_64-disk\n      flavor: m1.tiny\n      networks:\n      - port: {get_resource: CP1}\n      config_drive: false\n  CP1:\n    type: OS::Neutron::Port\n    properties: {port_security_enabled: false, network: net_mgmt}\n  CP2:\n    type: OS::Neutron::Port\n    properties: {port_security_enabled: false, network: net_mgmt}\n  VDU2:\n    type: OS::Nova::Server\n    properties:\n      user_data_format: SOFTWARE_CONFIG\n      availability_zone: nova\n      image: cirros-0.3.5-x86_64-disk\n      flavor: m1.tiny\n      networks:\n      - port: {get_resource: CP2}\n      config_drive: false\n  VL1: {type: 'OS::Neutron::Net'}\noutputs:\n  mgmt_ip-VDU1:\n    value:\n      get_attr: [CP1, fixed_ips, 0, ip_address]\n  mgmt_ip-VDU2:\n    value:\n      get_attr: [CP2, fixed_ips, 0, ip_address]\n"}>, <tacker.db.vnfm.vnfm_db.VNFAttribute[object at 7f11f48a4a90] {id=u'78ed9249-7e03-4153-8147-53692395721e', vnf_id=u'e2718c39-8079-42c0-9b18-18400eb11067', key=u'heat_template', value=u"heat_template_version: 2013-05-23\ndescription: 'sample-tosca-vnfd-scaling\n\n  '\nparameters: {}\nresources:\n  SP1_scale_out:\n    type: OS::Heat::ScalingPolicy\n    properties:\n      auto_scaling_group_id: {get_resource: SP1_group}\n      adjustment_type: change_in_capacity\n      scaling_adjustment: 1\n      cooldown: 120\n  SP1_group:\n    type: OS::Heat::AutoScalingGroup\n    properties:\n      min_size: 1\n      desired_capacity: 1\n      cooldown: 120\n      resource: {type: SP1_res.yaml}\n      max_size: 3\n  SP1_scale_in:\n    type: OS::Heat::ScalingPolicy\n    properties:\n      auto_scaling_group_id: {get_resource: SP1_group}\n      adjustment_type: change_in_capacity\n      scaling_adjustment: -1\n      cooldown: 120\noutputs: {}\n"}>] from (pid=31272) _make_vnf_dict /opt/stack/tacker/tacker/db/vnfm/vnfm_db.py:220
2018-06-13 14:53:43.375 DEBUG tacker.vnfm.plugin [req-7e112240-98ad-4043-9031-a186288838c6 admin admin] Policy SP1 is validated successfully from (pid=31272) _validate_scaling_policy /opt/stack/tacker/tacker/vnfm/plugin.py:575
2018-06-13 14:53:43.401 DEBUG tacker.db.vnfm.vnfm_db [req-7e112240-98ad-4043-9031-a186288838c6 admin admin] vnf_db <tacker.db.vnfm.vnfm_db.VNF[object at 7f11f477a990] {tenant_id=u'3dceca7c16ea41d0a178d5c9a1485af2', id=u'e2718c39-8079-42c0-9b18-18400eb11067', created_at=datetime.datetime(2018, 6, 13, 6, 50, 21), updated_at=None, deleted_at=datetime.datetime(1, 1, 1, 0, 0), vnfd_id=u'c4b66bfe-8952-4930-b709-4ba606a909ab', name=u'VNFSCALE3', description=u'sample-tosca-vnfd-scaling', instance_id=u'48ec1539-9b37-4999-be5a-52cd4b82f8a9', mgmt_url=u'{"VDU1": ["192.168.120.9"], "VDU2": ["192.168.120.16"]}', status='PENDING_SCALE_OUT', vim_id=u'c75df318-b349-4a50-a4b2-2625a7037ae5', placement_attr={u'vim_name': u'openstack1'}, error_reason=None}> from (pid=31272) _make_vnf_dict /opt/stack/tacker/tacker/db/vnfm/vnfm_db.py:219
2018-06-13 14:53:43.410 DEBUG tacker.db.vnfm.vnfm_db [req-7e112240-98ad-4043-9031-a186288838c6 admin admin] vnf_db attributes [<tacker.db.vnfm.vnfm_db.VNFAttribute[object at 7f11f4882150] {id=u'03c482d7-6d09-4266-90d7-8b5213f0ab6a', vnf_id=u'e2718c39-8079-42c0-9b18-18400eb11067', key=u'scaling_group_names', value=u'{"SP1": "SP1_group"}'}>, <tacker.db.vnfm.vnfm_db.VNFAttribute[object at 7f11f481c490] {id=u'4f790b44-1a47-4530-9680-e931870b5667', vnf_id=u'e2718c39-8079-42c0-9b18-18400eb11067', key=u'SP1_res.yaml', value=u"heat_template_version: 2013-05-23\ndescription: Tacker Scaling template\nresources:\n  VDU1:\n    type: OS::Nova::Server\n    properties:\n      user_data_format: SOFTWARE_CONFIG\n      availability_zone: nova\n      image: cirros-0.3.5-x86_64-disk\n      flavor: m1.tiny\n      networks:\n      - port: {get_resource: CP1}\n      config_drive: false\n  CP1:\n    type: OS::Neutron::Port\n    properties: {port_security_enabled: false, network: net_mgmt}\n  CP2:\n    type: OS::Neutron::Port\n    properties: {port_security_enabled: false, network: net_mgmt}\n  VDU2:\n    type: OS::Nova::Server\n    properties:\n      user_data_format: SOFTWARE_CONFIG\n      availability_zone: nova\n      image: cirros-0.3.5-x86_64-disk\n      flavor: m1.tiny\n      networks:\n      - port: {get_resource: CP2}\n      config_drive: false\n  VL1: {type: 'OS::Neutron::Net'}\noutputs:\n  mgmt_ip-VDU1:\n    value:\n      get_attr: [CP1, fixed_ips, 0, ip_address]\n  mgmt_ip-VDU2:\n    value:\n      get_attr: [CP2, fixed_ips, 0, ip_address]\n"}>, <tacker.db.vnfm.vnfm_db.VNFAttribute[object at 7f11f481cad0] {id=u'78ed9249-7e03-4153-8147-53692395721e', vnf_id=u'e2718c39-8079-42c0-9b18-18400eb11067', key=u'heat_template', value=u"heat_template_version: 2013-05-23\ndescription: 'sample-tosca-vnfd-scaling\n\n  '\nparameters: {}\nresources:\n  SP1_scale_out:\n    type: OS::Heat::ScalingPolicy\n    properties:\n      auto_scaling_group_id: {get_resource: SP1_group}\n      adjustment_type: change_in_capacity\n      scaling_adjustment: 1\n      cooldown: 120\n  SP1_group:\n    type: OS::Heat::AutoScalingGroup\n    properties:\n      min_size: 1\n      desired_capacity: 1\n      cooldown: 120\n      resource: {type: SP1_res.yaml}\n      max_size: 3\n  SP1_scale_in:\n    type: OS::Heat::ScalingPolicy\n    properties:\n      auto_scaling_group_id: {get_resource: SP1_group}\n      adjustment_type: change_in_capacity\n      scaling_adjustment: -1\n      cooldown: 120\noutputs: {}\n"}>] from (pid=31272) _make_vnf_dict /opt/stack/tacker/tacker/db/vnfm/vnfm_db.py:220
2018-06-13 14:53:43.425 DEBUG tacker.common.log [req-7e112240-98ad-4043-9031-a186288838c6 admin admin] tacker.db.common_services.common_services_db_plugin.CommonServicesPluginDb method create_event called with arguments (<tacker.context.Context object at 0x7f11f48cea90>,) {'res_type': 'vnf', 'evt_type': 'SCALE', 'res_id': u'e2718c39-8079-42c0-9b18-18400eb11067', 'res_state': 'PENDING_SCALE_OUT', 'tstamp': datetime.datetime(2018, 6, 13, 6, 53, 43, 425257)} from (pid=31272) wrapper /opt/stack/tacker/tacker/common/log.py:34
2018-06-13 14:53:43.444 DEBUG tacker.vnfm.plugin [req-7e112240-98ad-4043-9031-a186288838c6 admin admin] Policy SP1 vnf is at PENDING_SCALE_OUT from (pid=31272) _handle_vnf_scaling_pre /opt/stack/tacker/tacker/vnfm/plugin.py:594
2018-06-13 14:53:43.459 DEBUG tacker.vnfm.vim_client [req-7e112240-98ad-4043-9031-a186288838c6 admin admin] VIM info found for vim id c75df318-b349-4a50-a4b2-2625a7037ae5 from (pid=31272) get_vim /opt/stack/tacker/tacker/vnfm/vim_client.py:55
2018-06-13 14:53:43.460 DEBUG tacker.vnfm.vim_client [req-7e112240-98ad-4043-9031-a186288838c6 admin admin] VIM id is c75df318-b349-4a50-a4b2-2625a7037ae5 from (pid=31272) _build_vim_auth /opt/stack/tacker/tacker/vnfm/vim_client.py:71
2018-06-13 14:53:43.544 DEBUG barbicanclient.client [req-7e112240-98ad-4043-9031-a186288838c6 admin admin] Creating Client object from (pid=31272) Client /opt/stack/python-barbicanclient/barbicanclient/client.py:156
2018-06-13 14:53:43.554 DEBUG barbicanclient.v1.secrets [req-7e112240-98ad-4043-9031-a186288838c6 admin admin] Getting secret - Secret href: http://127.0.0.1/key-manager/v1/secrets/cde0888e-1068-4d6c-8a95-370df4e683f1 from (pid=31272) get /opt/stack/python-barbicanclient/barbicanclient/v1/secrets.py:457
2018-06-13 14:53:43.865 DEBUG barbicanclient.client [req-7e112240-98ad-4043-9031-a186288838c6 admin admin] Response status 403 from (pid=31272) _check_status_code /opt/stack/python-barbicanclient/barbicanclient/client.py:87
2018-06-13 14:53:43.867 ERROR barbicanclient.client [req-7e112240-98ad-4043-9031-a186288838c6 admin admin] 4xx Client error: Forbidden: Secret retrieval attempt not allowed - please review your user/project privileges
2018-06-13 14:53:43.871 ERROR tacker.api.v1.resource [req-7e112240-98ad-4043-9031-a186288838c6 admin admin] create failed: No details.: HTTPClientError: Forbidden: Secret retrieval attempt not allowed - please review your user/project privileges
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource Traceback (most recent call last):
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/opt/stack/tacker/tacker/api/v1/resource.py", line 77, in resource
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     result = method(request=request, **args)
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/opt/stack/tacker/tacker/api/v1/base.py", line 393, in create
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     obj = obj_creator(request.context, **kwargs)
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/opt/stack/tacker/tacker/vnfm/plugin.py", line 737, in create_vnf_scale
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     self._handle_vnf_scaling(context, policy_)
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/opt/stack/tacker/tacker/vnfm/plugin.py", line 670, in _handle_vnf_scaling
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     infra_driver, vim_auth = self._get_infra_driver(context, vnf)
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/opt/stack/tacker/tacker/vnfm/plugin.py", line 276, in _get_infra_driver
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     vim_res = self.get_vim(context, vnf_info)
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/opt/stack/tacker/tacker/vnfm/plugin.py", line 331, in get_vim
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     region_name)
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/opt/stack/tacker/tacker/vnfm/vim_client.py", line 60, in get_vim
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     vim_auth = self._build_vim_auth(context, vim_info)
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/opt/stack/tacker/tacker/vnfm/vim_client.py", line 79, in _build_vim_auth
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     vim_auth['password'])
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/opt/stack/tacker/tacker/vnfm/vim_client.py", line 118, in _decode_vim_auth
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     vim_key = secret_obj.payload
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/opt/stack/python-barbicanclient/barbicanclient/v1/secrets.py", line 193, in payload
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     self._fetch_payload()
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/opt/stack/python-barbicanclient/barbicanclient/v1/secrets.py", line 261, in _fetch_payload
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     if not self.payload_content_type and not self.content_types:
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/opt/stack/python-barbicanclient/barbicanclient/v1/secrets.py", line 184, in payload_content_type
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     if not self._payload_content_type and self.content_types:
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/opt/stack/python-barbicanclient/barbicanclient/v1/secrets.py", line 34, in wrapper
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     self._fill_lazy_properties()
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/opt/stack/python-barbicanclient/barbicanclient/v1/secrets.py", line 414, in _fill_lazy_properties
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     result = self._api.get(self._secret_ref)
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/opt/stack/python-barbicanclient/barbicanclient/client.py", line 70, in get
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     return super(_HTTPClient, self).get(*args, **kwargs).json()
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/usr/lib/python2.7/site-packages/keystoneauth1/adapter.py", line 304, in get
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     return self.request(url, 'GET', **kwargs)
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/opt/stack/python-barbicanclient/barbicanclient/client.py", line 63, in request
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     self._check_status_code(resp)
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource   File "/opt/stack/python-barbicanclient/barbicanclient/client.py", line 107, in _check_status_code
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource     status
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource HTTPClientError: Forbidden: Secret retrieval attempt not allowed - please review your user/project privileges
2018-06-13 14:53:43.871 TRACE tacker.api.v1.resource 
2018-06-13 14:53:43.877 INFO tacker.wsgi [req-7e112240-98ad-4043-9031-a186288838c6 admin admin] 127.0.0.1 - - [13/Jun/2018 14:53:43] "POST /v1.0/vnfs/e2718c39-8079-42c0-9b18-18400eb11067/actions.json HTTP/1.1" 500 367 0.559095



