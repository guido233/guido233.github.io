# 申明
**本教程仅供学习、研究和技术交流使用，请勿将其用于任何非法活动、商业用途或违反相关法律法规的行为。使用者应对自己的行为负责，确保操作符合当地法律和道德规范。作者和本教程的相关方不承担因使用本教程而产生的任何直接或间接责任。**

[domain]\: 在域名网站购买的域名，下面遇到这个请用自己申请的域名替代。
# 适用人群
**掌握基本的 Linux 服务器操作**
**适用于已经具备一定的网络环境但是网络环境不稳定的同学**

# 网络环境准备
本教程采取的措施是Cloudflare+WebSocket+TLS+Web
## 云服务器网站注册和云服务器申请
首先，选择一个可靠的云服务提供商，比如 AWS、Azure、Google Cloud 或其他你信赖的平台。不同的服务商提供的功能和价格有所不同，所以根据你的需求选择最适合的方案。本文以AWS平台举例，12个月免费使用Amazon EC2，缺点是需要一张外币信用卡。具体的注册流程在这里就不详述了。
![Pasted image 20240819145623](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819145623.png)
假设我们有能力申请了一台云服务器，进入`EC2 Dashboard`-->`实例`-->`启动新实例`，
![Pasted image 20240819150431](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819150431.png)
然后选择免费的套餐，服务器系统最好选择Ubuntu/Debian，主要因为作者更熟悉这类系统。
![Pasted image 20240819150855](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819150855.png)
### 实例类型
![Pasted image 20240819151118](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819151118.png)
### 密钥对
创建新密钥对，全部选择默认选项，将密钥文件下载到一个安全的位置。
![Pasted image 20240819151203](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819151203.png)

### 安全组
创建安全组，允许SSH/HTTPS/HTTP流量。安全组设置作者有空会更细的讲解。
![Pasted image 20240819151635](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819151635.png)
### 配置存储
直接免费拉满。
![Pasted image 20240819151830](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819151830.png)
ys(yun server)启动!
### 连接云服务器
启动后你的实例中就有一条记录，进入实例，就可以看到你刚刚创建的云服务器的公有IPv4地址[ip]。
![屏幕截图 2024-08-19 153600](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-08-19%20153600.png)
记住这个地址，使用ssh连接你的云服务器。
```bash
ssh [user]@[ip]
# 这里由于是ubuntu系统，默认的user是ubuntu，所以[user]是`ubuntu`
```
用户密钥选择刚刚密钥对保存的文件，然后点击连接进入云服务器。
![Pasted image 20240819152722](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819152722.png)
看到类似这样的界面表示你已经进入了云服务器。
![Pasted image 20240819153310](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819153310.png)

## 购买域名与选择DNS服务器
有很多渠道可以购买域名，作者购买的域名是从[Godaddy](https://www.godaddy.com/)中购买的，我购买的价格差不多是$15/year，可以多家比较一下，选个便宜的，域名地址为[domain]。
![Pasted image 20240819105538](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819105538.png)

购买域名后，找到**域名管理界面**，下图是域名管理界面的侧边栏。
![Pasted image 20240819113320 | 200](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819113320.png)
进入DNS界面，选择`Nameservers`-->`Change Nameservers`-->`I'll use my own namesevers`，将[DNS服务器](#DNS服务器地址)地址填进去（暂时没有DNS服务器没关系，继续阅读，等获得DNS服务器地址后再填写）。完成后这个网站就不需要再使用了，现在你的域名都将会被DNS服务器所解析。
## Cloudflare账号注册和设置DNS服务器
### Cloudflare账号注册
过
### 添加域名
![Pasted image 20240819111310](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819111310.png)
点击`添加站点`，然后将你的域名添加进去，选择`free`计划
![Pasted image 20240819111451](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819111451.png)
##### DNS服务器地址
你会得到`Cloudflare名称服务器`，这个服务器就是DNS服务器。
![Pasted image 20240819111622](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819111622.png)
##### 设置DNS服务器
找到DNS操作界面，然后点击`添加记录`。
![Pasted image 20240819114041](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819114041.png)
需要填写名称和IPv4地址，名称就是你的域名，填写`@`表示你的主域名，也就是你在GoDaddy中申请的域名[domain]，如果你不想使用主域名，可以使用子域名，例如名称中填写test，则表示你代理的域名是test.[domain]。IPv4地址表示这个域名指向的服务器地址，这里填写申请的AWS云服务器的IP。关闭代理状态。
![Pasted image 20240819144010](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819144010.png)
至此，Cloudflare 的任务已经全部完成。
## 云服务器配置
### 1. 域名证书申请

#### 1. 1 安装certbot，用于为域名申请证书
##### 安装snap
```bash
sudo apt update
sudo apt install snapd
```
备注：如果没有sudo权限，切换成root用户进行登录
```bash
su root
apt update
apt install snapd
```
##### 安装cerbot
```bash
sudo snap install --classic certbot
```
##### 建立软链接
```bash
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```
运行完上面的代码就可以使用certbot命令了。
![Pasted image 20240819102111](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819102111.png)
#### 1. 2 使用certbot申请证书流程
```bash
sudo certbot certonly --standalone
```
![Pasted image 20240819102431](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819102431.png)
如果之前没有使用过这个命令，你应该需要先输入自己的邮箱，上面的截图是之前我已经输入过自己的邮箱了，所以直接就输入域名。
##### 证书地址
![Pasted image 20240819104437](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819104437.png)
Certificate is saved at: 后就是**certificate地址**，记住这个地址，后面有用。
Key is saved at: 后就是**certificate_key地址**， 记住这个地址，后面有用。
###### **备注**：在运行这个命令前，需要确保:
* 自己的80端口处于未被占用状态
* 自己的服务器已经绑定了刚刚输入的域名
不然就会报错，下面是运行的报错的信息。
![Pasted image 20240819103005](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819103005.png)
###### 如何查看端口是否被占用
```bash
sudo apt install net-tools
netstat -nltup
```
![Pasted image 20240819103841](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240819103841.png)
0.0.0.0:443中443就是端口信息，如果你没有找到80端口，那么就是未被占用状态。
###### 域名绑定情况
如果ping不通域名，检查一下域名的绑定情况。
##### 证书自动更新
```bash
# 证书自动化更新
crontab -e

# 复制下面的内容
0 0 1 * * /usr/bin/certbot renew --force-renewal
5 0 1 * * /usr/bin/docker restart gost
```
![Pasted image 20240826164220](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240826164220.png)
##### 查看证书状态
```bash
sudo certbot certificates
```
##### 删除证书
```bash
sudo certbot delete --cert-name example.com
```
### 2. 安装nginx
#### 安装并写入网站首页html
```bash
sudo apt install nginx
# 打开防火墙
sudo ufw allow 'Nginx Full'
# 创建网站文件夹
sudo mkdir -p /var/www/[domain]/html
# 写入你的网站首页文件index.html
vim /var/www/[domain]/html/index.html
```
将下面的内容复制到index.html文件中
```html
<html>
    <head>
        <title>Welcome</title>
    </head>
    <body>
        <h1>Success! Your Nginx server is successfully configured. </h1>
<p>This is a sample page.</p>
    </body>
</html>
```
#### 修改网站配置文件
```bash
sudo vim /etc/nginx/sites-available/[domain]
```
将下面的内容复制到你的文件中，用于配置443端口用于连接我们的域名
将我们上面记住的证书地址填入到`ssl_certificate`和`ssl_certificate_key`
将`[random number]`替换成一个随机的数字，这个代表端口号，不要占用常用端口，可以使用比如`12345`
将`[path]`替换成一个路径名称，不需要真实存在这个路径，比如`/ray` ^nginxconfig
```bash
server {
  listen  443 ssl;
  ssl on;
  ssl_certificate       /etc/letsencrypt/live/[domain]/fullchain.pem;
  ssl_certificate_key   /etc/letsencrypt/live/[domain]/privkey.pem;
  ssl_protocols         TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers           HIGH:!aNULL:!MD5;
  server_name           [domain];
  root /var/www/[domain]/html;
  index index.html;

        location [path] { # 与 V2Ray 配置中的 path 保持一致
        proxy_redirect off;
        proxy_pass http://127.0.0.1:[random number];
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $http_host;

        # Show realip in v2ray access.log
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
}
```
接下来，让我们通过在 `sites-enabled` 目录下创建一个符号链接来启用这个服务器块，Nginx 在启动时会在这个目录下读取配置文件：
```bash
sudo ln -s /etc/nginx/sites-available/[domain] /etc/nginx/sites-enabled/
```
接下来，测试一下 Nginx 文件中是否有语法错误。
```bash
sudo nginx -t
# 重启nginx
sudo systemctl restart nginx
```
最终你可以在浏览器中通过域名访问你的网站这一步就成功了。
### 3. 安装V2ray
#### 安装
```bash
# 下载V2ray脚本
curl -O https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh
curl -O https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-dat-release.sh
# 安装V2ray程序
sudo bash install-release.sh
sudo bash install-dat-release.sh
# 配置V2ray
sudo vim /usr/local/etc/v2ray/config.json
```
下面是配置文件，`[random number]`和[[#^nginxconfig| 这里]]保持一致。`uuid`使用[[#生成`uuid`|后面]]生成的替换`[uuid]`，`[path]`使用[[#^nginxconfig|这里]]的path替换，其余配置可以照抄。
```bash
{
  "inbounds": [
    {
      "port": [random number],
      "listen":"127.0.0.1",
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "[uuid]",
            "alterId": 0
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "wsSettings": {
        "path": "[path]"
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}
```
#### 运行V2ray
```bash
sudo systemctl restart nginx
```
#### 生成`uuid`
```bash
cat /proc/sys/kernel/random/uuid
```
![Pasted image 20240826112259](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/Pasted%20image%2020240826112259.png)
### 自启并启动相关服务
```bash
sudo systemctl enable v2ray nginx --now   # 自启并启动 V2ray 和 Nginx 的服务
sudo systemctl status -l v2ray nginx      # 查看 V2ray 和 Nginx 服务的状态
```

## V2ray客户端配置
### Mac
切换美区App Store，下载Shadowrocket。
#### 注册美区苹果ID
进入网址: https://appleid.apple.com/account, 手机号可以填写国内手机号，重复也没有关系。
#### 配置Shadowrocket
新建一个节点，类型选择Vmess，填写[[#连接云服务器|地址]]、[[#^nginxconfig|端口]]、[[#生成`uuid`|uuid]]；传输方式选择websocket，填写[[#购买域名与选择DNS服务器|host]]和[[#^nginxconfig|路径]]；开启tls。（host就是你的域名）
![image.png](https://c2bfd01762cfbe4e34cc9-1313978990.cos.ap-nanjing.myqcloud.com/Pictures/20250122180437.png)

### Windows
以后有机会再更新

# Problem
使用的时候遇到了问题，使用ping可以正常ping通网站，但是没有网络，怀疑原因可能是https证书过期了，修复流程是更新证书，然后重启nginx和v2ray服务。（也可能是服务运行出现了问题，和证书无关，因为没有单独验证是否是证书的原因）

# Reference

Eric. V2Ray (WebSocket + TLS + Web + Cloudflare) 手动配置详细说明. _Eric_ [https://ericclose.github.io/V2Ray-TLS-WebSocket-Nginx-with-Cloudflare.html](https://ericclose.github.io/V2Ray-TLS-WebSocket-Nginx-with-Cloudflare.html) (2021).

V2Ray的WSS配置 - Anand’s Blog. [https://anandzhang.com/posts/ivy/1](https://anandzhang.com/posts/ivy/1).

WebSocket + TLS + Web | 新 V2Ray 白话文指南. [https://guide.v2fly.org/advanced/wss_and_web.html#%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%85%8D%E7%BD%AE](https://guide.v2fly.org/advanced/wss_and_web.html#%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%85%8D%E7%BD%AE).

Jinkang_Wei. 搭建免费的梯子. _Jinkang_ [https://weijinkang.github.io/2023/05/15/enjoy-free-internet/index.html](https://weijinkang.github.io/2023/05/15/enjoy-free-internet/index.html) (2023).

Chen, H. haoel/haoel.github.io. (2025).
