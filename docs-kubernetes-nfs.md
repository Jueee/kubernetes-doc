### 安装 nfs 

1、主机安装 nfs服务器

```
sudo apt-get install nfs-kernel-server
sudo apt-get install nfs-common
```

2、主机共享NFS文件夹

编辑  /etc/exports 文件

sudo vi /etc/exports
在文件末尾添加（nfs共享目录，它可以作为开发板的根文件系统通过nfs挂载。）：

```
/home/vmuser/Share                *(rw,sync,no_root_squash)
```

修改完成后，保存。 

该目录必须存在
然后启动或重启NFS服务。

在终端中执行如下命令，可以启动 NFS 服务：

```
$ sudo /etc/init.d/nfs-kernel-server start
```

执行如下命令则可以重新启动 NFS 服务：

```
$ sudo /etc/init.d/nfs-kernel-server restart
```

3、其他设备挂载NFS服务器

```
$ sudo mount -t nfs 192.168.12.123:/home/vmuser/Share    /mnt -o nolock  
```



参考资料：

> http://www.voidcn.com/article/p-cizdierd-brp.html
>

> https://www.cnblogs.com/openusb/archive/2013/09/09/3309136.html
>

> https://www.jianshu.com/p/af79350a6ddb