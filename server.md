## *离线服务器配置（基于HR2288 V3 + CentOS 7 + Python + Mysql）* 
* 华为HR2288 V3服务器CentOS 7 系统安装
> 在华为HR2288 V3服务器上安装系统时如果不注意往往会遇到安装磁盘未找到的问题，这种情况则需要考虑Raid是否完成了配置，但Raid配置会因服务器型号的不同而有所差异，所以需要查看官网上相关配置指南。如：[华为V2&V3服务器 RAID 控制卡 用户指南](http://support.huawei.com/enterprise/zh/doc/DOC1000004345)
* CentOS 7下Mysql 数据库离线安装
> 1. 下载正确的安装包版本：[Red Hat Enterprise Linux 7 / Oracle Linux 7](https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.20-1.el7.x86_64.rpm-bundle.tar)；
> 2. 安装Mysql前需要删除CentOS 7中自带的MariaDB:
>    * 查看已安装MariaDB相关的包：
>      * `rpm -qa | grep mariadb`
>    * 进行卸载：
>      * `rpm -e --nodeps mariadb-libs-5.5.44-1.el7_1.x86_64`
> 3. 正确安装Mysql：
>    * 解压安装包： 
>      * `tar -zxvf mysql-5.7.20-1.el7.x86_64.rpm-bundle.tar` 
>    * 进入Mysql解压文件进行以下安装：
>      * `rpm -ivh mysql-community-common-5.7.20-1.el7.x86_64.rpm`
>      * `rpm -ivh mysql-community-libs-5.7.20-1.el7.x86_64.rpm`
>      * `rpm -ivh mysql-community-libs-compat-5.7.20-1.el7.x86_64.rpm`
>      * `rpm -ivh mysql-community-client-5.20.17-1.el7.x86_64.rpm`
>      * `rpm -ivh mysql-community-server-5.20.17-1.el7.x86_64.rpm`
>    * 启动、停止、重启、查看Mysql：
>      * `systemctl {start|stop|restart|status} mysqld.service`
>    * 查看初始的随机密码：
>      * `cat /var/log/mysqld/log | more`
>    * 登录mysql：
>      * `mysql -u root -p`
>    * Mysql登录密码难度修改：
>      * 查看Mysql的密码安全策略： 
>        * `mysql> show variables like '%password%';`
>      * 进行参数修改：
>        * `mysql> set global validate_password_policy=0;`
>        * `mysql> set global validate_password_special_char_count=0;`
>        * `mysql> set global validate_password_number_count=0;`
>        * `mysql> set global validate_password_mixed_case_count=0;`
>        * `mysql> set global validate_password_length=0;`
>    * 重置Mysql登录密码：
>      * `mysql> use mysql`
>      * `mysql> update user set authentication_string = password('123456') where user = 'root';`
>      * 参考资料： [简书](http://www.jianshu.com/p/5cad576e5270)
* python项目依赖包离线安装
> 使用pip wheel实现Python依赖包的离线安装
>    * 首先，在开发机器上安装wheel：
>      * `pip install wheel`
>    * 创建requirements.txt文档，把所需依赖包列入其中：
>       * `touch requirements.txt`
>    * 接下来，下载依赖包的wheel文件：
>       * `pip wheel -r requirements.txt`
>    * 默认情况下，上述命令会下载requirments.txt中每个包的wheel包到当前目录的wheelhouse文件夹，包括依赖的依赖。此时可以把这个wheelhouse文件夹打包到你的安装包中。在你的安装脚本中执行：
>       * `pip install --use-wheel --no-index --find-links=wheelhouse -r requirments.txt`
>    * 之后，在离线机器上安装pip：
>       * `tar -zxvf pip-9.0.1.tar.gz`
>       * `cd pip-9.0.1`
>       * `python setup.py install`
>    * 然后在离线机器上通过pip指令安装已下载好的项目依赖包, 例如：
>       * `pip install wheel-0.30.0-py2.py3-none-any.whl`
* 项目部署
> 1. 在完成以上准备后，把项目工程代码拷贝到离线机器上，然后启动相关服务，如果开启的服务需要在其他地方可以正常访问，则需要通过firewall设置，开放相应服务端口：
>    * `firewall-cmd --zone=public --add-port=80/tcp --permanent`
> 2. 重启防火墙：
>    * `systemctl restart firewalld.service`
