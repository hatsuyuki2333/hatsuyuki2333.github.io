---
abbrlink: ''
categories:
- 2022年12月
date: '2022-10-02 10:49:16'
description: 导语,作为以后指北的开端,简单地对比了动静态博客。
tags:
- senior
- site
- blog
title: 我想拥有我的博客!
updated: Sun, 02 Apr 2023 10:20:15 GMT
---
> 我也说不清为什么当初想要建立自己的博客了,大概是想要~~跟风~~写点东西。从2019年的生日我发出了第一篇文章到现在我给重建的博客写文,从一开始的不知道写什么到现在有目的的发文章,我觉得这是一种成长。
>
> 其间发生过很多事情:换了一个又一个虚拟主机,博客框架也各种尝试。时间定格在2022/9/12,2019-2022上半年所有的文章没了,数据库文件损坏。这促使我把博客转移到了GithubPages托管。虽然文章丢失了,但是我的足迹没有消失,我执着于写博客,一部分原因就是留下自己的足迹。

言归正传。不管什么原因,你需要搭建自己的博客。所以,首先你要有一个放它的地方。对于静态博客,可部署在Github/Cloudflare/Gitlab/Gitee/Bitbucket上(如果想完全自己写HTML可以试试Neocity)。如果想搭建动态博客(Wordpress/Typecho之类),你至少需要一个虚拟主机或者服务器。

静态博客VS动态博客(简略的)


| 类别 | 多账户 | 在线编辑                 |           存储           | 便利                                      | 成本       | 稳定性                   | 多样性                                |
| ---- | ------ | ------------------------ | :-----------------------: | ----------------------------------------- | ---------- | ------------------------ | ------------------------------------- |
| 动态 | OK     | OK                       | WP比较臃肿,Typecho很轻量 | 需要配置PHP,SQL等(宝塔可以一键部署)    | 存在       | 当心数据库!             | 主流框架少                            |
| 静态 | NO     | NO(Hexo+  Heroku可以) |           Lite           | 安装NodeJS/GO/...+Git搞定。但是命令行操作 | 可以不存在 | 只要托管方没事博客就没事 | Hexo/Hugo/Jekyll/Docsify...一抓一大把 |


| 类别 | 主题         | 插件               | 可访问性                                                 | 安全性                     |
| ---- | ------------ | ------------------ | -------------------------------------------------------- | -------------------------- |
| 动态 | 多(特效也多 | 多                 | 国内运营商基本没问题,国外记得CDN                        | 数据库密码设的复杂些       |
| 静态 | 多           | 多(但是可能不会用 | 除了Gitee都在国外,加载速度较慢,只能通过自定义域名挂CDN | 当心Repository被别人搞了( |

两种平台各有千秋,不能说哪个好不好,只能看你习惯什么了。

预计会写各种博客搭建的教程~~(因为都整过)~~,时间关系,这篇文章仅作为一个开头(顺便提醒我别忘了更新)。

我的知识水平有限,文章中若出现谬误,先不要生气,发送改正意见到我的邮箱,我会抽时间修正。
