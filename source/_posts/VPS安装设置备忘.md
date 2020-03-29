---
title: VPS安装设置备忘
date: 2020-03-29 20:25:12

---

> 搬瓦工的VPS确实不错，我是17年买的18.99刀一年的，现在已经没有这个套餐了。系统选用的是Centos 7，已完成的软件和设置是 「私钥登录 + v2ray代理 +  + 锐速加速 + Nginx + https」。做此篇备忘，防止以后再设置VPS的时候到处翻找教程。

### VPS配置如图

本来还担心512M RAM安装Centos 7不够用，目前来看还是不用太担心的。

![vps](https://i.loli.net/2020/03/29/cjf8SFQw5WXaLEk.png)

### 1、ssh私钥登录

### 2、安装v2ray代理

### 3、启用锐速加速

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

### 4、安装配置Nginx

- 安装Nginx

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

  启动服务

  ```shell
  sudo systemctl start nginx
  ```

  停止服务

  ```shell
  sudo systemctl restart nginx
  ```

  重新加载

  ```shell
  sudo systemctl reload nginx
  ```

- 配置Nginx

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

### 5、申请证书+https访问

- 安装acme.sh，安装后请 **重启终端** 使用

  ```shell
  curl  https://get.acme.sh | sh
  ```

- 生成证书

  我使用的是阿里云的域名，生成主域名+泛域名证书，其他生成方式见 [acem.sh文档](https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E)

  ```shell
  export Ali_Key="Ali_Key"
  export Ali_Secret="Ali_Secret"
  acme.sh --issue --dns dns_ali -d example.com -d *.example.cn
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

  目前证书在 60 天以后会自动更新, 你无需任何操作. 今后有可能会缩短这个时间, 不过都是自动的, 你不用关心.

- 配置https访问

  <a href="###4、安装配置Nginx">参考Nginx配置</a>

  

