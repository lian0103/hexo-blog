---
tags: 學習
title: NodeJS架設Web服務(二)
---

NodeJS生態倚賴許多NPM中的套件庫，可以用來協助架設各種Web服務，以下是架設過程的紀錄。

[專案位置](https://github.com/lian0103/NodeAPI)

<!-- more --> 

## 上傳檔案
- 使用套件
[formidable](https://www.npmjs.com/package/formidable)
[mv](https://www.npmjs.com/package/mv)

使用表單物件和POST方法傳送檔案，formidable可做為中間層把檔案的資訊做解析。解析後的結果，再利用mv做搬運。

- fileController.js
```javascript=
const formidable = require('formidable');
const mv = require('mv');
const path = require('path');
const fs = require('fs');

exports.uploadFile = async (req, res, next) => {
  const form = new formidable.IncomingForm();
  form.parse(req, function (err, fields, files) {
    const uploadPath = path.join(process.cwd(), 'uploadFiles');//自訂的檔案存放路徑

    if (!fs.existsSync(uploadPath)) {
      fs.mkdirSync('uploadFiles');
    }

    const filename = fields.fileName
      ? `${fields.fileName}.${
          files.filetoupload.originalFilename.split('.')[1]
        }`
      : files.filetoupload.originalFilename;//有輸入自訂檔名欄位時使用自訂檔名
    const oldpath = files.filetoupload.filepath;
    const newpath = path.join(uploadPath, filename);
    
    //這個部分之後再比較把圖片檔File轉base64做法的差異
    mv(oldpath, newpath, function (err) {
      if (err) {
        throw err;
        next();
      }
      res.write(`File uploaded and moved to ${newpath}`);
      res.end();
    });
  });
};


```
## 取得檔案

[Express SendFile API](http://expressjs.com/zh-tw/api.html#res.sendFile)

- fileController.js
```javascript=

exports.getFile = async (req, res, next) => {
  const fileName = req.params.fileName;
  const options = {
    root: path.join(process.cwd(), 'uploadFiles'),
  };

  res.sendFile(fileName, options, function (err) {
    if (err) {
      next(new Error('無此檔案'));
    }
  });
};

```

- 取得結果
:::info
GET /file/:fileName
:::
![](https://i.imgur.com/0eSrCho.jpg)


## 寄送郵件
- helpers/mail.js

支持SMTP、Gmail發送的套件
[nodemailer](https://www.npmjs.com/package/nodemailer)

```javascript=
const logger = require('./logger');
const nodemailer = require('nodemailer');

//透過GMAIL帳號和'應用程式密碼'登入
const transporter = nodemailer.createTransport({
  service: 'gmail',
  auth: {
    user: process.env.GMAIL_USER,
    pass: process.env.GMAIL_PASS,
  },
});

const mailOptions = {
  from: process.env.GMAIL_USER,
  to: 'kel0oo0@yahoo.com.tw',
  subject: 'Sending Email using Node.js',
  text: 'That was easy???',
  html: `<h1>HI~~!!</h1><img src="https://miro.medium.com/max/676/1*XEgA1TTwXa5AvAdw40GFow.png">`,
};

const sendEmail = (options = {}) => {
  let mergeOption = {
    ...mailOptions,
    ...options,
  };
  
  //發送
  transporter.sendMail(mergeOption, function (error, info) {
    if (error) {
      console.log(error);
    } else {
      logger.info(mergeOption);
    }
  });
};
```

- 應用程式密碼
![](https://i.imgur.com/SzAGCvv.jpg)

:::warning
從nodemailer文件上可知，Gmail的送信方式比較適合開發時測試，真正產品應用時還要考量其他因素，不建議使用Gmail。
:::

## API請求紀錄
使用套件
[winston](https://www.npmjs.com/package/winston):用來產生log檔案
[morgan](https://www.npmjs.com/package/morgan):產生能擷取HTTP資訊的中間層

- helplers/logger.js
- helplers/httpLogger.js

- 紀錄
![](https://i.imgur.com/dzh1mVF.jpg)