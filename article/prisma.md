---

title: Prisma

date: 2025-09-03

summary: 现代化的 TypeScript ORM，类型安全的数据库访问与迁移工具链。

image: https://picgo-elaina.oss-cn-chengdu.aliyuncs.com/typora/202507280817038.jpg

---

# Prisma 速览：下一代 Node.js & TypeScript ORM

Prisma 是一个现代化的开源数据库工具集，旨在彻底改善开发者与数据库的交互方式。它并非传统的 ORM（对象关系映射），而是一个包含了多个强大工具的综合解决方案，其核心组件包括：

  - **Prisma Client**：一个自动生成且类型安全的查询构建器，让你能用直观的、可自动补全的 API 来操作数据库。
  - **Prisma Migrate**：一个声明式的数据库迁移工具，通过 `schema.prisma` 文件管理数据库结构，让团队协作和版本控制变得简单。
  - **Prisma Studio**：一个现代化的 GUI，用于查看和编辑本地数据库中的数据。

## 1\. Prisma Schema：单一数据源真相

所有 Prisma 的配置都始于 `schema.prisma` 文件。这个文件是您数据库模型的**唯一真实来源 (Single Source of Truth)**，采用 Prisma 定义语言 (Prisma Definition Language, PDL) 编写。

它主要由三部分构成：

1.  **Datasource**：指定数据库连接信息，如数据库类型 (PostgreSQL, MySQL, SQLite 等) 和连接地址。
2.  **Generator**：配置需要生成的产物，最常见的就是 Prisma Client。
3.  **Model**：定义您的数据库表结构、字段以及表之间的关系。

### 示例 `schema.prisma`

```prisma
// 1. 数据源配置
datasource db {
  // 指定数据库类型为 PostgreSQL
  provider = "postgresql"
  // 从环境变量 DATABASE_URL 中读取数据库连接字符串
  url      = env("DATABASE_URL")
}

// 2. 生成器配置
generator client {
  // 指定要生成 Prisma Client for JavaScript/TypeScript
  provider = "prisma-client-js"
}

// 3. 数据模型定义 (对应数据库中的 "User" 表)
model User {
  // 'id' 字段，整数类型，是主键 (@id)，且自增 (@default(autoincrement()))
  id    Int     @id @default(autoincrement())
  // 'email' 字段，字符串类型，值必须唯一 (@unique)
  email String  @unique
  // 'name' 字段，字符串类型，可以为空 (?)
  name  String?
  // 'posts' 字段，是一个关系字段，表示一个用户可以拥有多篇 Post
  posts Post[]
}

// 定义 "Post" 表
model Post {
  id        Int      @id @default(autoincrement())
  title     String
  content   String?
  published Boolean  @default(false)
  // 'author' 字段，是一个关系字段，表示一篇文章属于一个 User
  author    User     @relation(fields: [authorId], references: [id])
  // 'authorId' 是一个外键，与 User 表的 id 字段关联
  authorId  Int
}
```

## 2\. Prisma Client：类型安全的数据库交互

在定义好 `schema.prisma` 后，通过 `npx prisma generate` 命令，Prisma 会自动生成一个完全类型安全的数据库客户端。这个客户端懂得你的数据模型，能为所有查询操作提供精准的 TypeScript 类型提示和自动补全。

### 常用操作示例

```ts
// 导入自动生成的 PrismaClient 构造函数
import { PrismaClient } from '@prisma/client';

// 实例化 Prisma Client
const prisma = new PrismaClient();

/**
 * 主函数，用于演示 Prisma Client 的各种操作
 */
async function main() {
  // --- 创建 (Create) ---
  // 创建一个新用户，data 对象结构会得到类型提示
  const newUser = await prisma.user.create({
    data: {
      email: 'elaina@wanderer.com',
      name: 'Elaina',
    },
  });
  console.log('创建的用户:', newUser);

  // --- 查询 (Read) ---
  // 查询所有用户
  const allUsers = await prisma.user.findMany();
  console.log('所有用户:', allUsers);

  // 查询单个用户，并包含其关联的文章 (posts)
  const userWithPosts = await prisma.user.findUnique({
    // 查询条件，根据唯一的 email 字段查找
    where: { email: 'elaina@wanderer.com' },
    // 使用 include 关键字来加载关联数据
    include: { posts: true },
  });
  console.log('带文章的用户:', userWithPosts);

  // --- 更新 (Update) ---
  // 更新指定用户的数据
  const updatedUser = await prisma.user.update({
    where: { email: 'elaina@wanderer.com' },
    data: { name: '魔女伊蕾娜' },
  });
  console.log('更新后的用户:', updatedUser);

  // --- 删除 (Delete) ---
  // 删除指定用户
  const deletedUser = await prisma.user.delete({
    where: { email: 'elaina@wanderer.com' },
  });
  console.log('删除的用户:', deletedUser);
}

// 执行主函数并处理可能的错误
main()
  .catch((e) => {
    // 捕获并打印错误
    console.error(e);
  })
  .finally(async () => {
    // 最后确保断开数据库连接
    await prisma.$disconnect();
  });
```

## 3\. Prisma Migrate：声明式数据库迁移

Prisma Migrate 让数据库结构变更变得安全、可预测且易于团队协作。您不再需要手动编写复杂的 SQL 迁移脚本。

### 迁移工作流

1.  **修改 Schema**：在 `schema.prisma` 文件中添加、修改或删除模型。
2.  **生成迁移文件**：在终端运行 `npx prisma migrate dev --name <migration_name>`。
      - Prisma 会比较您的 `schema.prisma` 与数据库当前状态。
      - 自动生成一个包含相应 SQL 语句的迁移文件，并存储在 `prisma/migrations` 目录下。
      - 将生成的迁移应用到您的开发数据库。
3.  **应用到生产**：在生产环境中，运行 `npx prisma migrate deploy` 来安全地应用所有待处理的迁移。

这种方式使得每一次数据库结构的变更都有据可查，并且可以轻松地在不同环境（开发、测试、生产）之间同步。

## 为什么选择 Prisma

  - **端到端类型安全**：从数据库到应用程序代码，享受无缝的类型安全。自动生成的类型可以捕获大量潜在错误，例如拼写错误的字段名或错误的数据类型。
  - **卓越的开发体验**：强大的自动补全功能极大地提高了编码效率，让你不再需要频繁查阅数据库文档或表结构。
  - **可读性与可维护性**：Prisma Client 的 API 设计得非常直观，查询逻辑清晰易懂，代码更易于维护和重构。
  - **声明式数据建模**：`schema.prisma` 文件提供了一个清晰、统一的地方来定义数据模型和关系，使得团队协作更加顺畅，并且易于进行版本控制。
  - **安全的迁移系统**：Prisma Migrate 自动生成迁移文件，降低了手动编写 SQL 脚本出错的风险，保证了数据库结构变更的一致性和可追溯性。