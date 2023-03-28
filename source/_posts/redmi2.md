---
title: 3202年红米2搞机的正确姿势
date: 2023-02-05 09:59:57
tags: [redmi 2,twrp,lineage]
categories: 2023年2月
description: 老手机焕发新生系列（
---

# 3202年红米2搞机的正确姿势

120买了台红米2，顺便收了一张32G的SD卡和一块备用电池，总共185块钱。

拿到手之后先看系统，发现已经是MIUI9（Android5.0）了，就不用先更新固件版本。如果是安卓4.4需要先更新固件版本，后面再说。

| Model              | Redmi 2              | Redmi 2A   |
| ------------------ | -------------------- | ---------- |
| SoC                | Snapdragon 410 ARM64 | L1860C ARM |
| RAM                | 2GB LPDDR3           | Same       |
| Storage            | 16GB eMMC            | Same       |
| External   Storage | 32GB Max             | Same       |

红米2的SoC是骁龙410，而2A是联芯L1860C，由于L1860C主频更高所以2A性能略好，但是410是64位处理器，而L1860C仅32位，所以410对大多数现代应用程序兼容性较好，可以刷MIUI安卓5，而L1860C就只能停留在安卓4.4了。

但是抛开官方源，我们还有更多的选择。主流第三方ROM对红米2均提供支援，比如Lineage，Arrow和Mokee，都支持到安卓10。由于第三方ROM基本上把2和2A视为同一款机型，所以都只做了32位系统，发挥不出来410的64位支援。所以我选择了自己build64位ROM。

自己build的bug贼多，我又抽不出来太多时间，于是放弃了。还好XDA上已经有人做出来了安卓8.1的64位ROM，够用了。

开整！

## 开始之前

你需要准备：一台运行**windows7及以上/Ubuntu18以上/MacOS**的电脑；一台**红米2**；一张4~32GB的SD卡；一根USB数据线；足够的**阅读能力**和**脑子**

## 下载资源

[原帖地址](https://forum.xda-developers.com/t/experimental-rom-arm64-redmi2-8-1-0-unofficial-lineageos-15-1.3847951/)在这里，不过我还是直接把链接先copy过来得了

> ADB工具（选择对应系统的版本，下载之后解压）     
> 
> [Link](https://developer.android.google.cn/studio/releases/platform-tools?hl=zh-cn)
> 
> 64位的TWRP（下载之后移动到ADB文件夹并重命名为twrp.img）    
> [Link](https://www.androidfilehost.com/?fid=11410963190603861795)
> 
> System分区扩容工具（两个都需要）    
> [Link1-ResizePartition](https://forum.xda-developers.com/attachments/1resize-system-redmi2-v1-03-20180623-1847-signed-zip.4534093/)      
> 
> [Link2-ExpandPartition](https://forum.xda-developers.com/attachments/3expand-filesystem-redmi2-v1-03-signed-zip.4534094/)
> 
> ROM
> [Link](https://androidfilehost.com/?w=files&flid=283111)

System扩容工具和ROM下载之后移动到SD卡中，并装入手机。

## 连接电脑

将手机进入开发者模式（没进入百度即可），找到并开启“通过USB调试”选项和有关FASTBOOT的选项。

把手机用USB线连接到电脑上。

在ADB目录内打开终端。     

| Windows                                                   | Ubuntu                                                                                                                     | MacOS                       |
| --------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- | --------------------------- |
| Windows徽标+R打开运行，输入`cmd`并回车,在窗口内输入`cd`并拖动ADB文件夹到窗口，之后Enter | Ctrl+Alt+T打开终端，cd到ADB目录。也可以`sudo apt-get install nautilus-open-terminal`，完成之后`nautilus -q`，ADB文件夹内右键会有`Open in Terminal`选项 | 打开终端之后`cd`并将ADB文件夹拖动到窗口内，回车 |

输入`adb devices`，手机会提示“是否允许该计算机调试”，选择允许。

adb应该会返回`xxxxxx device`说明已连接。如果没有，尝试安装驱动（百度即可）。

## 刷入TWRP

> 红米2那个年代还没有流行起BL锁，所以不用解BL。

终端内输入`adb reboot bootloader`，设备应当重启，进入“米兔维修”界面，有蓝色**FASTBOOT**字样出现。

终端`fastboot devices`，应该会提示`xxxxxx fastboot`.Ubuntu和MacOS提示`Permission denied`需要用root权限运行。

终端`fastboot flash recovery twrp.img`,刷入TWRP。

成功之后关机，同时按住**音量↑+音量↓+电源键**，启动到Recovery。屏幕会显示**TEAMWIN Recovery Project**字样。

## 扩容分区

**扩容System有变砖风险，谨慎！！！！**

进入TWRP之后会提示是否保持System分区为只读，此处**不要选择保持只读，直接划到底**，进入主页。

点击`Install`,找到`1Resize-System-Redmi2.zip`,点击`Install`，接下来的页面不用管直接划到底。找不到zip点击`Select Storage`找找其他位置

安装时会弹出GUI，直接Next即可，`Select System Size`可选1.5GB或2GB，推荐2GB是因为后续可以刷OpenGAPPS。最后选择START。

最终会提示`ALL DONE`,点击Reboot即可，会自动重启到Recovery。

## 刷入ROM

进入TWRP之后点击`Install`,找到ROM文件，选择`Install`即可。

刷入完成之后，**不要Reboot**，选择Back。回到Install界面，找到`3Expand-filesystem-Redmi2.zip`,安装。

完成之后，就可以重启到系统了。

开始设置吧！

## 补充：安装OpenGAPPS

> 为什么我把OpenGapps单独拎出来呢？因为如果刷ROM之后直接装OpenGAPPS可能会因为众所周知的网络原因无法登录，没法正常使用手机。所以这里**默认**你已经做好了科学上网的准备。

[OpenGAPPS下载地址](https://opengapps.org)       

选择ARM64—8.1—nano，点击下载按钮，下载文件。完成之后拷到SD卡内，装入手机。

重启到Recovery，点击`Install`选择OpenGAPPS文件，并安装。完成之后重启即可。接下来就可以设置谷歌账户了。

## 补充：Magisk

直接找boot.img修补来安装Magisk太麻烦，这里讲root之后直接安装Magisk的方式，比较简捷。

### ROOT

LineageOS17之前，root权限需要单独刷入扩展包实现。这里需要两个文件，一个用于安装，一个用于删除。

[SU安装包](https://mirrorbits.lineageos.org/su/20190709/addonsu-15.1-arm64-signed.zip)      

[SU卸载包](https://mirrorbits.lineageos.org/su/20190709/addonsu-remove-15.1-arm64-signed.zip)     

下载之后移动到SD卡，并装入手机。

重启到Recovery，点`Install`，选择`addonsu-15.1-arm64-signed.zip`,安装。完成后重启到系统，在开发者模式下找到ROOT权限管理，授予“应用和ADB”。

### 安装Magisk

安装最新版Magisk APP，点击安装—直接安装，等待ROOT权限申请提示出现并允许。安装完成之后就好了。

不要立即重启，选择自行重启。**立即重启之后会提示发现不属于Magisk的SU模块，还是得进行下一步卸载。**

重启到Recovery，点`Install`，选择`addonsu-remove-15.1-arm64-signed.zip`，安装，完成之后重启。

就可以愉快的使用Magisk了。
