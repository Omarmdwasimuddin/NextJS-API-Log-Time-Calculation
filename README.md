## NextJS API---> Log, Time, Calculation


### Configure Prisma Logging
#### lib/prisma.ts
![](https://imgur.com/3g95Xkc.png)

```bash
import { PrismaClient } from "@/app/generated/prisma/client";
import { PrismaPg } from "@prisma/adapter-pg";

const globalForPrisma = global as unknown as { 
    prisma: PrismaClient;
 };

 const adapter = new PrismaPg({
    connectionString: process.env.DATABASE_URL,
 });

 const prisma = globalForPrisma.prisma || new PrismaClient({ adapter, log: ["query", "error", "info", "warn"] });

 if (process.env.NODE_ENV !== "production") globalForPrisma.prisma = prisma;

 export default prisma
```
---

### Calculate API Execution Time
![](https://imgur.com/2leGb3a.png)

```bash
import prisma from "@/lib/prisma";
import { NextRequest, NextResponse } from "next/server";


export async function GET(request: NextRequest) {
    try {

        const startTime = Date.now();
        const readData = await prisma.employee.findMany({
            skip: 1,
            take: 3,
        });
        const execTime = `${Date.now() - startTime} ms`;

        return NextResponse.json(
            {status: "success", executionTime: execTime, message: "Read data successfully", data: readData},
            {status: 200}
        )
    } catch (error) {
        return NextResponse.json(
            {status: "failed", message: "Internal server problem"},
            {status: 500}
        )
    }
}
```
---
