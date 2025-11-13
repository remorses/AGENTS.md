# prisma

this project uses prisma to interact with the database. if you need to add new queries always read the schema.prisma inside the db folder first so you understand the shape of the tables in the database.

never add new tables to the prisma schema, instead ask me to do so.

prisma upsert calls are preferable over updates, so that you also handle the case where the row is missing.

never make changes to schema.prisma yourself, instead propose a change with a message and ask me to do it. this file is too important to be edited by agents.

NEVER run `pnpm push` in db or commands like `pnpm prisma db push` or other prisma commands that mutate the database!

### prisma queries for relations

- NEVER add more than 1 include nesting. this is very bad for performance because prisma will have to do the query to get the relation sequentially. instead of adding a new nested `include` you should add a new prisma query and wrap them in a `Promise.all`

### prisma transactions for complex relations inserts

for very complex updates or inserts that involve more than 3 related tables, for example a Chat with ChatMessages and ChatMessagePath, you should use transaction instead of a super complex single query:

- start a transaction
- delete the parent table, the one with cascade deletes, so that the related tables are also deleted
- recreate all the tables again, reuse the old existing rows data when you don't have all the fields available
- make sure to create all the rows in the related tables. use for loops if necessary

### prisma, always make sure user has access to prisma tables

> IMPORTANT! always read the schema.prisma file before adding a new prisma query, to understand how to structure it

try to never write sql by hand, use prisma

if a query becomes too complex because fetching too deeply into related tables (more than 1 `include` nesting), use different queries instead, put them in a Promise.all

### prisma, concurrency

when doing prisma queries or other async operations try to parallelize them using Promise.all

this will speed up operations that can be done concurrently.

this is especially important in react-router loaders

### prisma security

all loaders, actions and spiceflow routes of the project should have authorization checks.

these checks should check that the current user, identified by userId, has access to the fetched and updated rows.

this simply means to always include a check in prisma queries to make sure that the user has access to the updated or queried rows, for example:

```typescript
const resource = await prisma.resource.findFirst({
  where: { resourceId, parentResource: { users: { some: { userId } } } },
});
if (!resource) {
  throw new AppError(`cannot find resource`);
}
```

### prisma transactions

NEVER use prisma interactive transactions (passing a function to `prisma.$transaction`), instead pass an array of operations. this is basically the same thing, operations are executed in order, but it has much better performance.

if you need to use complex logic to construct the array of operations, create an empty array using `const operations: Prisma.PrismaPromise<any>[]` first, then push to this array the queries you want to execute

> IMPORTANT! while constructing the operations array you should never call await in between, this would cause the prisma query to start and would make the transaction invalid.

````typescript

## errors

if you throw an error that is not unexpected you should use the `AppError` class, this way I can skip sending these errors to Sentry in the `notifyError` function

for example for cases where a resource is not found or user has no subscription.

you can even throw response errors, for example:

```typescript
if (!user.subscription) {
    throw new ResponseError(
        403,
        JSON.stringify({ message: `user has no subscription` }),
    )
}
````

## foreign key constraints

sometimes you will get errors like "Invalid `upsert()` invocation: Foreign key constraint violated on the constraint: `filed1_filed2_fkey`". This can be caused by the following issue

- a field that has a relation to table X is being passed a value where no table X exists for that id. You can fix the issue by making sure that the table exists before doing the create or upsert
- With upsert, even if the create branch is valid, the update branch can violate the FK.

```ts
await prisma.child.upsert({
  where: { id: 1 },
  create: {
    parent: { connect: { id: 1 } },
  },
  update: {
    parent: { connect: { id: 9999 } }, // no such parent
  },
});
```
-
