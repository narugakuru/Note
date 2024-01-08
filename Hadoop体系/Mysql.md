[如何在 Fedora 上安装 MySQL 5.7 34/33/32/31/30 |ComputingForGeeks](https://computingforgeeks.com/how-to-install-mysql-5-7-on-fedora/)


下载仓库源

`sudo dnf -y install https://dev.mysql.com/get/mysql80-community-release-fc37-1.noarch.rpm`

编辑需要下载的版本
`vim /etc/yum.repos.d/mysql-community.repo`

安装
`sudo dnf install mysql-community-server`

启动
`systemctl enable --now mysqld.service`

查看临时密码
`sudo grep 'A temporary password' /var/log/mysqld.log |tail -1`

\#千万不用这个安装！！
！mysql_secure_installation

连接，输入临时密码
`mysql -u root -p`

8.0 修改密码后才可进行其他操作`
`alter user 'root'@'%' identified by '@NOeSIS360724';`

查看密码相关参数；
`show variables like 'validate_password%';

![image.png](https://cdn.jsdelivr.net/gh/narugakuru/images/img/20230224161745.png)
```
validate_password_dictionary_file：用于验证密码强度的字典文件路径；
validate_password_length：密码最小长度，参数默认为8，它有最小值的限定；
validate_password_mixed_case_count：密码至少要包含的大写字母和小写字母的个数；
validate_password_number_count 密码至少要包含的数字个数；
validate_password_policy：密码强度等级检查；（默认是1，即MEDIUM）
validate_password_special_char_count：密码至少要包含的特殊字符个数；
```
