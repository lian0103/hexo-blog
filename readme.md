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

## 環境
Now using node v12.13.0 (64-bit)

## 部屬流程

```

hexo clean && hexo g //清除public資料夾 && 重新打包
firebase deploy //目前是使用google Firebase Hosting

```
