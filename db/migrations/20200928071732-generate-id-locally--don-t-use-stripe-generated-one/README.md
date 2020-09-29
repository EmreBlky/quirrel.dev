# Migration `20200928071732-generate-id-locally--don-t-use-stripe-generated-one`

This migration has been generated by Simon Knott at 9/28/2020, 9:17:32 AM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql

```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration 20200914124113-add-email-is-verified-field..20200928071732-generate-id-locally--don-t-use-stripe-generated-one
--- datamodel.dml
+++ datamodel.dml
@@ -2,9 +2,9 @@
 // learn more about it in the docs: https://pris.ly/d/prisma-schema
 datasource db {
   provider = "postgres"
-  url = "***"
+  url = "***"
 }
 generator client {
   provider = "prisma-client-js"
@@ -21,9 +21,9 @@
   sessions        Session[]
   isActive Boolean @default(true)
-  id                     String  @unique @id
+  id                     String  @unique @id @default(uuid())
   subscriptionId         String? @unique
   defaultPaymentMethodId String? @unique
   projects Project[]
```

