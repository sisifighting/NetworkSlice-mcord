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
  
  是tee命令报的错误，解决方向
 
 



