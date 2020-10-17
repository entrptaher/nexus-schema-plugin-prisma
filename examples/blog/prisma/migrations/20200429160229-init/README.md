# Migration `20200429160229-init`

This migration has been generated by Jason Kuhrt at 4/29/2020, 4:02:29 PM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
CREATE TYPE "UserRole" AS ENUM ('ADMIN', 'AUTHOR');

CREATE TYPE "PostStatus" AS ENUM ('DRAFT', 'PUBLISHED');

CREATE TABLE "public"."Blog" (
    "createdAt" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,
    "id" SERIAL,
    "name" text  NOT NULL ,
    "updatedAt" timestamp(3)  NOT NULL ,
    "viewCount" integer  NOT NULL DEFAULT 0,
    PRIMARY KEY ("id")
) 

CREATE TABLE "public"."User" (
    "blogId" integer   ,
    "createdAt" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,
    "id" SERIAL,
    "name" text   ,
    "rating" Decimal(65,30)  NOT NULL ,
    "role" "UserRole" NOT NULL ,
    "updatedAt" timestamp(3)  NOT NULL ,
    PRIMARY KEY ("id")
) 

CREATE TABLE "public"."Post" (
    "blogId" integer   ,
    "id" SERIAL,
    "status" "PostStatus" NOT NULL ,
    "title" text  NOT NULL ,
    "userId" integer   ,
    PRIMARY KEY ("id")
) 

CREATE TABLE "public"."Tag" (
    "id" SERIAL,
    "postId" integer   ,
    "value" text  NOT NULL ,
    PRIMARY KEY ("id")
) 

ALTER TABLE "public"."User" ADD FOREIGN KEY ("blogId")REFERENCES "public"."Blog"("id") ON DELETE SET NULL  ON UPDATE CASCADE

ALTER TABLE "public"."Post" ADD FOREIGN KEY ("blogId")REFERENCES "public"."Blog"("id") ON DELETE SET NULL  ON UPDATE CASCADE

ALTER TABLE "public"."Post" ADD FOREIGN KEY ("userId")REFERENCES "public"."User"("id") ON DELETE SET NULL  ON UPDATE CASCADE

ALTER TABLE "public"."Tag" ADD FOREIGN KEY ("postId")REFERENCES "public"."Post"("id") ON DELETE SET NULL  ON UPDATE CASCADE
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration ..20200429160229-init
--- datamodel.dml
+++ datamodel.dml
@@ -1,0 +1,58 @@
+datasource db {
+  provider = "postgresql"
+  url      = "postgresql://postgres:postgres@localhost:5432/myapp"
+}
+
+generator prisma_client {
+  provider = "prisma-client-js"
+}
+
+model Blog {
+  id        Int      @default(autoincrement()) @id
+  createdAt DateTime @default(now())
+  updatedAt DateTime @updatedAt
+  name      String
+  viewCount Int      @default(0)
+  posts     Post[]
+  authors   User[]
+}
+
+model User {
+  id        Int      @default(autoincrement()) @id
+  createdAt DateTime @default(now())
+  updatedAt DateTime @updatedAt
+  name      String?
+  posts     Post[]
+  blogId    Int?
+  blog      Blog?    @relation(fields: [blogId], references: [id])
+  rating    Float
+  role      UserRole
+}
+
+enum UserRole {
+  ADMIN
+  AUTHOR
+}
+
+model Post {
+  id     Int        @default(autoincrement()) @id
+  title  String
+  tags   Tag[]
+  blogId Int?
+  blog   Blog?      @relation(fields: [blogId], references: [id])
+  status PostStatus
+  userId Int?
+  user   User?      @relation(fields: [userId], references: [id])
+}
+
+enum PostStatus {
+  DRAFT
+  PUBLISHED
+}
+
+model Tag {
+  id     Int    @default(autoincrement()) @id
+  value  String
+  postId Int?
+  post   Post?  @relation(fields: [postId], references: [id])
+}
```

