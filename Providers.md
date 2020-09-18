Providers 是 Nest 的一個基本概念。 </br>
許多基本的 Nest 類可能被視為 provider - service,repository, factory, helper 等等。</br>
他們都可以通過 constructor 注入依賴關係。這意味著對象可以彼此創建各種關係，並且“連接”對象實例的功能在很大程度上可以委託給 Nest運行時系統。</br>
**Provider 只是一個用 @Injectable() 裝飾器註釋的類。**

## Service
主要負責跟資料庫的溝通，與資料庫的邏輯都會寫在這裡，並且被設計給 Controller 使用，就當作是資料庫跟 Controller 的橋樑吧！

使用 NestJs CLI 提供的 command ，可以快速建立 service 相關文件
```console
$ nest g service cats

CREATE src/cats/cats.service.spec.ts (446 bytes)
CREATE src/cats/cats.service.ts (88 bytes)
UPDATE src/app.module.ts (657 bytes)
```

```console
## 自動生成 'service' 需要的文件

src
└── cats
     ├── cats.service.ts
     └── cats.service.spec.ts
```

### Provider registration
```typescript
// path： src/app.module.ts
// 自動註冊'cats.service' 到 providers

import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { CatsController } from './cats/cats.controller';
import { TypeOrmModule } from '@nestjs/typeorm';
import { CatsService } from './cats/cats.service';

@Module({
  imports: [],
  controllers: [AppController, CatsController],
  providers: [AppService, CatsService]
})
export class AppModule {}
```

接下來定義 Cat 的介面 (interface)，確保我們 Cat 裡面的參數是一致的。

```typescript
// path： src/cats/interfaces/cat.interface.ts

export interface Cat {
    name: string;
    age: number;
    color: string;
}
```

接下來就可以開始在 service 加入需要的函式、邏輯。
```typescript
// path： src/cats/cats.service.ts

import { Injectable } from '@nestjs/common';
import { Cat } from './interfaces/cat.interface';

@Injectable()
export class CatsService {
    private cats: Cat[] = [];
    
    // 將新增的 cat 放進暫存 cats
    create(cat: Cat) {
        this.cats.push(cat);
        return {
            id: this.cats.length,
            ...cat
        };
    }

    // 回傳暫存的 cats
    findAll(): Cat[] {
        return this.cats;
    }
}
```

橋樑搭建好之後，Controller 就可以呼叫 service 的函式。

```typescript
// path: src/cats/cats.controller.ts

import { Controller, Get, Post, Body } from '@nestjs/common';
import { CreateCatDto } from './dto/create-cat.dto';
import { Cat } from './interfaces/cat.interface';
import { CatsService } from './cats.service';

type CatType = {
    id: string | number,
    name: string,
    age: number,
    color: string
}

@Controller('cats')
export class CatsController {
    constructor(private catsService: CatsService) {}

    // Http 方法
    @Get()
    // 函式名稱: 回傳型態
    findAll(): Cat[] {
        return this.catsService.findAll();
    } 

    @Post()
    create(@Body() createCatDto: CreateCatDto): CatType {
        const newCat = this.catsService.create(createCatDto);
        return newCat;
    }
}
```

`小碎念：
可能有人會想說，為什麼還要再 service 寫一次 create 的函式，根本就是多此一舉的動作！
沒錯，以目前的行為來說 service 確實沒什麼作用，因為前面有說到，service 主要是在跟資料庫做溝通的媒介，讀者可以先想像暫存的動作就是寫入資料庫的動作。
`

這樣我們就把需要的函式都建立完了，接下來就是使用 Postman 來呼叫 API，驗證我們的邏輯是否正確

1. 先確認是否已有 Cat 資料暫存在服務中，呼叫 http://localhost:3000/cats 方法選擇 Get，沒意外的話讀者應該會收一個空陣列 []，因為我們還沒有 Create Cat。

2. 建立一個 Cat 到暫存區中，呼叫 http://localhost:3000/cats 方法選擇 Post，在Body 輸入想建立的數據，送出後會回傳資料建立成功的訊息，讀者可以多建立幾筆，下一個步驟會再次搜尋出剛剛所建立的所有 Cat。
```jsonld
## Body
{
    "name": "小橘貓",
    "age": 4,
    "color": "orange"
}
```

![](https://i.imgur.com/jmNW5Yt.png)

3. 再次呼叫 ```Get: http://localhost:3000/cats```，會得到剛剛建立成功的 Cat 陣列，我剛剛建立了2筆資料。
![](https://i.imgur.com/xfsD6iH.png)
