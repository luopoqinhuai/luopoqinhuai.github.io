---
layout: post
comments: true
categories: 工具
---

##Saltstack介绍
Saltstack是一个新的基础设施管理工具。目前处于快速发展阶段，可以看做是强化的Func+弱化的Puppet的组合。间接的反映出了saltstack的两大功能：**远程执行**和**配置管理**。
  
Saltstack使用Python开发的，非常简单易用和轻量级的管理工具。由Master和Minion构成，通过ZeroMQ进行通信。

###安装salt源
`wget  http://dl.cpis-opt.com/huanw/shencan/epel-release-5-4.noarch.rpm && rpm -vih  epel-release-5-4.noarch.rpm`  
或者   
`rpm -ivh http://mirrors.sohu.com/fedora-epel/6/x86_64/epel-release-6-8.noarch.rpm`

###服务端安装salt-master  
`yum install salt-master -y`  

###客户端安装salt-minion
`yum install salt-minion -y`  

###启动服务：
	服务端启动方式：service salt-master start
	客户端启动方式：service salt-minion start
###日志查看路径：（有问题可查日志获取出错信息）
	服务端：/var/log/salt/master
	客户端：/var/log/salt/minion

###服务端master配置

默认情况下，salt master在所有接口(0.0.0.0)上监听4505和4506两个端口. 如果想bind某个具体的IP，需要对/etc/salt/master配置文件中"interface"选项做如下修改:   
```interface: 192.168.1.229```

修改auto_accept为True，自动接受客户端的KEY，当然也可以这里不设置，手动接受就行，接受方式：salt-key -a keyname （keyname即为客户端刚才设置的id标识）  
```auto_accept: True```
 
###客户端minion配置
需要修改minion的配置文件/etc/salt/minion中的master选项，进行如下操作:  
`master: 192.168.1.229`  
	`id :68`

 
###重启以上服务生效
服务端启动方式：service salt-master restart  
客户端启动方式：service salt-minion restart  



###Master与Minion认证
1. minion在第一次启动时，会在/etc/salt/pki/minion/（该路径在/etc/salt/minion里面设置）下自动生成minion.pem(private key)和minion.pub(public key)，然后将minion.pub发送给master。

2. master 在接收到minion的public key后，通过salt-key命令accept minion public key，这样在master的`/etc/salt/pki/master/minions`下的将会存放以minion id命名的public key, 然后master就能对minion发送指令了。
Master与Minion的连接
Saltstack master启动后默认监听4505和4506两个端口。4505为salt的消息发布系统，4506为salt客户端与服务端通信的端口。如果使用lsof查看4505端口，会发现所有的Minion在4505端口持续保持在ESTABLISHED

>[root@51ou.com salt]# lsof -i :4505  
>COMMAND     PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME  
>salt-mast 10843 root   27u  IPv4  53124      0t0  TCP   >192.168.1.229:4505 (LISTEN)  
>salt-mast 10843 root   29u  IPv4  53214      0t0  TCP   >192.168.1.229:4505->192.168.1.68:12183 (ESTABLISHED)  
>salt-mast 10843 root   30u  IPv4  53215      0t0  TCP   >192.168.1.229:4505->192.168.1.230:49306 (ESTABLISHED)  

###KEY管理：
Salt在master和minion数据交换过程中使用AES加密, 为了保证发送给minion的指令不会被篡改，master和minion之间认证采用信任的接受(trusted, accepted )的key.  
在发送命令到minion之前，minion的key需要先被master所接受(accepted). 运行salt-key可以列出当前key的状态  

>[root@51ou.com src]#salt-key -L  
>Accepted Keys:  
>230  
>68  
>Unaccepted Keys:  
>Rejected Keys:  

注：Accepted Keys为被服务端接受的KEY（230，68这二台客户端是被服务端接受了的KEY，其实230，68就是minion中的id标识号）  
Unaccepted Keys:未被服务端接受的KEY  
Rejected Keys：被服务端拒绝的KEY  
salt-key命令可以接受特定的单个key或批量接受key, 使用-A选项接受当前所有的key, 接受单个key可以使用-a keyname.  

认证命令为salt-key，常用的有如下命令：  

>-a ACCEPT, --accept=ACCEPTAccept the following key  
>-A, --accept-all    Accept all pending keys  
>-r REJECT, --reject=REJECTReject the specified public key  
>-R, --reject-all    Reject all pending keys  
>-d DELETE, --delete=DELETEDelete the named key  
>-D, --delete-all    Delete all keys  


###发送指令：
master和minion之间可以通过运行test.ping远程命令判断是否存活

>[root@51ou.com src]# salt -E '230|68' test.ping  
>230:  
>True  
>68:   
>True  

或者对所有minion进行：salt  '*' test.ping
返回True说明测试是OK的，客户端是存活状态
 
###执行命令：  

>salt '68' cmd.run 'df -h'  
>salt -E '230|68' cmd.run 'df -h'

###正则表达式
匹配web-prod和web1-devel minion:  
`salt -E 'web1-(prod|devel)' test.ping`
 
###指定列表
`salt -L 'web1,web2,web3' test.ping`
 
###指定组：
在服务务端中打开master配置文件`vim /etc/salt/master`  
添加如下分组

	nodegroups:
	group1: 'L@230,68'
	group2: '68'
	group3: 'G@os:centos'
	group4: 'G@mem:487'

值得注意的是编辑master的时候，group1和group2前面是2个空格
 
###测试：
	[root@51ou.com salt]#salt -N group2 test.ping
	68:
	True
	[root@51ou.com salt]# salt -N group1 test.ping
	230:
	True
	68:
	True


###Salt命令介绍
 
 
####cmd.run  
Saltstack可以远程执行shell命令，使用cmd.run。如：
	
>salt '68' cmd.run 'df -h'





