# Hexo Blog

## 使用MD檔新增post
文章路徑於:./source/_posts/


## run dev hexo server 
1.先安裝hexo

2.git clone
```
https://github.com/lian0103/hexo-blog.git

```
2.git clone themes
```
# 在hexo專案目錄
$ git clone https://github.com/lian0103/hexo-blog-theme-next.git themes/next
```

說明:
nexT主題也是獨立的一包模組，在結構上是被當作Hexo Blog專案的子模組，所以要分別做git管理。
同理，假設要更換專案的主題，可到Hexo官網提供的主題去clone，再調動_config.yml的設定。


## 文章格式參考
```
---
title: 年底讀書
tags: books
categories: 職涯 
---

<!-- more -->  ## 此標註之上會擷取顯示於文章列

內文...

```

## 本地服務指令
```
hexo serve
```

## 部屬指令
在主題內預設有github pages的部屬設定。也可以搭配其他部屬方式。
而在github pages部屬需要注意根目錄路徑需要有對應調整。

目前是使用google Firebase Hosting

```
hexo clean && hexo g
firebase deploy
```
