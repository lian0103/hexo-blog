---
tags: 學習
title: NodeJS架設Web服務(一)
---

NodeJS生態倚賴許多NPM中的套件庫，可以用來協助架設各種Web服務，以下是架設過程的紀錄。

[專案位置](https://github.com/lian0103/NodeAPI)

<!-- more --> 

## 目錄結構
```
│  .env
│  .env.example
│  .gitignore
│  package-lock.json
│  package.json
│  server.js
│  sql.sql
│  
├─config
│      db.js
│      
├─controllers
│      fileController.js
│      postControllers.js
│      
├─helpers
│      httpLogger.js
│      logger.js
│      mail.js
│      
├─logs
│      error.log
│      info.log
│      
├─models
│      Post.js
│      
├─node_modules
│      
├─routes
│      fileRoutes.js
│      postRoutes.js
│      
├─uploadFiles
│      ccc.jpg
│      
└─views
        uploadFile.ejs
        

```

## 串接MySQL
本地安裝MySQL。在安裝成功後，可以先使用workbench做連線測試，這組資訊記錄下來之後要讓Node做連線使用。

- 連線測試
![](https://i.imgur.com/lC9qWYd.jpg)
![](https://i.imgur.com/XB5c2Bt.jpg)

- 建立Post表的欄位與資料

![](https://i.imgur.com/OrOogrN.jpg)


- config/db.js

[NPM mysql2](https://www.npmjs.com/package/mysql2)

```javascript=
require("dotenv").config(); //藉此讀取.env的參數
const mysql = require('mysql2'); //連線資料庫所使用的套件

const pool = mysql.createPool({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  database: process.env.DB_NAME,
  password: process.env.DB_PASSWORD,
  port: process.env.DB_PORT
});

module.exports = pool.promise();//支持非同步處理的連線池物件

```

- .env檔
```shell=
# DATABASE CONNECTION ENVIRONMENT VARIABLES
DB_HOST=localhost
DB_USER=root
DB_NAME=blog-app
DB_PASSWORD=root
DB_PORT=3306
```


- models/Post.js
建立一個資料類的Model，並實作資料操作的方法，如CRUD。這同時是透過mysql2的API，來執行sql指令。

```javascript=
const db = require('../config/db');
class Post {
  constructor(title, body) {
    this.title = title;
    this.body = body;
  }

  save() {
    let date = new Date();
    let created_at = `${date.getFullYear()}-${
      date.getMonth() + 1
    }-${date.getDate()}`;
    
    //新增1筆3個欄位的資料
    let sql = `
            INSERT INTO posts(
                title,
                body,
                created_at
            )
            VALUES(
                '${this.title}',
                '${this.body}',
                '${created_at}'
            )
        
        `;
    return db.execute(sql);//會回傳1個Promise物件
  }
}

```
## 開API
透過NodeJs Express所啟動的Web服務，連線的網域於 http://localhost:3000
並可監聽來訪請求的路徑與HTTP連線方法(GET、POST、UPDATE、DELTE..)對應。

- server.js
```javascript=
//...(略)
app.use('/posts', require('./routes/postRoutes'));

```
- routes/postRoutes.js
```javascript=
const express = require('express');
const postControllers = require('../controllers/postControllers')
const router = express.Router();

router.route("/").get(postControllers.getAllPosts);

router.route("/").post(postControllers.createNewPost);

router.route("/:id").get(postControllers.getPostById);

module.exports = router;
```


- Postman測試連線
GET方法與路徑/posts，則可對應去呼叫控制層的資料操作方法，並透過exprss路由的回傳物件將資料回傳。

```javascript=
//postControllers.js
const Post = require('../models/Post');

exports.getAllPosts = async (req, res, next) => {
  try {
    const [posts] = await Post.findAll();

    res.status(200).json({ posts });
  } catch (error) {
    console.log(error);
    next(error);
  }
};
```
:::info
GET  /posts
:::
![](https://i.imgur.com/cjUhayn.jpg)
