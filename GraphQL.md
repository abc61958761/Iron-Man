GraphQL
===

>GraphQL 是一種為 API 的查詢語言

`這個篇章不會介紹 GraphQL ，所以需要讀者先有一定 GraphQL 的基礎才適合閱讀接下來的文章。`

NestJs 提供了 @nestjs/graphql 模組 ，是基於 Apollo server 搭建起來的模組，我們可以直接安裝此模組來運行 GraphQL 。

1. 首先我們先建立一個新的 NestJs 專案
2. 安裝需要的 package 
    ```
    $ yarn add @nestjs/graphql graphql-tools graphql
    ```
3. 安裝完後我們就可以註冊 GraphQLModule 

    ```typescript
    // 目錄: src/app.module.ts
    
    import { Module } from '@nestjs/common';
    import { AppController } from './app.controller';
    import { AppService } from './app.service';
    // 引入 GraphQLModule
    import { GraphQLModule } from '@nestjs/graphql';

    @Module({
      imports: [
        // 註冊 GraphQLModule
        GraphQLModule.forRoot({})
      ],
      controllers: [AppController],
      providers: [AppService],
    })
    export class AppModule {}
    ```
4. 將服務啟動
```
$ yarn run start
```
:::danger
1. 如果啟動時，出現以下錯誤，讀者可以試著把 NodeJs 版本更新到 12(包含)以上。
```console
yarn run v1.22.4
$ nest start
node_modules/@nestjs/graphql/dist/interfaces/gql-gateway-module-options.interface.d.ts:1:58 - error TS2307: Cannot find module '@apollo/gateway' or its corresponding type declarations.

1 import { GatewayConfig, ServiceEndpointDefinition } from '@apollo/gateway';
                                                           ~~~~~~~~~~~~~~~~~
node_modules/@nestjs/graphql/dist/interfaces/gql-gateway-module-options.interface.d.ts:2:35 - error TS2307: Cannot find module '@apollo/gateway/src/datasources/types' or its corresponding type declarations.

2 import { GraphQLDataSource } from '@apollo/gateway/src/datasources/types';
                                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Found 2 error(s).

error Command failed with exit code 1.
```

2. NodeJs 版本升級成功後再一次啟動服務，可能會再出現以下的錯誤訊息，我們就按照他的提示，安裝 apollo-server-express ，再重起服務。
```
[Nest] 40221   - 09/09/2020, 4:49:58 PM   [PackageLoader] The "apollo-server-express" package is missing. Please, make sure to install this library ($ npm install apollo-server-express) to take advantage of GraphQLModule. +6ms
```
3. 然後就完美啟動！！！才怪，這時你會看到一個警告訊息，因為我們還沒有定義任何的 schema 或 modules ，所以服務無法正常運行，這邊請讀者先到 github 先把專案 [clone](https://github.com/abc61958761/tutorial-graphql) ，再運行一次。
```
(node:40533) UnhandledPromiseRejectionWarning: Error: Apollo Server requires either an existing schema, modules or typeDefs
```
:::

5. 在終端機下指令 ```yarn start:dev```，成功後輸入 http://localhost:3000/graphql 就能開始使用 graphql playground 。
```console
$ yarn start:dev

yarn run v1.22.4
$ nest build --webpack --webpackPath webpack-hmr.config.js

 Info  Webpack is building your sources...

Hash: 3d85a29aa587648661f9
Version: webpack 4.44.1
Time: 5321ms
Built at: 09/10/2020 4:40:03 PM
Entrypoint main = main.js
[Nest] 43028   - 09/10/2020, 4:40:04 PM   [NestFactory] Starting Nest application...
[Nest] 43028   - 09/10/2020, 4:40:04 PM   [InstanceLoader] AppModule dependencies initialized +25ms
[Nest] 43028   - 09/10/2020, 4:40:04 PM   [InstanceLoader] CatsModule dependencies initialized +1ms
[Nest] 43028   - 09/10/2020, 4:40:04 PM   [InstanceLoader] GraphQLSchemaBuilderModule dependencies initialized +2ms
[Nest] 43028   - 09/10/2020, 4:40:04 PM   [InstanceLoader] GraphQLModule dependencies initialized +2ms
[Nest] 43028   - 09/10/2020, 4:40:04 PM   [NestApplication] Nest application successfully started +253ms
```

![](https://i.imgur.com/lU9zndl.png)

6. 執行 crateCat API 以及 getCats API，結果應該跟使用 Postman 回傳是一樣，這邊就不多為 Playground 使用方式做詳細介紹，需要的讀者可以到官網上閱讀文件。

```graphql
# crateCat
mutation {
  createCat(catInput: {
    age: 3
    color: "yellow"
    name: "test123"
  }) {
    id
    age
    color
    name
  }
}

# getCats
query {
  getCats {
    id
    age
    name
    color
  }
}
```

貌似完美的啟動了服務，也能進行簡單的 Mutation 跟 Query，看來我們又邁進了一大步！好吧，我相信大多數人可能看不懂，別急，後續篇章會開始說明，當 NestJs 遇上 Graphql ，整個結構究竟會怎麼搭建，敬請期待！！
