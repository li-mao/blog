---
title: IP的一些资料
date: 2021-02-02 10:23:44
tags: [ip]
---
### 中国的IPv4数量  
http://www.cac.gov.cn/wxb_pdf/0228043.pdf 的第5页，"截至 2018 年 12 月，我国 IPv4 地址数量为 33892 万个。"

### 一些聚合网站
http://ip.bczs.net/   
http://ip.yqie.com/

可以查询各国ip数量分布，其中有研究价值的是各省市，比如  
上海IP地址列表 http://ip.yqie.com/cn/shanghai/   
上海市网吧IP地址列表 http://ip.yqie.com/cn/shanghai/wangba.htm   
可以看见各机构申请IP的数量

当前公网IP地址是 116.233.76.62
```text
您的本机 IP：116.233.76.62
所属国家/地区：中国(CN)
路由信息：116.233.0.0/16
区域号码：AS4812
区域名称：CHINANET-SH-AP
描述信息：China Telecom (Group) Shanghai Telecom Company Shanghai China
中国电信（集团） 上海电信公司 上海，中国

IP地址段：116.224.0.0 - 116.239.255.255
IP段名称：CHINANET-SH
IP段描述：CHINANET Shanghai province network China Telecom No.31,jingrong street Beijing 100032

参考数据：116.232.205.0-116.233.89.255 上海市徐汇区 电信
IP数据：116.224.0.0-116.239.255.255 中国 上海 上海
```

### MAC的直连范围

https://juejin.cn/post/6917139283463634951   

MAC 地址的通信范围比较小，局限在一个子网里面。  
例如，从 192.168.0.2/24 访问192.168.0.3/24 是可以用 MAC 地址的。   
一旦跨子网，即从 192.168.0.2/24 到 192.168.1.2/24，MAC地址就不行了，需要 IP 地址起作用了   

### 几个概念

https://juejin.cn/post/6905319321321504775
```text

包:  相当于发送请求的网络包

消息:     应用协议中数据的单位
段:      TCP数据流中的信息
片:      IP中数据单位
帧:      数据链路层中包的单位
```
