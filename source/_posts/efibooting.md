---
abbrlink: efibiooting
categories:
- - 2023年5月
date: '2023-05-15 14:30:08'
description: 用EFI Shell應急啟動移動設備上的操作系統
tags:
- EFI Shell
- Arch Linux
title: EFI Shell應急啟動系統
updated: 2023-5-15T16:1:15.127+8:0
---
# EFI Shell應急啟動系統

前些日子我十分順利的把我盤裡的ArchLinux搞崩潰了（這是另一個故事，這裡暫且不談），只能重新裝了一遍。但是裝系統的時候忘了裝rEFInder，導致換一台機器引導就失效。還好電腦支援Built-in Shell，要不然這系統就算無了。

## 啟動到EFI Shell

> 前置條件：電腦支援UEFI，並且USB設備已連接

開機狂按boot熱鍵，找啟動項，回車進入。

## 啟動系統

uefi方式是從設備的引導文件啟動，而非legacy的引導分區。所以需要找到系統的引導文件（*.efi）。進入Shell之後首先 `map`一下設備，USB設備通常會顯示在底部，並帶有 `PciRoot USB`之類的字樣。找到設備的名稱（Shell中為黃色高亮字樣）打個比方，我這塊盤是 `hd957c2a`

> 需要注意的是 設備名不是以blk開頭的那些

打開硬盤：`hd975c2a:`
列出目錄：`ls`
然後cd進目錄裡面找efi文件即可，知道了輸入   `name-of-the-file.efi`，回車即可。
