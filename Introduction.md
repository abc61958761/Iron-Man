
本篇目的：</br>
能夠稍微了解 NestJs，並將 NestJs 專案運行成功


## 簡介
NodeJs 的框架之一，可使用 TypeScript 來編寫程式碼，當然，JavaScript 依然是支援的，後續範例主要會以 TypeScript 撰寫。

NestJs 幾個重要元素，包含 </br>
**OOP(Object Oriented Programming)** </br>
**FP (Functional Programming)** </br>
**FRP (Functional Reactive Programming)** </br>

曾經寫過 Java 語言的會覺得有點熟悉，我個人是這麼認為的。 </br>


>官網上提供三種方式 </br>
`請確保 NodeJs 版本 >= 10.13.0`

``` console
## 確認 NodeJs 版本
$ node -v
v12.18.3
```

### 使用 CLI 安裝
``` console
$ npm i -g @nestjs/cli

## 確認 nest 版本
$ nest -v
7.4.1

$ nest new project-name
```

### 使用 git 安裝
``` console
## TypsScript 版本
$ git clone https://github.com/nestjs/typescript-starter.git project

## JavaScript 版本
$ git clone https://github.com/nestjs/javascript-starter.git project

$ cd project
$ npm install
$ npm run start
```

### 手動安裝
``` console
$ npm i --save @nestjs/core @nestjs/common rxjs reflect-metadata
```
## 安裝
我這邊以 CLI 方式來建立專案，我認為這是比較方便的方式 </br>
~~首先我們來一個無腦安裝~~，跟著以下步驟就能漂亮的建置一個 NestJs 專案

1.  在終端機下安裝指令
``` console
$ nest new tutorial-1
```

2. 接下來會詢問要使用 npm 還是 yarn
    我這邊選擇 yarn，將箭頭指向 yarn

![](https://i.imgur.com/NssfvJZ.png)

成功畫面
![](https://i.imgur.com/dz4wU5p.png)


3. 進入 tutorial-1 目錄，啟動服務
``` console
$ cd tutorial-1
$ yarn run start
```

4. 在瀏覽器輸入 http://localhost:3000/ ，網頁會出現 **Hello World!**


**以上我們就成功完美啟動了 NestJs 服務！**


## 目錄結構
``` console
src
 ├── app.controller.ts
 ├── app.module.ts
 ├── app.service.ts
 └── main.ts 
```

以圖是來說明整個結構關係，讓讀者能夠更清楚明瞭
![](https://i.imgur.com/SZsS4gP.png)

- 首先我們在 main.ts 使用 NestJs 提供的 NestFactory.create ，建立以及運行應用程式
- 服務啟動後，Client Side 發送 http 請求，Controller 負責接收
- 接著實作部分，如邏輯層、資料庫的 CRUD 會由 Providers 提供 service
讓 Controller 取得運算結果，回傳給 Client Side

不太明白的讀者可以之後再回來看這部分，先讓讀者有個概念，期望後續實作可以更清楚它們之間的關係。

這篇 NestJs 介紹就到這邊

~~記得按下小鈴鐺，訂閱，定時收看！~~


