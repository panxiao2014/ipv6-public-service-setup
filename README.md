# 概述

最近在家中搭建了一个文件服务器，之后想要试着从外网访问，才发现目前搭建公网服务的条件不是很好满足。打电话给电信客服，了解到获取公网IPv4地址的两种方式：

- 升级到399套餐，可以拿到公网IPv4地址
- 申请静态IPv4地址，月租费3000

这两种方案太花钱，当然是不能采纳了。幸好客服补充了一句，现在用户终端都是有IPv6公网地址的。从这句话出发，经过一阵折腾，终于实现了外网访问内网服务。由于涉及的点比较多，也踩了一些坑，接下来将把搭建网络的全过程做一个归纳总结。

# 组网方式和IP路由流程

方案的组网方式如下：


光猫需要接软路由R2S，R2S内网接入家里原来的Wi-Fi路由器。家里所有需要接入网络的设备配置不需要做任何改变。

软路由的LAN地址为`192.168.100.1`，它给Wi-Fi路由器分配的地址为`192.168.100.112`。内网的文件服务器地址为`192.168.0.114：5050`。

当外网要访问这个服务器时，流程为：

外网用户使用域名加端口号访问服务 --> 域名服务器返回R2S软路由WAN口的IPv6地址 --> 外网用户访问此地址 --> 软路由将IPv6地址转化为IPv4地址，转发到Wi-Fi路由器 --> Wi-Fi路由器再转发报文到服务器。

采用这个方案的原因是：既利用了电信的公网IPv6资源，同时又保持了家里设备的IPv4地址设置不变。另外一个方案是家里的设备都获取IPv6地址，软路由不再做v6到v4地址的转换。但是我个人还是比较喜欢前一种方案，因为还是想家里设备尽量使用IPv4地址。

接下来对每个步骤做详细说明。

# IPv6地址配置

首先检查R2S软路由的WAN口设置，如果是直接获取IP地址，那么说明拨号实在光猫上进行的，IPv6地址并没有分配到路由器上。这时可以直接给电信客服打电话，要求把光猫改成桥接模式。这个会立即生效，之后重启软路由。重新配置WAN口，改为拨号上网方式，填上电信开网时提供的用户名和密码。上网成功后，软路由的web管理界面不一定会显示WAN口的IPv6地址，这时可以ssh到软路由上，使用以下命令查看：

如果看到这个，就表示软路由上已经有公网IPv6地址了。Wi-Fi路由上，不需要做任何地址配置修改。只是需要检查一下WAN口配置，设置为自动获取IP地址即可。


