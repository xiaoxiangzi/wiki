# 问题：

MacOS升级到10.15（Catalina）版本之后，原来php中的zip功能失败，报错如下：

Fatal error: Uncaught Error: Class 'ZipArchive' not found

# 解决方案：

## 1、安装pecl：

下载pear：

$curl -OL http://pear.php.net/go-pear.phar

安装pear：

$sudo php -d detect_unicode=0 go-pear.phar

输入 1，将安装根目录修改为 /usr/local/pear；

输入 4，将命令安装到 /usr/local/bin 目录；

其它选项默认即可，一路回车。

## 2、安装zip

如果系统升级到Big Sur，参考Big Sur的zip安装

$pecl search zip

$sudo pecl install zip

如果显示ERROR: `phpize' failed

使用命令

brew install autoconf

安装autoconf之后，再重新安装zip

如果显示：

checking for libzip... not found
configure: error: Please reinstall the libzip distribution
ERROR: `/private/tmp/pear/install/zip/configure --with-php-config=/usr/bin/php-config' failed

运行：
$brew install libzip

安装libzip，再重新安装zip

如果出现：

/private/tmp/pear/install/zip/php73/php_zip.c:24:10: fatal error: 'php.h' file not found

ERROR: `make' failed

重启系统，按住command+r键进入恢复模式，打开终端，用命令"csrutil disable"关闭SIP，重启电脑

使用下面命令将根目录设置为可读写，否则/usr/读写会失败，本修改重启前有效。
$sudo mount -uw / 

$sudo ln -s "$(xcrun --show-sdk-path)/usr/include" /usr/include

之后再重新安装zip

## 3、配置php.ini

$sudo cp /private/etc/php.ini.default /private/etc/php.ini

$sudo chmod o+w /private/etc/php.ini

将extension=zip.so放到php.ini中的最后一行，保存退出

运行php -m查看扩展，如果已经列表中有zip说明已经安装成功

## Big Sur的zip安装

使用brew安装php

#brew install php

link安装的php库

#brew link php

如果提示/usr/local/bin没有写权限，使用以下命令打开：

#sudo chown -R `whoami`:admin /usr/local/bin

同理，如果碰到其他目录的写权限问题，也可以用上面的命令处理

$pecl search zip

$sudo pecl install zip

注意：如果安装过程中碰到ERROR: failed to mkdir /usr/local/Cellar/php/8.0.2/pecl/20200930

查看pecl符号链接的具体位置

#ls -al /usr/local/Cellar/php/8.0.2/pecl

/usr/local/lib/php/pecl

然后直接mkdir -p /usr/local/lib/php/pecl/20200930

安装好后按照第三部分配置php.ini

参考链接：

http://www.mayanlong.com/archives/2018/pear.html

https://zhile.io/2018/09/26/macOS-10.14-install-sdk-headers.html

http://php-pear.1086190.n5.nabble.com/trouble-with-go-pear-phar-install-td28769.html

https://www.jianshu.com/p/0f1b358b92a5
