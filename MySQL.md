MySQL
===

目標：
1. 建立 MySQL 資料庫
2. 透過 service 層實作資料庫的 CRUD
---

資料庫方面，可選擇 SQL 或是 NoSQL ，這篇文章會介紹如何引入 MySQL，後續文章會有 mongoDB 的教學。
NestJs 提供現成 TypeORM @nestjs/typeorm，由 TypeScript 撰寫。

### 安裝 MySQL typeorm Package
```
$ yarn add @nestjs/typeorm typeorm mysql
```

使用 Typeorm 提供的 TypeOrmModule#forRoot ，並設定資料庫參數
```typescript
// 目錄: src/app.module.ts

import { TypeOrmModule } from '@nestjs/typeorm';

@Module({
  imports: [
    TypeOrmModule.forRoot({
      type: 'mysql',
      host: '127.0.0.1',
      port: 3306,
      username: 'admin', // MYSQL_USER
      password: 'admin', // MYSQL_PASSWORD
      database: 'test',  // MYSQL_DATABASE
      entities: [],
      synchronize: true,
    }),
  ]
})
```

`
補充: 
這邊我沒有在本機實際安裝MySQL，所以也不會有安裝教學，我是直接在 Docker Run MySQL，以下提供簡單範例
`
> docker-compose.yml
```yaml
version: "3.7"

services:
  mysql:
    container_name: mysql
    image: mysql
    command: --default-authentication-plugin=mysql_native_password
    cap_add:
      - SYS_NICE  # CAP_SYS_NICE
    environment:
      MYSQL_PASSWORD: admin
      MYSQL_ROOT_PASSWORD: admin
      MYSQL_USER: admin
      MYSQL_DATABASE: test
    ports:
      - 3306:3306
    volumes:
      - mysql_data:/var/lib/mysql
volumes:
  mysql_data:
```

接下來隨便抄起個 Database Tools ，確認資料庫是有在運行的，沒問題的話我們就是順利接上資料庫了！</br>
(如果沒有使用 Hot Reload 的讀者，記得重新啟動服務)

### Repository pattern

TypeORM 支援 `Repository Design Pattern`，因此每個 Entity 都有一個 Repository
Repository 可以從資料庫連線中取得

1. 首先先建立一個 Entity
    ```typescript
    // Path: src/cats/entities/cat.entity.ts

    import { Entity, Column, PrimaryGeneratedColumn } from 'typeorm';

    @Entity()
    export class Cat {
        @PrimaryGeneratedColumn()
        id: number | string;

        @Column()
        name: string;

        @Column()
        age: number;

        @Column()
        color: string;

        @Column({ default: true })
        isActive: boolean;
    }
    ```

2. 該如何使用 Entity 呢？
在 module forRoot() 插入 entities ， 讓 TypeORM 能獲取 Entity 的資訊
    ```typescript
    // Path: src/app.module.ts

    import { Cat } from './cats/entities/cat.entity';

    @Module({
      imports: [
        TypeOrmModule.forRoot({
          type: 'mysql',
          host: 'localhost',
          port: 3306,
          username: 'root',
          password: 'root',
          database: 'test',
          entities: [Cat],
          synchronize: true,
        }),
      ],
    })
    ```
3. 接著在 CatModule 的 @Module 中使用 forFeature() 方法註冊 Repositories ，我們就能使用 @InjectRepository() decorator 將 Repository 注入到 Service

    ```typescript
    // Path: src/cats/cats.module.ts

    import { Cat } from './entities/cat.entity';

    @Module({
        controllers: [CatsController],
        imports: [TypeOrmModule.forFeature([Cat])],
        providers: [CatsService]
    })
    ```

    ```typescript
    // Path: src/cats/cats.service.ts

    import { Injectable } from '@nestjs/common';
    import { InjectRepository } from '@nestjs/typeorm'
    import { Cat } from './entities/cat.entity';
    import { Repository } from 'typeorm';

    @Injectable()
    export class CatsService {
        constructor(
            @InjectRepository(Cat)
            private catRepository : Repository<Cat>
        ) {}

        async create(cat: Cat): Promise<Cat> {
            const newCat = await this.catRepository.save(cat);
            return newCat; 
        }

        async findAll(): Promise<Cat[]> {
            return this.catRepository.find();
        }
    }
    ```

4. 最後使用 Postman 測試，並檢查資料庫資料是否正確

    Post: http://localhost:3000/cats
    建立 Cat
    ![](https://i.imgur.com/c6uI0h5.png)

    Get: http://localhost:3000/cats
    取得所有 Cat
    ![](https://i.imgur.com/nHyUn0k.png)


    檢查資料庫
    ![](https://i.imgur.com/vWR9Lx9.png)

`
補充坑：
在使用 MySQL 8 的時候，會出現以下的錯誤，有遇到的讀者可以參考
`

1. 
    - 問題描述 :
        ```
        Error: ER_NOT_SUPPORTED_AUTH_MODE: Client does not support authentication protocol requested by server; consider upgrading MySQL client
        ```
        簡單來說就是 MySQL8 加入新的驗證方法，而我們使用 npm 或是 yarn 安裝的 mysql 都還未更新，所以會出現這個錯誤
        下方連結是我找到的解決方法，希望對讀者們有一些幫助，如果有新看法也歡迎提出。

    - 解決方法 : 
        在 docker-compose 中加入 `command: --default-authentication-plugin=mysql_native_password`

    - 來源: 
       [MySQL 8.0 — Client does not support authentication protocol requested by server; consider upgrading MySQL client](
    https://medium.com/codespace69/mysql-8-0-client-does-not-support-authentication-protocol-requested-by-server-consider-8afadc2385e2)


2. 
    - 問題描述 :
        ```bash
        mysql    | mbind: Operation not permitted
        ```

    - 解決方法:
        在 docker-compose 中加入其中一個設定
        ```yaml
         security_opt:
              - seccomp:unconfined
        ```
        ```yaml
        cap_add:
          - SYS_NICE  # CAP_SYS_NICE
        ```
    - 來源: 
        [docker logs mbind: Operation not permitted](https://github.com/docker-library/mysql/issues/303)

以上就是我在對接 MySQL 時所遇到的問題
在官方文件中，還有很多部分是我這邊沒有提到的，後續如果有時間會在繼續研究
