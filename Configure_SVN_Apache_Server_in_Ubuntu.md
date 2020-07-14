# svn apache服务器配置

## 1、安装apache
```
$ sudo apt-get update
$ sudo apt-get install apache2
```

apache默认配置是80端口，可以在另一台机器输入一下地址，检查apache服务是否正常

```http://your-ip-address```

我碰到connection timeout，然后用:
```
$ service --status-all | grep apache
 [ + ]  apache-htcacheclean
 [ + ]  apache2
```
发现服务器已经起来了

怀疑80端口没有起来，用下面命令检查80端口有没有起来
```
$ sudo netstat -ap | grep 80
```
发现并没有内容输出，想起来服务器好像把80和8080给禁掉了，于是修改了下apache的默认端口:
```
$ sudo vim /etc/apache2/ports.conf
```

将监听端口换成8091：
```
Listen 8091
```

重启apache服务:
```
$ sudo service apache2 restart
```

检查8091端口：
```
$ sudo netstat -ap | grep 8091
tcp6       0      0 [::]:8091               [::]:*                  LISTEN      7696/apache2 
```
发现apache已经在监听8091

再次在另一台机器浏览器上访问
```
http://your-ip-address:8091
```

可以看到apache的home page了，说明服务安装成功

## 2、安装subversion和libapache2-mod-svn
```
$ sudo apt-get install subversion # 如果已经安装svn，此步骤可以跳过
$ sudo apt-get install libapache2-mod-svn
```

## 3、创建svn库
创建svn库的根目录
```
~$ sudo mkdir svn-repo
```
创建名为test的svn库
```
$ sudo svnadmin create ./svn-repo/test
```
可以查看test下面的内容：
```
$ ls ./svn-repo/test
conf  db  format  hooks  locks  README.txt
```

修改test目录的owner和组为www-data，方便apache可以访问：
```
$ sudo chown -R www-data:www-data ./svn-repo
```

## 4、为svn配置一个apache虚拟账户
```
$ sudo vim /etc/apache2/mods-enabled/dav_svn.conf
```
写入以下内容：
```
<Location /svn>
     DAV svn
     SVNParentPath /home/xiaoxiangzi/svn-repo
     AuthType Basic
     AuthName "Subversion Repository"
     AuthUserFile /etc/apache2/dav_svn.passwd
     Require valid-user
</Location>
```
为名为xiaoxiangzi的用户创建密码(需要包含字母数字和特殊符号)
```
$ sudo htpasswd -cm /etc/apache2/dav_svn.passwd xiaoxiangzi
```

重启apache2
```
$ sudo service apache2 restart
```

使用浏览器访问test库
```
http://your-ip-address:8091/svn/test/
```
显示
```
test - Revision 0: /
Powered by Apache Subversion version 1.9.7 (r1800392).
```
配置成功，结束。

如果配到错误信息，可以查看apache2的error log文件，检查问题：
```
/var/log/apache2/error.log
```
