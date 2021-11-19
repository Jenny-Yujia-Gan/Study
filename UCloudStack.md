# UCloudStack

## 需求类

### 1.特殊需求

让项目售前提UPro，由产品经理来处理

UPro地址：https://upro.ucloudadmin.com/projects/PCCLPDD

### **2.更改管理地址的域名或者端口**

客户无法自行操作，群里找后端处理

###  

## 虚拟机

### 1.系统盘最大容量

私有云虚拟机系统盘最大支持500G

### 2.创建150G系统盘，为什么进到系统只有40G

镜像大小是40G系统盘，大于40G需要进行扩容操作

系统盘扩容步骤：

[https://docs.ucloud.cn/UCloudStack/userguide/vm?id=_319-%e7%b3%bb%e7%bb%9f%e7%9b%98%e6%89%a9%e5%ae%b9](https://docs.ucloud.cn/UCloudStack/userguide/vm?id=_319-系统盘扩容)

### 3.自制镜像

如果有用该镜像创建的主机的话，自制镜像无法删除，没有虚拟机在用就可以删，客户如果有特殊需求，转给项目售前提产品需求找产品经理评估

### 4.创建虚拟机后状态一直启动中

自制镜像没有安装qemu-guest-agent

参考https://docs.ucloud.cn/UCloudStack_v2.x/customimage/README

![image-20211118183341783](https://raw.githubusercontent.com/Jenny-Gan-Yujia/Image/master/img/image-20211118183341783.png)

主机运行后，需要确保3个agent正常运行

hhga  hhgad node_exporter

 

## 网络

### 1.云平台VPC的内网网段和平台上联的物理交换机的网段不通

云平台VPC的内网网段10.8.2.0/24，物理交换机的网段也是10.8.2.0/24

虚拟机网段和宿主机网段不能相同

vpc 是overlay的网络，和走外网通过物理网络出去的外网underlay是不感知的，没办法打通的

目前只有一个办法，只能把虚拟机做成镜像，再通过镜像重新创建绑新的vpc地址，或者删除重建

### 2.ulb健康检查失败 

node_exporter版本低导致

rs主机node_exporter 版本低导致lb健康检查失败，需要更新node_exporter版本

客户制作镜像时建议使用最新的工具进行初始化

2021-11-18时最新版本为 go1.14.9

![image-20211118183504872](https://raw.githubusercontent.com/Jenny-Gan-Yujia/Image/master/img/image-20211118183504872.png)

v1 版本初始化工具

http://ucloudstack-image.cn-sh2.ufileos.com/v1.x/linux/image_init_new
http://ucloudstack-image.cn-sh2.ufileos.com/v1.x/windows/windows_init_new.exe
http://ucloudstack-image.cn-sh2.ufileos.com/v1.x/windows/windows_virtio.exe

### 3.natgw snat规则一直删除中 

node_exporter服务未正常启动导致

natgw删除规则时会调用主机的node_exporter服务来删除路由，如果natgw所在子网下的主机未启动node_exporter或者更改了node_exporter服务的端口（默认60910端口），会导致natgw删除规则一直删除中

