---
abbrlink: efibiooting
categories: []
date: '2023-05-15 14:30:08'
tags:
- EFI Shell
- Arch Linux
title: EFI Shell應急啟動系統
updated: Mon, 15 May 2023 06:31:33 GMT
---
# EFI Shell應急啟動系統

開機狂按boot熱鍵，找啟動項，回車進入。     

## 啟動系統

uefi方式是從設備的引導文件啟動，而非legacy的引導分區。所以需要找到系統的引導文件（*.efi）。          

進入Shell之後首先 `map`一下設備，USB設備通常會顯示在底部，並帶有 `PciRoot USB`之類的字樣。

找到設備的名稱（Shell中為黃色高亮字樣）打個比方，我這塊盤是 `hd957c2a`

> 需要注意的是 設備名不是以blk開頭的那些  



打開硬盤： `hd975c2a:`

列出目錄： `ls`

然後cd進目錄裡面找efi文件即可，知道了輸入   `name-of-the-file.efi`，回車即可。
