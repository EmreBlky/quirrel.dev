# Migration `20200913090437-rename-stripe-customer-id-to-id`

This migration has been generated by Simon Knott at 9/13/2020, 11:04:37 AM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
CREATE TABLE "public"."User" (
"createdAt" timestamp(3)   NOT NULL DEFAULT CURRENT_TIMESTAMP,
"updatedAt" timestamp(3)   NOT NULL ,
"email" text   NOT NULL ,
"hashedPassword" text   ,
"id" text   NOT NULL ,
"subscriptionId" text   ,
"defaultPaymentMethodId" text   ,
PRIMARY KEY ("id")
)

CREATE TABLE "public"."Session" (
"id" SERIAL,
"createdAt" timestamp(3)   NOT NULL DEFAULT CURRENT_TIMESTAMP,
"updatedAt" timestamp(3)   NOT NULL ,
"expiresAt" timestamp(3)   ,
"handle" text   NOT NULL ,
"userId" text   ,
"hashedSessionToken" text   ,
"antiCSRFToken" text   ,
"publicData" text   ,
"privateData" text   ,
PRIMARY KEY ("id")
)

CREATE TABLE "public"."Project" (
"slug" text   NOT NULL ,
"createdAt" timestamp(3)   NOT NULL DEFAULT CURRENT_TIMESTAMP,
"ownerId" text   NOT NULL ,
PRIMARY KEY ("slug","ownerId")
)

CREATE TABLE "public"."Token" (
"projectSlug" text   NOT NULL ,
"projectOwnerId" text   NOT NULL ,
"name" text   NOT NULL ,
PRIMARY KEY ("name","projectSlug","projectOwnerId")
)

CREATE UNIQUE INDEX "User.email_unique" ON "public"."User"("email")

CREATE UNIQUE INDEX "User.id_unique" ON "public"."User"("id")

CREATE UNIQUE INDEX "User.subscriptionId_unique" ON "public"."User"("subscriptionId")

CREATE UNIQUE INDEX "User.defaultPaymentMethodId_unique" ON "public"."User"("defaultPaymentMethodId")

CREATE UNIQUE INDEX "Session.handle_unique" ON "public"."Session"("handle")

ALTER TABLE "public"."Session" ADD FOREIGN KEY ("userId")REFERENCES "public"."User"("id") ON DELETE SET NULL ON UPDATE CASCADE

ALTER TABLE "public"."Project" ADD FOREIGN KEY ("ownerId")REFERENCES "public"."User"("id") ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE "public"."Token" ADD FOREIGN KEY ("projectSlug", "projectOwnerId")REFERENCES "public"."Project"("slug","ownerId") ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE "public"."Token" ADD FOREIGN KEY ("projectOwnerId")REFERENCES "public"."User"("id") ON DELETE CASCADE ON UPDATE CASCADE
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration ..20200913090437-rename-stripe-customer-id-to-id
--- datamodel.dml
+++ datamodel.dml
@@ -1,0 +1,61 @@
+// This is your Prisma schema file,
+// learn more about it in the docs: https://pris.ly/d/prisma-schema
+
+datasource db {
+  provider = "postgres"
+  url = "***"
+}
+
+generator client {
+  provider = "prisma-client-js"
+}
+
+// --------------------------------------
+
+model User {
+  createdAt      DateTime  @default(now())
+  updatedAt      DateTime  @updatedAt
+  email          String    @unique
+  hashedPassword String?
+  sessions       Session[]
+
+  id String @unique @id
+  subscriptionId String? @unique
+  defaultPaymentMethodId String? @unique
+
+  projects Project[]
+}
+
+model Session {
+  id                 Int       @default(autoincrement()) @id
+  createdAt          DateTime  @default(now())
+  updatedAt          DateTime  @updatedAt
+  expiresAt          DateTime?
+  handle             String    @unique
+  user               User? @relation(fields: [userId], references: [id])
+  userId             String?
+  hashedSessionToken String?
+  antiCSRFToken      String?
+  publicData         String?
+  privateData        String?
+}
+
+model Project {
+  slug      String
+  createdAt DateTime @default(now())
+  owner     User     @relation(fields: [ownerId], references: [id])
+  ownerId   String
+  tokens    Token[]
+
+  @@id([slug, ownerId])
+}
+
+model Token {
+  project        Project @relation(fields: [projectSlug, projectOwnerId], references: [slug, ownerId])
+  projectSlug    String
+  projectOwner   User @relation(fields: [projectOwnerId], references: [id])
+  projectOwnerId String
+  name           String
+
+  @@id([name, projectSlug, projectOwnerId])
+}
```

