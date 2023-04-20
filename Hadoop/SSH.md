SSH配置（必需
-=-=-=-=-=--=-=-=-=-=-=-服务器端-=-=-=-=-=-=-=-=-=-=-
*安装SSH服务
	# 在Ubuntu, Debian Linux中使用
	a) apt-get update
	   apt-get install ssh/openssh
	# 在Fedora, CentOS Linux中使用
	b) yum update
	   yum install openssh-server
-=-=-=-=-=--=-=-=-=-=-=-服务器端-=-=-=-=-=-=-=-=-=-=-
*启动SSH服务
	# 在Ubuntu, Debian Linux中使用
	a) service sshd start
	-或者-
	# 在Fedora, CentOS Linux中使用
	b) systemctl start sshd

*查看本地是否开启SSH端口(默认:22)
	lsof -i:22

*允许客户端以root身份连接SSH服务
	1）修改/etc/ssh/sshd_config配置文件
		# 允许root使用ssh登录到本地
		PermitRootLogin yes
		# 允许使用公钥验证登录用户
		PubkeyAuthentication yes
	2) 重启SSH服务
		# 在Ubuntu, Debian Linux中使用
		a1) service sshd stop
		a2) service sshd start
		-或者-
		# 在Fedora, CentOS Linux中使用
		b1) systemctl stop sshd
		b2) systemctl start sshd

-=-=-=-=-=--=-=-=-=-=-=-客户端-=-=-=-=-=-=-=-=-=-=-
*SSH客户端登录远端SSH服务
	ssh <用户名>@<远端服务器 ip or hostname>

-=-=-=-=-=--=-=—多虚拟机-=-=-=-=-=-=-=-=-
*SSH客户端生成本地SSH秘钥
	ssh-keygen

*客户端公钥Copy到远端SSH服务器上
	ssh-copy-id <用户名>@<远端服务器 ip or hostname>

-=-=-=-=-=--=-=—单虚拟机-=-=-=-=-=-=-=-=-
*设置本地SSH免密登录
	# 客户端生成秘钥对（私钥，公钥[后缀.pub]）
	ssh-keygen
	# 添加公钥到服务器端验证秘钥文件中
    cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

-=-=-=-=-=--=-=—设置服务器端文件权限-=-=-=-=-=-=-=-=-
	# 设置登录服务器公钥文件权限
    chmod 0600 ~/.ssh/authorized_keys
    # 设置当前用户ssh目录的所属者完全权限
    chmod 700 ~/.ssh
    # 设置当前用户主目录的所属组和其他成员为不允许写权限
    chmod go-w ~/


-=-=-=-=-=--=-=—ssh客户端调试信息-=-=-=-=-=-=-=-=-
	# 客户端使用调试模式连接ssh服务(debug信息中包含提交秘钥对验证过程)
	ssh -vvv <user>@<ssh_server_ip>
	# 服务器端查看ssh登录日志(如果客户端登录账户被锁定,可使用passwd -u <user>)
	tail /var/log/secure

### Windows免密登录Linux
将Window中~/.ssh/id_rsa.pub的内容
追加到Linux下vi ~/.ssh/authorized_keys里面