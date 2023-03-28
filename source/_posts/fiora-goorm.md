---
title: Fiora+Goorm IDE部署在线聊天系统
date: 2022-11-08 15:57:27
tags: [node.js,goormIDE,fiora,chatroom]
categories: 2022年11月
description: 倡导言论自由（确信）
---

# Fiora+GoormIDE部署在线聊天系统

> Tip：GoormIDE今年10月停止了对免费用户的Always-On（保持开启）功能，如果你有一个早于10月注册的账号，则会保有Always-On功能，可以参照这篇文章。当然升级计划也可以，IDE配置会提高，还允许绑自己的域名。升级计划之后该文章仍然可用。

[fiora](https://yinxin630.github.io/fiora/zh-Hans/) 是一款有趣的聊天应用. 基于 node.js, MongoDB, react 和 socket.io 等技术开发。该项目起始于2015-11-04。             
[GoormIDE](ide.goorm.io)提供免费的CloudIDE环境，支持Python，Java，PHP，Kotlin，Ruby等多种语言的开发环境。免费IDE提供Docker内Ubuntu18.04/16.04/14.04（root账户），8核心EPYC7742、1GB RAM和10GB存储。     

## 创建容器（Container）

在Goorm主页中选择New Container，输入名称，选择Blank（空白模板），勾选Install MongoDB（安装MongoDB）          
![goorm](/images/fiora-goorm/new.png)
然后点击Create，等待完成。      
出现“Container has been successfully created.Run the generated container XXXX”后，         

- 2022/10之后注册的用户没有Always-On选项，直接点击Run Container         

- 2022/10之前注册/开通了其他计划的用户，刷新页面，点击stop（关闭容器），打开Always-On之后再Run Container。      

## 配置环境

IDE开启后是这样的界面            
![ide](/images/fiora-goorm/ide.png)

### 安装Redis

打开[https://redis.io/topics/quickstart](https://redis.io/topics/quickstart)，选Install redis  on Linux，选择第一种方法安装。       

```
curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/redis.list

sudo apt-get update
```

返回如下：       

```
root@goorm:/workspace/fioratt# curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg

root@goorm:/workspace/fioratt# echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/redis.list
deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb bionic main

root@goorm:/workspace/fioratt# sudo apt-get update
Hit:1 [Index of /ubuntu](http://ap-northeast-2.ec2.archive.ubuntu.com/ubuntu) bionic InRelease
Hit:2 [Index of /ubuntu](http://ap-northeast-2.ec2.archive.ubuntu.com/ubuntu) bionic-updates InRelease
Hit:3 [Index of /ubuntu](http://ap-northeast-2.ec2.archive.ubuntu.com/ubuntu) bionic-backports InRelease
Ign:6 https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.4 InRelease
Get:5 https://cli-assets.heroku.com/apt ./ InRelease [2550 B]
Hit:7 [Index of /ubuntu](http://security.ubuntu.com/ubuntu) bionic-security InRelease
Err:5 https://cli-assets.heroku.com/apt ./ InRelease ease [2679 B]
Err:5 https://cli-assets.heroku.com/apt ./ InRelease ease [2679 B]
Err:5 https://cli-assets.heroku.com/apt ./ InRelease ease [2679 B]
.................................................
Err:5 https://cli-assets.heroku.com/apt ./ InRelease ease [2679 B]
 The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 536F8F1DE80F6A35
Get:11 https://packages.redis.io/deb bionic/main all Packages [2501 B]
Err:4 [https://cf-cli-debian-repo.s3.amazonaws.com](https://cf-cli-debian-repo.s3.amazonaws.com) stable InRelease
 The following signatures were invalid: EXPKEYSIG 172B5989FCD21EF8 CF CLI Team [cf-cli-eng@pivotal.io](mailto:cf-cli-eng@pivotal.io) Get:12 https://packages.redis.io/deb bionic/main amd64 Packages [10.4 kB]
Fetched 22.0 kB in 4s (5172 B/s)
Reading package lists... Done
W: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. GPG error: https://cli-assets.heroku.com/apt ./ InRelease: The following signatures couldn't be
verified because the public key is not available: NO_PUBKEY 536F8F1DE80F6A35
W: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. GPG error: [https://cf-cli-debian-repo.s3.amazonaws.com](https://cf-cli-debian-repo.s3.amazonaws.com) stable InRelease: The following signature
s were invalid: EXPKEYSIG 172B5989FCD21EF8 CF CLI Team <cf-cli-eng@pivotal.io>
```

然后安装Redis:       

```
sudo apt-get install redis
```

```
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
 redis-server redis-tools
Suggested packages:
 ruby-redis
The following NEW packages will be installed:
 redis redis-server redis-tools
0 upgraded, 3 newly installed, 0 to remove and 193 not upgraded.
Need to get 1346 kB of archives.
After this operation, 8381 kB of additional disk space will be used.
Do you want to continue? [Y/n] Y
Get:1 https://packages.redis.io/deb bionic/main amd64 redis-tools amd64 6:7.0.5-1rl1~bionic1 [1222 kB]
Get:2 https://packages.redis.io/deb bionic/main amd64 redis-server amd64 6:7.0.5-1rl1~bionic1 [85.7 kB]
............................
invoke-rc.d: policy-rc.d denied execution of start.
Setting up redis (6:7.0.5-1rl1~bionic1) ...
Processing triggers for systemd (237-3ubuntu10.29) ...
```

返回大概是这样。       

#### 运行Redis服务

```
root@goorm:/workspace/fioratt# redis-server
2086:C 08 Nov 2022 03:21:05.199 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
2086:C 08 Nov 2022 03:21:05.199 # Redis version=7.0.5, bits=64, commit=00000000, modified=0, pid=2086, just started
2086:C 08 Nov 2022 03:21:05.199 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
2086:M 08 Nov 2022 03:21:05.199 * Increased maximum number of open files to 10032 (it was originally set to 1024).
2086:M 08 Nov 2022 03:21:05.199 * monotonic clock: POSIX clock_gettime
 _._
 _.-``__ ''-._
 _.-`` `. `_. ''-._ Redis 7.0.5 (00000000/0) 64 bit
 .-`` .-```. ```\/ _.,_ ''-._
 ( ' , .-` | `, ) Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'| Port: 6379
 | `-._ `._ / _.-' | PID: 2086
 `-._ `-._ `-./ _.-' _.-'
 |`-._`-._ `-.__.-' _.-'_.-'|
 | `-._`-._ _.-'_.-' | https://redis.io
 `-._ `-._`-.__.-'_.-' _.-'
 |`-._`-._ `-.__.-' _.-'_.-'|
 | `-._`-._ _.-'_.-' |
 `-._ `-._`-.__.-'_.-' _.-' `-._ `-.__.-' _.-' `-._ _.-'
 `-.__.-'

2086:M 08 Nov 2022 03:21:05.200 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
2086:M 08 Nov 2022 03:21:05.200 # Server initialized
2086:M 08 Nov 2022 03:21:05.200 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the com
mand 'sysctl vm.overcommit_memory=1' for this to take effect.
2086:M 08 Nov 2022 03:21:05.200 * Ready to accept connections
```

看到Ready to accept connections就说明安装好了，现在刷新Terminal（点击TERMINAL旁边的小箭头），再次运行`redis-server`，会提示`Failed listening on port 6379 (TCP), aborting.` 这说明redis服务已经在后台运行了。

## 安装Node.js

由于fiora需要特定版本的Node.js（14.15.0），这里需要用到nvm.        

```
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.2/install.sh | bash

export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

返回如下：      

```
root@goorm:/workspace/fioratt# wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.2/install.sh | bash
=> Downloading nvm from git to '/root/.nvm'
=> Cloning into '/root/.nvm'...
remote: Enumerating objects: 356, done.
remote: Counting objects: 100% (356/356), done.
remote: Compressing objects: 100% (303/303), done.
remote: Total 356 (delta 39), reused 164 (delta 27), pack-reused 0
Receiving objects: 100% (356/356), 222.14 KiB | 475.00 KiB/s, done.
Resolving deltas: 100% (39/39), done.

- (HEAD detached at FETCH_HEAD)
  master
  => Compressing and cleaning up git repository

=> Appending nvm source string to /root/.bashrc
=> Appending bash_completion source string to /root/.bashrc
=> Close and reopen your terminal to start using nvm or run the following to use it now:

export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion" # This loads nvm bash_completion
root@goorm:/workspace/fioratt# export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
root@goorm:/workspace/fioratt# [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

安装Node.js：       

```
nvm install 14.15.0
```

返回如下：     

```
root@goorm:/workspace/fioratt# nvm install 14.15.0
Downloading and installing node v14.15.0...
Downloading https://nodejs.org/dist/v14.15.0/node-v14.15.0-linux-x64.tar.xz...
########################################################################################################################################################################################################################## 100.0%
Computing checksum with sha256sum
Checksums matched!
manpath: can't set the locale; make sure $LC_* and $LANG are correct
Now using node v14.15.0 (npm v6.14.8)
Creating default alias: default -> 14.15.0 (-> v14.15.0)
```

验证安装：        

```
root@goorm:/workspace/fioratt# node -v
v14.15.0
root@goorm:/workspace/fioratt# npm -v
6.14.8
```

### 安装yarn:

`npm install -g yarn`            

返回如下：       

```
root@goorm:/workspace/fioratt# npm install -g yarn

> yarn@1.22.19 preinstall /root/.nvm/versions/node/v14.15.0/lib/node_modules/yarn
> :; (node ./preinstall.js > /dev/null 2>&1 || true)

/root/.nvm/versions/node/v14.15.0/bin/yarn -> /root/.nvm/versions/node/v14.15.0/lib/node_modules/yarn/bin/yarn.js
/root/.nvm/versions/node/v14.15.0/bin/yarnpkg -> /root/.nvm/versions/node/v14.15.0/lib/node_modules/yarn/bin/yarn.js

- yarn@1.22.19
  added 1 package in 2.375s
```

克隆项目到本地       

```
git clone https://github.com/yinxin630/fiora.git -b master
```

cd到fiora根目录：`cd fiora`  
安装依赖：`yarn install`.**这一步会比较慢，因为内存太小了（1G），如果终端突然刷新就重新来过吧**

```
root@goorm:/workspace/fioratt/fiora(master)# yarn install
yarn install v1.22.19
[1/5] Validating package.json...
[2/5] Resolving packages...
[3/5] Fetching packages...
info There appears to be trouble with your network connection. Retrying...
[4/5] Linking dependencies...
warning " > @testing-library/react@12.0.0" has unmet peer dependency "react@*".
warning " > @testing-library/react@12.0.0" has unmet peer dependency "react-dom@*".
warning " > @typescript-eslint/eslint-plugin@2.34.0" has incorrect peer dependency "eslint@^5.0.0 || ^6.0.0".
warning " > @typescript-eslint/parser@2.34.0" has incorrect peer dependency "eslint@^5.0.0 || ^6.0.0".
[5/5] Building fresh packages...
$ npx lerna bootstrap && yarn link
lerna notice cli v4.0.0
lerna info versioning independent
lerna info Bootstrapping 10 packages
lerna info Installing external dependencies    
lerna info Symlinking packages and binaries
lerna success Bootstrapped 10 packages
yarn link v1.22.19
success Registered "fiora".
info You can now run `yarn link "fiora"` in the projects where you want to use this package and it will be used instead.
Done in 0.51s.
Done in 540.43s.    
```

构建web客户端：`yarn build:web`.**也很慢，耐心~**         

```
root@goorm:/workspace/fioratt/fiora# yarn build:web
yarn run v1.22.19
$ npx lerna run build:web --stream
lerna notice cli v4.0.0
lerna info versioning independent
lerna info Executing command in 1 package: "yarn run build:web"
@fiora/web: warning package.json: "dependencies" has dependency "@testing-library/jest-dom" with range "^5.14.1" that collides with a dependency in "devDependencies" of the same name with version "^4.2.4"
@fiora/web: $ rm -rf dist && cross-env NODE_ENV=production DOTENV_CONFIG_PATH=../../.env webpack --config build/webpack.prod.js && cp -r -f dist/fiora/* ../server/public
@fiora/web: ℹ Compiling Webpack
@fiora/web: Browserslist: caniuse-lite is outdated. Please run:
@fiora/web: npx browserslist@latest --update-db
@fiora/web: Why you should do it regularly:
@fiora/web: https://github.com/browserslist/browserslist#browsers-data-updating
```

到这里看似卡住，但是实际上它一直在跑，等着就行.**但是一旦刷新还是得重来啊草**  
没报什么错基本就是成功了（废话）

### 配置Jwtsecret

`echo "JwtSecret=<string>" > .env2`. 要将 `<string>` 替换为一个秘密文本，具体什么看你喜欢。

## 配置域名和端口

打开设置，找到URL/Ports选项。  
![url](/images/fiora-goorm/urlprts.png)  
可以看到它默认分配的域名是fioratt-owsii.run.goorm.io，端口是80。  
现在fiora的默认端口是9200，所以需要把它换到80端口（其实把域名端口改到9200也可以，但是打字加上端口号很麻烦）。  
在fiora根目录新建.env（环境变量）文件：`touch .env`  
在左边栏找到.env，打开它（编辑），第一行添加参数`Port=80`  
![port](/images/fiora-goorm/env.png)  
Ctrl+S保存。

## 部署网页

首先，运行`redis-server`，确认redis服务开启之后，运行`mongod --fork --logpath=/data/db/log.log`(目的是保持MongoDB后台运行。  
两个都正常在后台运行的输出如下：

```
root@goorm:/workspace/fioratt/fiora(master)# redis-server
4837:C 08 Nov 2022 07:04:11.295 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
4837:C 08 Nov 2022 07:04:11.295 # Redis version=7.0.5, bits=64, commit=00000000, modified=0, pid=4837, just started
4837:C 08 Nov 2022 07:04:11.295 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
4837:M 08 Nov 2022 07:04:11.296 * Increased maximum number of open files to 10032 (it was originally set to 1024).
4837:M 08 Nov 2022 07:04:11.296 * monotonic clock: POSIX clock_gettime
4837:M 08 Nov 2022 07:04:11.296 # Warning: Could not create server TCP listening socket *:6379: bind: Address already in use
4837:M 08 Nov 2022 07:04:11.296 # Failed listening on port 6379 (TCP), aborting.

root@goorm:/workspace/fioratt/fiora(master)# mongod --fork --logpath=/data/db/log.log
about to fork child process, waiting until server is ready for connections.
forked process: 4988
child process started successfully, parent exiting
```

然后启动fiora：`yarn start`

```
root@goorm:/workspace/fioratt/fiora(master)# yarn start
yarn run v1.22.19
$ npx lerna run start --stream
lerna notice cli v4.0.0
lerna info versioning independent
lerna info Executing command in 1 package: "yarn run start"
@fiora/server: $ cross-env NODE_ENV=production DOTENV_CONFIG_PATH=../../.env ts-node -r dotenv/config --transpile-only src/main.ts
@fiora/server: [2022-11-08T07:11:09.658] [INFO] default - >>> server listen on http://localhost:80
```

打开域名，应该你看到这样的页面：  
![fiora](/images/fiora-goorm/fiora.png)  
几乎就是成品了。

## 保持后台运行

fiora服务端也是会随着shell刷新被关闭的，所以需要pm2让它后台运行。            

```
# 安装 pm2

npm install -g pm2

# 使用 pm2 运行 fiora

pm2 start yarn --name fiora -- start

# 查看 pm2 应用状态

pm2 ls

# 查看 pm2 fiora 日志

pm2 logs fiora
```

运行服务端：`pm2 start yarn --name fiora -- start`  
返回如下：          

```
[PM2] Spawning PM2 daemon with pm2_home=/root/.pm2
[PM2] PM2 Successfully daemonized
[PM2] Starting /root/.nvm/versions/node/v14.15.0/bin/yarn in fork_mode (1 instance)
[PM2] Done.
┌─────┬──────────┬─────────────┬─────────┬─────────┬──────────┬────────┬──────┬───────────┬──────────┬──────────┬──────────┬──────────┐
│ id │ name │ namespace │ version │ mode │ pid │ uptime │ ↺ │ status │ cpu │ mem │ user │ watching │
├─────┼──────────┼─────────────┼─────────┼─────────┼──────────┼────────┼──────┼───────────┼──────────┼──────────┼──────────┼──────────┤
│ 0 │ fiora │ default │ 0.39.2 │ fork │ 5496 │ 0s │ 0 │ online │ 0% │ 26.8mb │ root │ disabled │
└─────┴──────────┴─────────────┴─────────┴─────────┴──────────┴────────┴──────┴───────────┴──────────┴──────────┴──────────┴──────────┘
```

现在就可以直接访问网页端啦。  
最后把我自己的fiora链接放上 [YukiChat](https://yuki-chat.run.goorm.io/),快来跟我聊天吧（
