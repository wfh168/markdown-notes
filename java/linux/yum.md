新建yum仓库配置文件

```
vi /etc/yum.repos.d/CentOS-Base.repo
```

点击`i`键进入编辑模式，将以下配置信息复制到配置文件中

```
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the 
# remarked out baseurl= line instead.
#
#
 
[base]
name=CentOS-$releasever - Base - mirrors.aliyun.com
failovermethod=priority
baseurl=https://mirrors.aliyun.com/centos-vault/7.9.2009/os/$basearch/
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/centos-vault/RPM-GPG-KEY-CentOS-7
 
#released updates 
[updates]
name=CentOS-$releasever - Updates - mirrors.aliyun.com
failovermethod=priority
baseurl=https://mirrors.aliyun.com/centos-vault/7.9.2009/updates/$basearch/
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/centos-vault/RPM-GPG-KEY-CentOS-7
 
#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras - mirrors.aliyun.com
failovermethod=priority
baseurl=https://mirrors.aliyun.com/centos-vault/7.9.2009/extras/$basearch/
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/centos-vault/RPM-GPG-KEY-CentOS-7
 
#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever - Plus - mirrors.aliyun.com
failovermethod=priority
baseurl=https://mirrors.aliyun.com/centos-vault/7.9.2009/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://mirrors.aliyun.com/centos-vault/RPM-GPG-KEY-CentOS-7
 
#contrib - packages by Centos Users
[contrib]
name=CentOS-$releasever - Contrib - mirrors.aliyun.com
failovermethod=priority
baseurl=https://mirrors.aliyun.com/centos-vault/7.9.2009/contrib/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://mirrors.aliyun.com/centos-vault/RPM-GPG-KEY-CentOS-7

```

# 创建目录
mkdir -p /etc/docker
复制内容，注意把其中的镜像加速地址改成你自己的

tee /etc/docker/daemon.json <<-'EOF'
{
   "registry-mirrors": [
"https://proxy.1panel.live","https://docker.1panel.top","https://docker.m.daocloud.io","https://docker.1ms.run","https://docker.ketches.cn"
]
}
EOF

# 重新加载配置
systemctl daemon-reload
# 重启Docker
systemctl restart docker
