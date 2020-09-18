---
tags: NestJs
---
Module
===
前幾篇文章中，我們提到很多次 Module ，這個篇章就詳細說明 Module 的功用。

> A module is a class annotated with a @Module() decorator. 
> The @Module() decorator provides metadata that Nest makes use of to organize the application structure.


一開始建立 NestJs 專案就會生成一個 Root Module (app.module.ts)</br>
根據需求建立 Module (User Module, Order Module, Chat Module)</br>
清楚劃分每個 Module 功能，最後再 import 到 Root Module。

並不是每個專案都需要多個 Module，小專案可能只會有 Root Module。

## Feature modules
```bash
## 利用 CLI 建立 module
## nest g module <MODULE_NAME>

$ nest g module cats

CREATE src/cats/cats.module.ts (81 bytes)
UPDATE src/app.module.ts (722 bytes)
```

NestJs CLI 會自動幫我們建立 cats module ，以及在 module 的根目錄下直接引入新建立的 CatsModule 。

```console
src
└── cats
     └── cats.module.ts
```

接著我們把 Cats 的 service 跟 controller 引入到 Module 的 providers 以及 controllers。
```typescript
// 目錄: src/cats/cats.module.ts

import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
    controllers: [CatsController],
    providers: [CatsService]
})
export class CatsModule {}
```

眼尖的讀者就會發現，CatsController 跟 CatsService 我不是已經在 app.module.ts 中引入過了嗎？ </br>
為什麼還要再引入一次！別急，接下來我們就是要修改 app.module.ts。

```typescript
// 目錄: src/app.module.ts

import { Module } from '@nestjs/common';
import { CatsModule } from './cats/cats.module';
// import { CatsController } from './cats/cats.controller';
// import { CatsService } from './cats/cats.service';

@Module({
    imports: [CatsModule], // 引入！！！
    // controllers: [CatsController], 拔除
    // providers: [CatsService], 拔除
})
export class AppModule {}
```
原本在 app.module.ts 中我們直接將 CatsController, CatsService 引入到 Module ，現在我們只要引入 CatsModule 就可以了，後續如果還有其他的 Module (User, Order ...)，也是直接將 Module 接續引入就可以了。

這就是一個模組化的概念。可以想像一下，假設未來每個 Module 裡也有多個 controller, service，在沒有做模組化的情況下，你的 app.module.ts 是不是會又臭又長呢？

所以我們會盡可能把相依性高的功能，整合到一個 Module ，建立清晰的邊界，並用SOLID原理進行開發，當團隊或是服務慢慢增長時，這有助於我們專案管理。

## Shared modules
Modules 預設是採用 Singletons(單體模式)，所以可以在多個 Modules 共用同一個 Provider。

每個 Module 都能被視為是 Shared Module，被建立後便可以被任何 Modules 重複使用。
舉個例子，我們想讓 CatsService 在 Modules 之間共用，只需要將 CatsService 添加到 exports。

```typescript
// 目錄：src/cats/cats.module.ts

import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
    controllers: [CatsController],
    providers: [CatsService],
    exports: [CatsService] // 導出
})
export class CatsModule {}
```
現在只要將 CatsModule 導入，便可以使用 CatsService 。

## Global modules
假設有個 Module 必須在其他多數的 Modules 中引入，我們能使用裝飾器提供的 **@Global()**，像是範例中 CatsService 使用 @Global()，在其他模組就能任意的使用。

該注意的是，非不得已要定義全域的模組時，root 或者 core module 只註冊一次，否則3個月後回來，你就已經忘記這函式是從哪來的。

官網上建議盡量不要使用 @Global()，他說明這不是一個很好的寫作規範，使用 import 清楚表明才是最佳方式。

```typescript
@Global() // 加入裝飾器
@Module({
    controllers: [CatsController],
    providers: [CatsService],
    exports: [CatsService]
})
```

