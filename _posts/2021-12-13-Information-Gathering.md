---
layout: post
title:  "information-Gathering"
date:   2021-12-13 17:16:00 +0800
tags: 
color: rgb(198,168,153)
cover: '../assets/papercover/cute3.png'
subtitle: 'information-Gathering by yako33!'
---

[TOC]

## google语法

也许你想在bash/python中继承一些搜索苦于寻找关键词写法，本文或许可以帮你。

#### 网站后缀
```
inurl:login|admin|manage|member|admin_login|login_admin|system|login|user|main|cms
```
#### 文本内容
```
site:域名 intext:管理|后台|登陆|用户名|密码|验证码|系统|帐号|admin|login|sys|managetem|password|username
```
#### 可注入点
```
site:域名 inurl:aspx|jsp|php|asp
```
#### 上传漏洞
```
site:域名 inurl:file|load|editor|Files
```
#### eweb编辑器
```
site:域名 inurl:ewebeditor|editor|uploadfile|eweb|edit
```
#### 存在的数据库
```
site:域名 filetype:mdb|asp|#
```
#### 查看脚本类型
```
site:域名 filetype:asp/aspx/php/jsp
```
#### 迂回策略入侵
```
inurl:cms/data/templates/images/index/
```
#### exploit-db

可以直接使用[exploit-db](https://www.exploit-db.com/)中的GHDB(Google Hacking Database)

<ul>
<li  markdown="1" style="list-style-type: none;">
![1]({{site.url}}/screenshot/20211213InformationGathering/1.png)
</li>
</ul>

## shodan

#### CVE检测

```
初始化： shodan init  <api key> 

shodan init aaaa
```



```
#shellcode指纹
shodan search --limit 10 --fields ip_str,port '"\x03\x00\x00\x0b\x06\xd0\x00\x00\x124\x00"'

shodan search --limit 10 --fields ip_str,port "\x03\x00\x00\x0b\x06\xd0\x00\x00\x124\x00" country:jp

shodan search --limit 10 --fields ip_str,port vuln:cve-2019-0708

#保存json文件
shodan download filename --limit 10 '"\x03\x00\x00\x0b\x06\xd0\x00\x00\x124\x00"'

#解析json文件
shodan parse --fields ip_str filename.json.gz > 0708.txt

#msf,加载文件的方式批量扫描ip地址
set rhost file://0708.txt
set targets
```

#### VNC空密码

```
shodan count '"\x03\x00\x00\x0b\x06\xd0\x00\x00\x124\x00"'

shodan honeyscore ip

shodan host ip --history

#端口未授权
shodan search --limit 10 --fields ip_str "authentication disabled" port:5900
```

#### 搜索路由器

```
#查看本地的出口ip
shodan myip

#查看被黑的网站
shodan search --limit 10 --fields ip_str,port http.title:hacked by
shodan search --limit 10 --fields ip_str,port http.title:hacked by country:cn
shodan search --limit 10 --fields ip_str,port http.title:hacked by country:jp

city:chengdu

shodan search --limit 10 --fields ip_str,port http.title:yako country:cn

#思科
shodan search --limit 10 --fields ip_str,port "cisco -authorized" port:23
#爆破
+hydra
```

#### 搜索组件

```
#google搜索网段,美国国家安全局IP
nsa ip address range

shodan search --limit 10 --fields ip_str,port net:208.88.84.0/24

org:baidu
hostname:google
http.waf:Safedog

#ip信息收集
shodan host ip

#html Login页面收集
http.html:login

#mongodb:
shodan search  --limit 10 --fields ip_str "MongoDB Server information -authentication" port:27017 

#myphpadmin 3306  redis

#Jenkins:
"X-Jenkins" "Set-Cookie:JSESSIONID" http.title:"Dashboard"
```

#### Moniter 和图形界面

网页搜索与命令搜索相同，不过可以看清地域分布和计数

```
webcam
```

#### **Monitor Network**

单一目标针对搜索，shodan非实时性

扫描结果可以连接email / TG

```
product:Apache httpd
org:Tencent cloud
```

#### teamserver

```
http.component:jQuery bootstrap
has_screenshot:true encrypted attention
industrial control systems ics
screenshot.label:ics

shodan stats --facets ssl.version country:cn has_ssl:true HTTP
shodan scan submit ip


port 50050 country:cn 
product "cobalt strike team server"
```
