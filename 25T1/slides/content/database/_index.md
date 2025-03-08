+++
title = "API Design and Documentation"
outputs = ["Reveal"]
+++

# Database Fundamentals

`SELECT username, password FROM users;`

---

{{% section %}}

slides are at [tp.denzeliskandar.com/database](https://tp.denzeliskandar.com/database)

{{% /section %}}

---

{{% section %}}

## disclaimer

* i am not an expert in databases and prisma
  * there are also some things i dont know
  * i am still learning too
* i might still get things wrong (apologies in advance)
* im trying my best (real)

---

agenda: i will be introducing database concepts, talk a little bit about prisma, then we will design our schema together 🔥

{{% /section %}}

---

{{% section %}}

## what is a database?

* a structured collection of data
* allows for efficient storage, retrieval, and management of information
* provides a centralised and persistent way to store and access data

---

## what is a relational database?

* data is stored in tables with rows and columns
* tables have a defined schema with data types
* relationships can be established between tables
* queried using Structured Query Language (SQL)
* Examples: PostgreSQL, MySQL, Oracle, SQL Server

---

## relational database concepts

---

### tables

* a table is a collection of related data stored in rows and columns
* columns define the structure of the data, with specific data types
* rows represent individual records or instances of data

---

### primary keys

* a primary key is a column (or set of columns) that uniquely identifies each row in a table
* primary keys must be unique and cannot be null
* used to establish relationships between tables

---

### foreign keys

* a foreign key is a column (or set of columns) in one table that refers to the primary key of another table
* foreign keys are used to create relationships between tables
* allows you to link data across multiple tables

---

### relationships

* one-to-one: a single row in one table is related to a single row in another table (e.g. student and zid)
* one-to-many: a single row in one table is related to multiple rows in another table (e.g. customer and sales purchases)
* many-to-many: multiple rows in one table are related to multiple rows in another table (e.g. students and courses taken)

{{% /section %}}

---

{{% section %}}

## sql basics

---

### select

`SELECT` statement is used to retrieve data from a table

```sql
SELECT * FROM users;
SELECT name, email FROM users;
SELECT * FROM users WHERE id = 1;
SELECT * FROM users ORDER BY name DESC;
```

---

### insert

`INSERT` statement is used to add new data to a table

```sql
INSERT INTO users (name, email)
VALUES ('John Doe', 'john@example.com');
```

---

### update

`UPDATE` statement is used to modify existing data in a table

```sql
UPDATE users
SET email = 'jane.doe@example.com'
WHERE id = 2;
```

---

### delete

`DELETE` statement is used to remove data from a table

```sql
DELETE FROM users
WHERE id = 3;
```

---

### join

JOINs are used to combine rows from two or more tables based on a related column between them

```sql
SELECT u.name, p.title
FROM users u
JOIN posts p ON u.id = p.user_id;
```

there are outer joins and inner joins (and so much more) but i never care enough to differentiate between them

{{% /section %}}

---

{{% section %}}

## advanced sql concepts

---

### aggregations

aggregate functions like `COUNT`, `SUM`, `AVG`, `MIN`, and `MAX` can be used to perform calculations on multiple rows

```sql
SELECT COUNT(*) as total_users FROM users;
SELECT SUM(salary) as total_salary FROM employees;
SELECT AVG(age) as average_age FROM customers;
```

---

### Subqueries

subquery is a query nested inside another query; subqueries can be used in the `WHERE`, `SELECT`, or `FROM` clauses

```sql
SELECT name, email
FROM users
WHERE id IN (
  SELECT user_id
  FROM posts
  WHERE created_at > '2022-01-01'
);
```

---

### other database concepts

there are things such as
* ER modelling
* PLpgSQL
* triggers
* normalisation
* transactions
* serialisability
* concurrency

do COMP3311! highly recommended

{{% /section %}}

---

{{% section %}}

### database schemas

* blueprint that defines the structure of a database, including tables, columns, data types, constraints, and relationships
* helps maintain data consistency and integrity
* facilitates database administration and maintenance

{{% /section %}}

---

{{% section %}}

## introducing prisma

---

### what is prisma?

* a database toolkit
* provides an abstraction layer over the database
* offers a type-safe, auto-generated API for interacting with the database
* supports multiple database engines (PostgreSQL, MySQL, SQLite, MongoDB)

---

### using prisma with postgresql

Define Prisma Schema:
```
model User {
    id    String    @id @default(uuid())
    name  String
    email String    @unique
    posts Post[]
}

model Post {
  id       String @id @default(uuid())
  title    String
  content  String
  author   User   @relation(fields: [authorId], references: [id])
  authorId String
}
```

---

Generate the Prisma Client
```sh
npx prisma generate
```

---

Use the Prisma Client in our code:
```typescript
import { PrismaClient } from "@prisma/client";
const prisma = new PrismaClient();

const user = await prisma.user.findUnique({
  where: { email: 'john@example.com' },
  include: { posts: true }
})
```

{{% /section %}}

---

{{% section %}}

## case study

as in just looking at some of my code lol

---

### modelling one-to-many relationship

```
model Researcher {
    id                     String          @id @default(uuid())
    name                   String
    institution            String
    email                  String          @unique
    username               String          @unique
    password               String
    remainingLoginAttempts Int             @default(3)
    tokens                 Token[]
    patients               Patient[]
    dataAccessLogs         DataAccessLog[]
}
```

---

and... what does the Patient model look like?

```
model Patient {
    id             String          @id @default(uuid())
    name           String
    dateOfBirth    DateTime        @db.Date
    sex            Sex
    diagnosticInfo String
    treatmentInfo  String
    researcherId   String
    researcher     Researcher      @relation(fields: [researcherId], references: [id])
    genomicData    GenomicData[]
    phenotypes     PhenotypeData[]
    imagingData    ImagingData[]
    signalData     SignalData[]
    categories     CategoryType[]
}
```

---

### enums

```
enum DataQuality {
    UNKNOWN
    LOW
    MEDIUM
    HIGH
}
```

pre-defined content

```
model Data {
    ...
    quality       DataQuality     @default(UNKNOWN)
    ...
}
```

---

### before i continue to queries...

any questions?

---

### using it in our code

```ts
export async function getPatientFromId(patientId: string) {
  return await prisma.patient.findUnique({
    where: {
      id: patientId
    }
  });
}
```

for optimisation, it is always wise to use `findUnique` instead of `findMany` when we are searching on a `@unique` or `@id` fields

---

### creating data

```ts
const researcher = await getResearcherById(researcherId);
if (researcher === null) return null; // to keep typescript quiet

await prisma.patient.create({
    data: {
        name: name,
        dateOfBirth: dateOfBirth,
        sex: sex,
        diagnosticInfo: diagnosticInfo,
        treatmentInfo: treatmentInfo,
        categories: categories,
        researcher: {
            connect: researcher
        }
    }
});
```

---

### include

```ts
await prisma.patient.findUnique({
    where: {
        id: patientId
    },
    include: {
        genomicData: true,
        phenotypes: true,
        imagingData: true,
        signalData: true
    }
});
```

prisma already includes some default fields...

---

### select

```ts
await prisma.patient.findUnique({
    where: {
        id: patientId
    },
    select: {
        id: true,
        name: true,
        dateOfBirth: true,
        sex: true,
        diagnosticInfo: true,
        treatmentInfo: true,
        genomicData: true,
        phenotypes: true,
        imagingData: true,
        signalData: true,
        researcherId: true,
        categories: true
    }
});
```

full control

---

### updating data

```ts
await prisma.patient.update({
    where: {
        id: patientId
    },
    data: patientData,
    include: {
        genomicData: true,
        phenotypes: true,
        imagingData: true,
        signalData: true,
    }
});
```

prisma is smart enough to know what to update

---

### find many (arrays)

```ts
const phenotypes = await prisma.phenotypeData.findMany({
    // select or include and where whatever
});
```

you might find map useful

```ts
return phenotypes.map(phenotype => ({
    id: phenotype.id,
    name: phenotype.name,
    description: phenotype.description,
    traits: phenotype.traits,
    categories: phenotype.categories,
    researcherId: phenotype.patient.researcher.id,
    researcherEmail: phenotype.patient.researcher.email
}));
```

---

### searches

```ts
await prisma.patient.findMany({
    where: {
        OR: [
            {
                sex: {
                    equals: searchTerm.toUpperCase() as any,
                }
            },
            {
                diagnosticInfo: {
                    contains: searchTerm,
                    mode: "insensitive"
                }
            },
            {
                treatmentInfo: {
                    contains: searchTerm,
                    mode: "insensitive"
                }
            },
            {
                categories: {
                    has: searchTerm.toUpperCase() as any
                }
            }
        ]
    }
});
```

not the most aesthetic looking piece of code but it gets the job done 🔥

{{% /section %}}

---

{{% section %}}

## prisma best practices

---

### schema design

* model your data accurately
* use appropriate data types
* leverage relationships between entities

---

### query optimisation

* use the right query type (e.g., `findUnique` vs. `findMany`)
* leverage eager loading with `include` and `select`

---

### error handling

* catch and handle Prisma client errors
* provide meaningful error messages to your users
* log errors for debugging and monitoring

---

### security and permissions

* implement role-based access control (RBAC)
* avoid exposing sensitive data in your API

{{% /section %}}

---

## designing a database

time to think (i know, sad)
