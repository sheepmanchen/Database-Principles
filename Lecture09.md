# Lecture09

Write queries to use index properly.

We have seen that joins and subqueries could be exchanged. But all writings haven't the same indexing implications. 

Primary Key --> **INDEX** <-- Unique constraint

![](https://ws3.sinaimg.cn/large/006tNc79gy1fs1jjy4tvgj30bm0903zl.jpg)

**UNIQUE (surname, first_name)**

## Order

A regular table (unordered, rows are inserted where room is found) is called a **Heap-organized table**.

A table that is ordered is calledL:

* Clustered index (SQLServer)
* Index-organized table (ORACLE)

why "index"? Because indexes are strongly ordered structures, and an ordered table follows the same pattern. 

In an ordered table, **row data replaces the row address**. 

### Benefits & Drawbacks

#### Benefits

1. The table is an index on a key, don't need to maintain any additional index on that key.
2. When walk the tree and find **data**, not an address (requiring an access to something else)
3. A range of values will be found in succession.

#### Drawbacks

1. If **update** the key, it's moving aound a whole row.

   This is why the **"key" ordering everything** is usually the **PK**, which isn't updated by definition (if you change the identifier, it's something different) 

2. If **insert** a row, it may involve shifting aroud a lot of bytes to make room.

### CASES

table: "tall" and "thin".

Tables that implement a many-to-many relationship may fall into this category.

Q: CREDIT table, index movies or people?

Index both! (constraint + additional index)

Order by the most important for you.

## VIEW

We have seen stored functions, but these functions are just returning numbers, strings or dates. Can we have relational functions, allowing to reuse relational operations as we would reuse code in a program? 

```sql
create view viewname (col1, ..., coln)
as
select ...
```

It's basically a named query.

Example:

```sql
create view vmovies
as select m.movieid,
          m.title,
          m.year_released,
          c.country_name
   from movies m
        inner join countries c
           on c.country_code = m.country
```

```sql
select *
from vmovies
where country_name = 'Italy'
```

**Permanent object**

**Permanent structure**

### Problems

awful function: full_name()

There is no way the index on (SURNAME, FIRST_NAME) can be used. We'll have to scan the full table, compute the function, and compare its result to the constant.  

**Views hide complexity**

### Scanlability

Computing power is always limited.

With complex views, more requests will be queued and will wait. 

### Security

Before have talked about **views** are useful in Reports/User Interface.

#### How is security managed in a database

Username/Password

####Privilege

**Data Control Language (DCL)**

`grant <right> to <account>`

`revoke <right> from <account>`

1. system rights give users DDL commands and change the structure of the database.
2. access and change the data

```sql
grant select, insert on tablename to accountname
And for users who have been naughty:
revoke privilege on tablename from accountname
```

#### Views & Security

The trick is to use a view that only shows what people are supposed to see, and grant SELECT on the view and not on the table.. 

If you simply drop and recreate it, you lose the privileges. Use CREATE OR REPLACE.

### CHANGING DATA Through VIEWS

Lots of things can go wrong.

EX: name split/update table CONUNTRIES rather than MOVIES

**In many cases, view update is simply impossible:**

Most joins 

Aggregates 

Expressions 

Omitted 

mandatory columns (insert) 

But sometimes it works very well.

## Data Dictionary (Catalog)

A good example of view application is the **set of tables** that contain **information** about the objects in the database.

**One catalog per database**

A database is an independent unit and you can have foreign keys only within (inside) one database; however, you can have several **schemas** in a database, and you can reference tables in another schema.  

**Read access to these tables is provided through system views.** 

`pg_... views`  PosrgreSQL

**INFORMATION_SCHEMA. TABLES**