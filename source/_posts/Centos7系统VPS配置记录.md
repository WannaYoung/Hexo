---
title: Centos7系统VPS配置记录
categories:
  - 技巧
tags:
  - VPS
abbrlink: 8f1d
date: 2020-03-29 20:25:12
---

> 搬瓦工的VPS确实不错，我是17年买的18.99刀一年的，现在已经没有这个套餐了。系统选用的是Centos 7，已完成的软件和设置是 「私钥ssh + V2Ray + 锐速 + Nginx + Https」。做此篇备忘，防止以后再设置VPS的时候到处翻找教程。

### VPS配置如图

本来还担心512M RAM安装Centos 7不够用，目前来看还是不用太担心的。

![vps](https://i.loli.net/2020/03/29/cjf8SFQw5WXaLEk.png)

### 1、ssh私钥登录

- 生成公私钥

  ```shell
  ssh-keygen -t rsa
  ```

  会提示公私钥保存路径和设置私钥密码，路径保持默认即可，生成的公私钥保存在 **/root/.ssh**，id_rsa.pub 为公钥，id_rsa 为私钥。

- 安装公钥

  复制公钥内容到/root/.ssh/authorized_keys，修改 .ssh文件夹权限，修改 authorized_keys文件权限。

  ```shell
  cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
  chmod 600  /root/.ssh/authorized_keys
  chmod 700  /root/.ssh
  ```

- 修改SSH配置

  编辑修改 **/etc/ssh/sshd_config** 文件

  ```nginx
  RSAAuthentication yes # 开启密钥登入的认证方式
  PubkeyAuthentication yes # 开启密钥登入的认证方式
  PermitRootLogin yes #root用户能否通过ssh登录
  ```

  重启ssh服务

  ```shell
  service sshd restart
  ```

  保存私钥到本地，在ssh客户端使用私钥登录。

### 2、安装V2Ray代理

- 使用V2Ray一键安装脚本

  ```shell
  bash <(curl -s -L https://git.io/v2ray.sh)
  ```

- 具体介绍和配置请参考 [V2Ray官网](https://www.v2ray.com/) 和 [V2Ray一键安装脚本](https://github.com/233boy/v2ray/wiki/V2Ray%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85%E8%84%9A%E6%9C%AC)

### 3、启用锐速

- 内核降级

  ```shell
  wget --no-check-certificate -O rskernel.sh https://raw.githubusercontent.com/uxh/shadowsocks_bash/master/rskernel.sh && bash rskernel.sh
  ```

- 安装锐速

  ```shell
  yum install net-tools -y && wget --no-check-certificate -O appex.sh https://raw.githubusercontent.com/0oVicero0/serverSpeeder_Install/master/appex.sh && bash appex.sh install
  ```

- 常用命令

  ```shell
  #查看运行状态
  /appex/bin/serverSpeeder.sh status
  #启动锐速
  /appex/bin/serverSpeeder.sh start
  #停止锐速
  /appex/bin/serverSpeeder.sh stop
  #重启锐速
  /appex/bin/serverSpeeder.sh restart
  #卸载锐速
  /appex/bin/serverSpeeder.sh uninstall
  ```

### 4、安装配置 Nginx

- 安装 Nginx

  添加yum源

  ```shell
  sudo rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
  ```

  安装 Nginx

  ```shell
  sudo yum install nginx
  ```

  设置开机启动

  ```shell
  sudo systemctl enable nginx
  ```

  常用命令

  ```shell
  sudo systemctl start nginx  #启动服务
  sudo systemctl restart nginx  #重启服务
sudo systemctl reload nginx  ##重载服务
  ```

- 配置 Nginx

  ```nginx
  server {
      listen 80;
      server_name example.com www.example.com;
      rewrite ^(.*) https://$server_name$1 permanent;
  }
  server {
      listen 443 ssl;
      server_name  example.com www.example.com;
      ssl_certificate  /etc/nginx/ssl/fullchain.cer;
      ssl_certificate_key  /etc/nginx/ssl/example.com.key;
      ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4:!DH:!DHE;
      ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
      ssl_prefer_server_ciphers on;
      location / {
          root /usr/share/nginx/home;
          index index.html index.htm;
      }
  }
  
  server {
      listen 80;
      server_name blog.example.com;
      rewrite ^(.*) https://$server_name$1 permanent;
  }
  server {
      listen 443 ssl;
      server_name  blog.example.com;
      ssl_certificate  /etc/nginx/ssl/fullchain.cer;
      ssl_certificate_key  /etc/nginx/ssl/example.com.key;
      ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4:!DH:!DHE;
      ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
      ssl_prefer_server_ciphers on;
      location / {
          root /usr/share/nginx/blog;
          index index.html index.htm;
      }
  }
  ```

### 5、申请证书打开Https

- 安装acme.sh，安装后请 **重启终端** 使用

  ```shell
  curl  https://get.acme.sh | sh
  ```

- 生成证书

  我使用的是阿里云的域名，生成主域名+泛域名证书，其他生成方式见 [acem.sh文档](https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E)

  ```shell
  export Ali_Key="Ali_Key"
  export Ali_Secret="Ali_Secret"
  acme.sh --issue --dns dns_ali -d example.com -d *.example.com
  ```

- 安装证书

  我使用的nginx安装证书，其他安装方式见 [acem.sh文档](https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E)

  ```shell
  acme.sh --installcert -d example.com -d *.example.com \
  --key-file       /etc/nginx/ssl/example.com.key  \
  --fullchain-file /etc/nginx/ssl/fullchain.cer \
  --reloadcmd     "service nginx force-reload"
  ```

- 更新证书

  目前证书在 60 天以后会自动更新, 无需任何操作。

- 配置https访问

  > 参考第4步: Nginx配置