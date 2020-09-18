本篇目的：
1. 了解 Controllers 在 NestJs 佔據哪種角色
2. 可以使用 Postman 成功收發 API


負責接收 **requests** 和回傳 **responses**。也就是後端收發站，主要負責路由
![](https://i.imgur.com/UrZYi2N.png)

### 利用 CLI 建立 controller
$ nest g controller <CONTROLLER_NAME>

```console
$ nest g controller cats

CREATE src/cats/cats.controller.spec.ts (478 bytes)
CREATE src/cats/cats.controller.ts (97 bytes)
UPDATE src/app.module.ts (322 bytes)
```

NestJs CLI 會自動幫我們建立 controller ，以及在 module 的根目錄下直接引入新建立的 controller，看一下圖示會更清楚。

```console
## 自動生成 'controller' 需要的檔案

src
└── cats
     ├── cats.controller.ts
     └── cats.controller.spec.ts
```

```console
## 自動引入'cats.controller' 到 controllers

src
└── app.module.ts
```

### 建立 Get Cats 

```typescript
//目錄: src/cats/cats.controller.ts

import { Controller, Get } from '@nestjs/common';
import { get } from 'http';

// controller 的 decorator
// 路由前綴設置為 cats
@Controller('cats')
export class CatsController {
    // Http 方法
    @Get()
    // 函式名稱: 回傳型態
    findAll(): string[] {
        return ['小黑', '小橘', '小白'];
    } 
}
```

> Http方法：@Put(), @Delete(), @Patch(), @Options(), @Head(), @All()


使用 Postman ，Http 選擇 Get ，輸入 http://localhost:3000/cats ，便可獲得剛剛的回傳值

![](https://i.imgur.com/o0phMjf.png)

`
注意：讀者輸入完後回傳 Could not send request 等錯誤訊息，請重新啟動服務，如果覺得每次改動都要重啟服務太麻煩，後續篇章會介紹 Hot Reload，包你滿意。
`

### 建立 Post Create Cat

#### Controller
```typescript
// 目錄: src/cats/cats.controller.ts

import { Controller, Get, Post, Body } from '@nestjs/common';
import { CreateCatDto } from './dto/create-cat.dto';

type Cat = {
    id: string | number,
    name: string,
    age: number,
    color: string
}

@Controller('cats')
export class CatsController {

    @Post()
    create(@Body() createCatDto: CreateCatDto): Cat {
        return {
            id: '1',
            ...createCatDto
        }
    }
}
```

#### Data Transfer Object(DTO)
資料互相傳遞時，所需要的參數。

```typescript
// 目錄: src/dto/create-cat.dto.ts

export class CreateCatDto {
    name: string;
    age: number;
    color: string;
}
```


小小補充：另一個好處是，當你要使用DTO裡面的參數時，VSCode會自動幫你列出來，不確定其他編輯器是否有一樣的功能。
![](https://i.imgur.com/Fs07hX1.png)


使用 Postman ，Http 選擇 POST ，輸入 http://localhost:3000/cats ，選擇 Body ，格式選擇 JSON
```json 
{
    "name": "小黃貓",
    "age": 5,
    "color": "yellow"
}
```

![](https://i.imgur.com/0Kha4S3.png)

這樣就成功建立 Create Cate 以及 Get Cats 兩個 API，接下來比較深入的部份就不細說，因為這邊後續會著重在 GraphQL，有興趣的人可以去官網參考，或者有天時地利人和時，我會再補上。

下一篇會講解 NestJs Providers。
