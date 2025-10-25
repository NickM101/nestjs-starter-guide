

# 🧠 **NestJS + Prisma + PostgreSQL Quickstart Guide**

> ⚡ A clean and easy guide to kickstart a **NestJS backend** with **Prisma ORM** and **PostgreSQL** — without the usual confusion.
> Ideal if you already understand frameworks but hate starting from scratch.

---

## 🏗️ **1. Create a New NestJS Project**

```bash
# 🧰 Install Nest CLI
npm i -g @nestjs/cli

# 🚀 Create your project
nest new nestjs-starter-guide

# 📂 Move into project directory
cd nestjs-starter-guide
```

🧩 When prompted, choose **npm** or **pnpm** (your choice).

---

## 🧱 **2. Install Prisma + PostgreSQL Client**

```bash
npm install prisma --save-dev
npm install @prisma/client
```

---

## 🧮 **3. Initialize Prisma**

```bash
npx prisma init
```

This will create a new folder called **`prisma/`** with a default `schema.prisma` file.
It also adds a `.env` file — you’ll define your database URL there.

---

## 🗃️ **4. Configure PostgreSQL Connection**

Edit your `.env` file:

```env
DATABASE_URL="postgresql://<username>:<password>@localhost:5432/<database_name>?schema=public"
```

💡 Example:

```env
DATABASE_URL="postgresql://postgres:mysecretpassword@localhost:5432/nest_prisma"
```

Make sure your PostgreSQL server is running locally or remotely.

---

## 🧩 **5. Define Your Prisma Schema**

Open `prisma/schema.prisma` and replace the contents with this 👇

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  createdAt DateTime @default(now())
}
```

---

## 🔄 **6. Run Prisma Migration**

```bash
npx prisma migrate dev --name init
```

✅ This will:

* Create your database tables
* Generate Prisma client
* Sync everything up

---

## ⚙️ **7. Add Prisma to Your NestJS App**

### Create Prisma Module and Service:

```bash
nest g module prisma
nest g service prisma
```

Then open `src/prisma/prisma.service.ts` and update it to:

```ts
import { Injectable, OnModuleInit, OnModuleDestroy } from '@nestjs/common';
import { PrismaClient } from '@prisma/client';

@Injectable()
export class PrismaService extends PrismaClient implements OnModuleInit, OnModuleDestroy {
  async onModuleInit() {
    await this.$connect();
  }

  async onModuleDestroy() {
    await this.$disconnect();
  }
}
```

---

## 👤 **8. Create User Module (Example CRUD)**

```bash
nest g resource user
```

When prompted:

* Choose **REST API**
* Choose **Yes** for CRUD entry points
* Choose **No** for GraphQL

Then open `src/user/user.service.ts` and replace contents with:

```ts
import { Injectable } from '@nestjs/common';
import { PrismaService } from '../prisma/prisma.service';
import { CreateUserDto, UpdateUserDto } from './dto';

@Injectable()
export class UserService {
  constructor(private prisma: PrismaService) {}

  create(data: CreateUserDto) {
    return this.prisma.user.create({ data });
  }

  findAll() {
    return this.prisma.user.findMany();
  }

  findOne(id: number) {
    return this.prisma.user.findUnique({ where: { id } });
  }

  update(id: number, data: UpdateUserDto) {
    return this.prisma.user.update({ where: { id }, data });
  }

  remove(id: number) {
    return this.prisma.user.delete({ where: { id } });
  }
}
```

---

## 🧩 **9. Inject PrismaService**

Open `src/user/user.module.ts` and ensure `PrismaModule` is imported:

```ts
import { Module } from '@nestjs/common';
import { UserService } from './user.service';
import { UserController } from './user.controller';
import { PrismaModule } from '../prisma/prisma.module';

@Module({
  imports: [PrismaModule],
  controllers: [UserController],
  providers: [UserService],
})
export class UserModule {}
```

---

## 🧪 **10. Run and Test Your API**

```bash
npm run start:dev
```

Your endpoints will be live at:

* `POST /user` → create
* `GET /user` → list all
* `GET /user/:id` → get one
* `PATCH /user/:id` → update
* `DELETE /user/:id` → delete

🎯 You can test them using **Postman**, **Insomnia**, or **curl**.

---

## 💡 **11. Extras & Tips**

✅ **Prisma Studio (GUI for DB):**

```bash
npx prisma studio
```

Runs a browser UI to view/edit data.

> URL: [http://localhost:5555](http://localhost:5555)

🪄 **Format Schema:**

```bash
npx prisma format
```

📦 **Generate New Prisma Client (after schema changes):**

```bash
npx prisma generate
```

---

## 🎉 **You’re Done!**

You now have a fully functional **NestJS + Prisma + PostgreSQL** backend:

* 🧱 Type-safe database access
* ⚡ Easy-to-extend architecture
* 🔥 Production-ready structure

---
