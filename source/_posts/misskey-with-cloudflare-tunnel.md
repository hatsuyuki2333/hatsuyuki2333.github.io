---
abbrlink: cftunnel-misskey
categories:
- - 2023年8月
date: '2023-08-12T20:00:35.310976+08:00'
tags:
- misskey
- cloudflare
- linux
- armbian
title: 使用Cloudflare Tunnel部署公网misskey实例的一些心得
updated: 2023-8-12T20:0:37.861+8:0
---
> 嗯，好久没更新了……

先说说Cloudflare Tunnel吧，这东西其实就是一个FRP，但是因为Cloudflare大厂所以**完全免费**。注册一个Cloudflare账号很简单，然后你需要一个属于自己的域名并过渡到Cloudflare管理（控制台底下有说明，在注册商改NS（Name Server，域名服务器）就行了。

在控制台主页右边栏能找到Zero Trust，进入之后在右边栏翻一翻就能找到Tunnel了。新建一个Tunnel，跟随提示在你的服务器上安装对应的Cloudflared安装包，并且注册组件，再在控制台上填入需要映射的端口协议类型和端口号以及映射到的域名就行了。本次部署的是misskey，所以协议选择`HTTP`，端口填写`localhost:3000`即可。

**使用Cloudflare Tunnel之后无需设置Nginx代理！！**

其次，在进行`NODE_ENV=production pnpm install --frozen-lockfile`时，家里网络环境不好的可能会用NPMMIRROR镜像，但是这个镜像不够新，很多依赖会404，这时候就记下依赖包名，然后去NPMMIRROR手动镜像一下。

然后是个人遇到的一点问题，运行`pnpm run init`报错，是数据库权限问题。具体参考[这个issue](https://github.com/misskey-dev/misskey/issues/11485)。

嗯。大概就这样。

[欢迎来玩！](https://yukiverse.icu)
