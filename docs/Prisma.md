# Prisma

## 使用 `bunx prisma db pull` 拉取当前数据库的表结构


## 连接多个数据库

1. 在 `prisma` 目录下新建一个 `schema`目录, 然后根据需要的数据库配置多个`schema`文件

2. 在`schema.prisma` 文件中 注意添加 `output`

需要注意把生成目录 放在node_modules下, 否则会因为生成的文件是cjs格式导致报错

```prisma

generator client {
    provider      = "prisma-client-js"
    binaryTargets = ["native"]
    output = "../../node_modules/@prisma-db1/client"
}

datasource db {
    provider = "mysql"
    url      = env("BPM_PRO")
}
```

3. 拉取和生成的时候 需要添加参数

```bash
bunx prisma db pull --schema ./prisma/schema.prisma
bunx prisma generate --schema ./prisma/schema.prisma
```

4. 导出多个库

``` ts
import { PrismaClient as PrismaClient1 } from '@prisma-db1/client1'
import { PrismaClient as PrismaClient2 } from '@prisma-db2/client2'

const client1 = new PrismaClient1()
const client2 = new PrismaClient2()

export { client1, client2 };
```

## 打印SQL执行日志

```ts
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient({
  log: [{
        emit: 'event',
        level: 'query',
    }]
})

prisma.$on('query', (e) => {
    console.log('Query: ' + e.query)
    console.log('Params: ' + e.params)
    console.log('Duration: ' + e.duration + 'ms')
})

export default prisma
```