ps：使用gitolite必须使用高版本git

1、先安装依赖包：yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel -y

2、检查当前版本：git --version，系统默认的版本为1.8.3，需要卸载重装，卸载命令：yum remove git

3、# 下载高版本git
wget https://www.kernel.org/pub/software/scm/git/git-2.31.1.tar.gz
 
# 解压
tar xzf git-2.31.1.tar.gz

# 进入源码目录
$ cd git-2.31.1
 
# 安装工具和依赖
$ yum install libcurl-devel
$ yum install expat-devel
$ yum install openssl-devel
 
# 编译
$ make prefix=/usr/local/git all
$ make prefix=/usr/local/git install
 
# 将git路径加入bashrc配置
$ echo "export PATH=$PATH:/usr/local/git/bin" >>/etc/bashrc
 
# 刷新bashrc重启
$ source /etc/bashrc

#验证结果
$ git --version
# git version 2.31.1

# 添加git账户
$ adduser git

# 修改git的密码
$ passwd git
# 然后两次输入git的密码确认后。


#在windows客户端安装好git后，生成公钥
先配置本机git账号name和email使用自己的
$ git config user.name "admin"
$ git config user.email "admin@qq.com"
$ ssh-keygen -t rsa  #一路回车(也可以设置密码)，默认生成密钥位置为C:\Users\Administrator\.ssh目录

把id_rsa.pub上传到服务器，修改文件位置：mv id_rsa.pub /home/git/.ssh/admin.pub


#下面安装gitolite
#使用git用户
$ su git  #切换到git用户
$ cd /home/git  #进入git目录
$ git clone https://github.com/sitaramc/gitolite  #下载gitolite文件

#如果没有/home/git/bin目录，则需要创建
$ mkdir -p /home/git/bin

#安装gitolite到bin
$ /home/git/gitolite/install -to /home/git/bin

#把上传到服务器的 管理员的公钥setup到gitolite中
$ /home/git/bin/gitolite setup -pk /home/git/.ssh/admin.pub

#查看目录，已经创建成功了 # repositories目录下已经有了两个git仓库了。
ls /home/git
# .
# |-- gitolite-admin.git    # 管理配置权限的仓库
# `-- testing.git           # 测试仓库


#到此就完成了gitolite的安装，下面在客户端clone到本机

$ git clone git@服务器IP:gitolite-admin（如果生成的私钥没有密码，则不需要输入密码）

克隆得到的目录结构如下：
# .
# ├── conf                # 配置文件夹
# │   └── gitolite.conf   # 配置权限的文件
# └── keydir              # 客户端的公钥文件夹，所有伙伴的公钥要放到此目录下
#     └── malun.pub

# git clone 克隆报错
bash: git-upload-pack: command not found
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.

需要增加软连接：ln -s /usr/local/git/bin/git-upload-pack /usr/bin/git-upload-pack


#其他用户需要在客户端生成公钥，发送给管理员放到
$ git config user.name "test2"
$ git config user.email "test2@qq.com"
$ ssh-keygen -t rsa -C "test2@qq.com"  #默认生成密钥位置为C:\Users\Administrator\.ssh目录

发送pub给管理员上传到gitolite-admin项目的keydir目录下
注意文件名字格式为test2.pub,test2就是配置权限时的用户名。


# gitolite的权限配置
添加其他开发的小伙伴

把小伙伴的公钥发给管理员。管理员添加到gitolite-admin仓库的keydir目录下,注意文件名字格式为username.pub,username就是配置权限时的用户名。

配置用户对仓库的读写权限

直接修改conf文件夹下的，gitolite.conf文件。简单解释下几个用法：

repo代表仓库的意思，如果新添加一个repo，代表服务端新建一个空仓库，仓库push到服务端后会自动创建。
RW 代表可读可写
@all 代表所有人。
master和 dev代表分支
参考：

#定义用户分组
@admin = malun  
@om = malun bcd   #多个用户以空格隔开
  
repo gitolite-admin  
    RW+     =   malun 
  
repo testing  
    RW+     =   @all  
  
repo om  
    RW+     =   @admin  #分配分组权限
    RW+ master = @admin  
    RW+ dev  =   @om  
应用修改到服务器端

做好配置后，由管理员把修改push到服务器端，会自动处理。

$ git add conf
$ git add keydir
$ git commit -m "added foo, gave access to alice, bob, carol"
$ git push
此时登录服务端，查看/home/git/repositories/目录下是否增加了对应的仓库了呢？