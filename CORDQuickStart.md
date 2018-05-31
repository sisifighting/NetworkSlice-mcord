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


