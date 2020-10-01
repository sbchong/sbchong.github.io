# CentOS 7使用yum安装Nginx

## 前言

今天重装了一台服务器的系统，因为之前没有备份快照，所以之前的所有应用和数据都没有了，可谓是真（一）的（个）勇（憨）士（憨）。

## 前置说明

首先，我们先确定自己的系统版本号。安装系统的时候，服务商一般都会直接让我们自己选择需要安装的系统，那如果你~~憨憨地~~不小心地忘记了系统版本号，那随意安装可能会有兼容问题。比如我的就是CentOS  7.7.1908 

```bash
$ cat /etc/redhat-release
CentOS Linux release 7.7.1908 (Core)
```

介绍一下Nginx（读作engine X），这是一个轻量级，高性能的HTTP服务器应用，简单配置即可伺服本地文件或者用作反向代理，同时也是一个IMAP/POP3/SMTP服务器（当然，我没试过）。

## 安装步骤

### 添加yum源

在CentOS默认的yum源中，并没有包含Nginx，需要我们自己添加，可以使用epel或者官网给的yum源。这里使用官方提供yum源。[可以点击这里查看官方目录](http://nginx.org/packages/centos/7/noarch/RPMS/)

```bash
$ sudo rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
```

该命令即可在yum源中添加Nginx源，查看源，我们可以发现，nginx repo已经有了。

```bash
$ sudo yum repolist
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: asi-fs-n.contabo.net
 * extras: centos.schlundtech.de
 * updates: asi-fs-n.contabo.net
repo id                    repo name                    status
!base/7/x86_64             CentOS-7-Base                10,097
!extras/7/x86_64           CentOS-7 - Extras            335
!nginx/x86_64              nginx repo                   172
!packages-microsoft-com-mssql-server-2019                packages-microsoft-com-mssql-server-2019                     32
!updates/7/x86_64          CentOS-7-Updates             1,487
repolist: 12,123
```

### 安装Nginx

既然配置了yum源，那就可以使用yum命令安装Nginx。

```bash
$ sudo yum install nginx -y
```

### 配置Nginx

使用systemctl来对Nginx进行配置。

设置开机启动

```bash
$ sudo systemctl enable nginx
```

启动服务

```bash
$ sudo systemctl start nginx
```

停止服务

```bash
$ sudo systemctl stop nginx
```

重启服务

```bash
$ sudo systemctl restart nginx
```

重新加载服务

```bash
$ sudo systemctl reload nginx
```

### 配置防火墙

系统防火墙默认是关闭所有端口的，首先我们要设置防火墙规则。我的系统防火墙使用的是firewalld，这里有两个方法，一个是开放单独端口，每新加一个站就要设置一次。还有一种就是直接添加服务规则。

先介绍开启单独端口，以80端口为例。

```bash
$ sudo firewall-cmd --zone=public --add-port=80/tcp --permanent
success
$ sudo firewall-cmd --reload
success
$ sudo firewall-cmd --zone=public --list-port
80/tcp
```

接下来是以服务添加的规则。

```bash
$ sudo firewall-cmd --zone=public --add-service=http --permanent 
success
$ sudo firewall-cmd --reload
success
$ sudo firewall-cmd --list-service
ssh dhcpv6-client http
```

之后，在浏览器中直接访问IP应该就可以看到Nginx的默认页面啦。