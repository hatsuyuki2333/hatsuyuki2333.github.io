---
title: Hexo-GithubPages部署指北
date: 2022-10-05 06:58:58
tags: [hexo,github pages,blog]
description: 在Github Pages上建立你的Blog！   
categories: 2022年12月
---

# Hexo-GithubPages部署指北

> Tip:静态博客的核心在于写作的物理机。所以，在进行下面任意一步时，确保旁边有一台电脑。此外，这篇教程针对的是Windows，对于Linux，以后的文章会写到。

## 1.安装Node.js

Hexo基于Node.js，所以**一切的开始**，你需要安装[Node.js](https://nodejs.org)。
单击16.17.1LTS下载（非LTS也可以，注意不要太久的版本）。  
下载完毕之后，运行程序并安装（一直Next即可）。  
安装完毕，调出cmd或者Powershell，运行`node -v`和`npm -v`指令。若指令返回版本号（而不是错误信息）就说明安装成功了。

```
PS C:\Windows\system32> npm -v
8.15.0
PS C:\Windows\system32> node -v
v16.17.1  
```

## 2.安装Git

打开[Git](https://git-scm.com)首页，点击“Download for Windows”，找到“Other Git for Windows downloads”下的“Standalone Installer”，根据你的系统位数选择32bit/64bit安装包。  S
下载完毕同样下一步下一步即可。完成后右键菜单会多出来“Git Bash Here”和“Git GUI Here”两项。在任意目录选择“Git Bash Here”（或使用PowerShell或CMD），输入`git --version`，返回如下：

```
C:\users\administrator\desktop\1>git --version
git version 2.37.2.windows.2
```

至此Git安装完成。

## 3.安装并部署Hexo

在你想存放博客数据的地方新建一个文件夹，随便什么名字。打开powershell并cd到此目录，输入`npm install hexo-cli -g`  
返回结果大概是这样。

```
PS C:\Windows\system32> npm install hexo-cli -g
added 1 package, and changed 58 packages in 14s    
```

然后输入`hexo init` (如果提示计算机已禁用脚本xxx.ps1，就换cmd并且cd到博客目录）

```
C:\users\administrator\desktop\1>hexo init
INFO  Cloning hexo-starter https://github.com/hexojs/hexo-starter.git
INFO  Install dependencies
INFO  Start blogging with Hexo!    
```

此时Hexo会克隆“hexo-starter”仓库，完成后显示“Start Blogging With Hexo”。  
输入`hexo s`启动本地预览。

```
C:\users\administrator\desktop\1>hexo s
INFO  Validating config
INFO  Start processing
INFO  Hexo is running at http://localhost:4000/ . Press Ctrl+C to stop.
```

至此，你的博客就算是部署完成了！

## 3.个性化博客

> 记得Ctrl-C关掉预览！

### 主题的配置

Hexo的[Themes](https://hexo.io/themes/)页面会对你有所帮助。  
打开主题页面，选择喜爱的主题，把它下载到博客目录下/themes文件夹里，并且把根目录下config.yml中theme改成你主题的文件夹的名字。

```
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: landscape  #修改此处    
```

再次打开本地预览，正常的话，主题应该可以加载。

### 网站标题/副标题/撰写者

在根目录下config.yml中修改。  
提供一个模板，按需取用。

```
# Site
title: Your own title
subtitle: 'it can be empty'
description: 'for better SEO,you can write it if you need'
keywords: also for SEO
author: Your Name
language: zh-CN
timezone: 'Asia/Shanghai'   
```

Title即标题，Subtitle为副标题。Author即显示的作者。语言和时区照抄就OK。  
如果有中文，需要另存为**UTF-8**格式的文件，要不然会乱码。 **所有“:”之后均有一个空格，不要删除！**

## 部署到GitHub Pages

### 配置GitHub账号

在Git Bash中输入 `git config --global user.name "Your GitHub Account Name"`     

`git config --global user.email "Your Github Account's Email"`

### 配置SSH秘钥

`ssh-keygen -t rsa -C "Your Github Account's email"` 后面连敲回车就行
返回大概如下：

```
C:\users\administrator\desktop\1>ssh-keygen -t rsa -C "someone@example.com"
Generating public/private rsa key pair.
Enter file in which to save the key (C:\Users\Administrator/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in C:\Users\Administrator/.ssh/id_rsa.
Your public key has been saved in C:\Users\Administrator/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:jgcBZgPRIGuuVyommF1Q someone@example.com
The key's randomart image is:
+---[RSA 2048]----+
|  oo. o          |
|.E. .  + .       |
|. o              |
|  +              |
|o                |
| .               |
|oo.              |
|                 |
|                 |
+----[SHA256]-----+
```

打开[GitHub](https://github.com)->Settings->SSH & GPG Keys->New SSH key  
打开**C:\Users\Administrator（即你的用户名）.ssh\id_rsa.pub**，把里面所有字符复制粘贴到网页中“Key”文本框中，Title随意填。  
点击“Add SSH Key”即可。
返回Git Bash，输入`ssh git@github.com`  
返回Hi,xxx.You've successfully authenticated........则配置成功。

### 推送Blog

#### 新建存储库（Repository）

打开新建页面     

Repository Name填写 你的Github用户名.github.io    

Description 随便填    

选择Public    

选择Add a README file

点击 Create repository   

就新建完成了。   

完成后切到Repo的settings页面，找到pages选项。设置Deploy from a branch，选择master分支/root目录。

#### 配置Git推送

在仓库页面Code下找到SSH地址（git@github.com:用户名/用户名.github.io.git）复制。   

打开博客目录下config.yml，页面底部照这个填写   

```
deploy:
 type: 'git'
 repo: git@github.com:你的用户名/你的用户名.github.io.git
 branch: master
```

回到GitBash, 输入`npm install hexo-deployer-git` 安装部署插件。   

安装完成后，输入`hexo s`   

部署完成，等待一会就能看到成品（不是）了。
