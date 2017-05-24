# dhcpLogScan
从dhcp日志中过滤出ip－mac－SN的对应关系，并存入ETCD。 scan dhcp log , fill to ETCD


## 简介
本项目算是AllLogScan的一个应用，程序从dhcp日志中提取出IP－MAC－设备SN信息，然后写入到ETCD中，方便其它程序取用。


## 配置文件格式
```
| 日志文件名 | 设备描述|报警关键字|邮箱(不起作用)|手机号（不起作用）|
|-----|------|----|----|----|
|message| hellodhcpserver123 | dhcpd:.DHCPACK | yihf@lie.com | 135XXXXXX |


解释：
读取/var/log/message文件， 过滤出包含“dhcpd:.DHCPACK”正则的行，对这行里的信息进行解析并调用函数写入ETCD
```



## dhcp日志举例
```
实践发现会有三种dhcp日志出现， 不符合着三种数据格式的条目会被丢弃

Nov  7 11:28:10 oobmgmt dhcpd: DHCPACK on 172.1.222.183 to 54:9f:35:0a:cd:bb (idrac-9HFMM32) via eth0     #可分13份
Nov  7 11:28:10 oobmgmt dhcpd: DHCPACK on 172.1.222.201 to 44:a8:42:1a:9e:78 (idrac) via eth0             #可分13份
Nov  7 12:21:11 oobmgmt dhcpd: DHCPACK on 172.1.222.212 to 38:d5:47:02:80:40 via eth0                     #可分12份

 ```



## ETCD中存储的数据展示
```
[root@oobmgmt dhcpLogScan]# etcdctl ls /dhcp-oob
/dhcp-oob/172.1.222.183-54:9f:35:0a:cd:bb-idrac-9HFMM32
/dhcp-oob/172.1.222.201-44:a8:42:1a:9e:78-idrac
/dhcp-oob/172.1.222.212-38:d5:47:02:80:40-idrac-null
```



## 主程序中的参数介绍
```
devicefile = '/root/dhcpLogScan/alllogscan.ini'           #配置文件路径
logfile_path = '/var/log/'                                #日志文件的存放位置
alllogscan_path = '/root/dhcpLogScan/'                    #本程序所在的位置
pythonlog =  '/root/mylog.txt'                            #本程序输出的日志

sms_off = 0                                               #没用上
mail_off = 0                                              #没用上
MAX_process = 100                                         #多线程并发数
once_line = 300                                           #每次只过滤日志的最后300行，当日志产生的很快时可以增大这个数

ETCDURL   = "http://127.0.0.1:2379/v2/keys/"             #etcd地址
ETCDKEYS  = "dhcp-oob"                                   #存入ETCD中的目录名
ETCDTTL   = "31536000"                                   #TTL 12 hour，推荐等于 dhcp lease max  time, 如果设置为20170328则为1年 ，为0则为不过期

```


## 开发环境
Python 2.7.5

## 作者介绍
yihongfei  QQ:413999317   MAIL:yihf@liepin.com

CCIE 38649


## 寄语
为网络自动化运维尽绵薄之力，每一个网工都可以成为NetDevOps
