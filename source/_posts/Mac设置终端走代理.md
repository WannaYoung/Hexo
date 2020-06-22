---
title: Mac设置终端走代理
categories:
  - 技巧
tags:
  - Mac
abbrlink: 60247
date: 2019-05-25 17:55:24
---

> Mac终端默认是不走代理的，使用brew、git、cocoapods速度极慢。设置终端走代理后速度明显好转，我用的是V2rayU，最近SS识别率太高了，建议有条件的都换用v2ray。

### V2rayU

多数人使用的是SS的方法实现科学上网，不过最近SS识别率大增，ip很容易被墙，于是我重新搭建了v2ray，客户端使用的是V2rayU。

[v2ray 搭建教程](https://github.com/233boy/v2ray/wiki/V2Ray%E6%90%AD%E5%BB%BA%E8%AF%A6%E7%BB%86%E5%9B%BE%E6%96%87%E6%95%99%E7%A8%8B)

[V2rayU 下载地址](https://github.com/yanue/V2rayU/releases)

### **终端代理设置**

- #### 查看代理端口

  打开代理软件的偏好设置，找到监控本地的默认HTTP端口，V2rayU默认是1087，如果改过默认端口，就使用你修改以后的端口，使用SS同理。

  

  ![v2rayu](https://tva1.sinaimg.cn/large/00831rSTgy1gd8m21ee6ej30pk0c8ack.jpg)

  

- #### 代理设置写入.bash_profile

  编辑 .bash_profile 文件

  ```shell
  vi ~/.bash_profile
  ```

  在文件末尾添加以下代码

  ```javascript
  function proxy_on() {
      export http_proxy=http://127.0.0.1:1087
      export https_proxy=http://127.0.0.1:1087
      echo -e "已开启代理"
  }
  
  function proxy_off() {
      unset http_proxy
      unset https_proxy
      echo -e "已关闭代理"
  }
  ```

- #### 使代理文件生效

  ```shell
  source ~/.bash_profile
  ```

- #### 打开代理

  先查看下当前的 ip 地址

  ```shell
  $ curl -L tool.lu/ip
  当前IP: 183.193.7*.2**
  归属地: 中国 上海 上海
  ```

  开启 proxy,再查看

  ```shell
  $ proxy_on
  已开启代理
  
  $ curl -L tool.lu/ip
  当前IP: 67.216.228.120
  归属地: 美国 美国
  ```

- #### 关闭代理

  ```shell
  $ proxy_off
  已关闭代理
  ```

  