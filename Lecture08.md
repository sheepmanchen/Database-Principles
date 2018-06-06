# Lecture08

# Speeding Up

# INDEX

## IDEA

* Associating values in one or several columns of a table to the locator(s) of the row(s) where they can be found.

![](https://ws1.sinaimg.cn/large/006tKfTcly1fra1ej6t0xj30f905ewet.jpg)

* We build a sorted list of all the values with their locators.
  * For example, we may look by groups of columns, such as FIRST_NAME and SURNAME in the PEOPLE table, and want to index them as a combination).
* Indexed values are binary values.

![](https://ws1.sinaimg.cn/large/006tNc79ly1fs1gmp3b9tj30du0agq45.jpg)

## CREATE an INDEX

```sql
create index <index name>
on <table name> (<col1>, ... <coln>)

--Example:
create index coutries_cont_idx
on contries(continent)
```

Two columns often queried together can be indexed together; what is indexed is concatenated values (NOT separate values) 

```sql
create index people_surname_born_idx
on people(surname, born)
--composite index
```

Whenever you declare a **PRIMARY KEY** or **UNIQUE** <u>constraint</u>, an **index** is created behind your back. 

* It has nothing to do with <u>constraints</u> or the <u>relational theory</u> (with indexes, we are more talking engineering than theory), it's purely practical. 

![](https://ws1.sinaimg.cn/large/006tKfTcly1fra1un77stj30cw09rmxz.jpg)

### INSERT & DELETE

Always require maintaining **table** AND **indexes**.  

### UPDATE

Updating an indexed column isn't only changing its value. 

It requires moving things around in the tree, which is more painful work. 

The location is the same but the key has changed. 

### Storage

Indexes use a lot of storage, sometimes more than data! It has a huge impact on operations. 

### Unique Index

```sql
create unique index <index name> 
on <table name>(<col1>, ... <coln>)
```

Enforces unique constraint like a constraint definition 

Q: If both are equivalent, then which one should we use? 

Unique index or **Unique constraint** 

### Naming Conventions

IDX_MYTABLE_SOMECOL

MYTABLE_SOMECOL_IDX (sorting)

## Performance

If we need to retrieve MANY  rows, there will be a time when plainly scanning the table will be faster. 

**Don't index all column.** Every INSERT will have to write not only into the table but into every index.

![](https://ws2.sinaimg.cn/large/006tNc79gy1fs1h2g9b3xj30dn0aeabe.jpg)

**Q: INDEX WHICH ONES?**

* Search criteria often used. 

Typically, in table containing exchange rates, you would index by currency code and date, because one as little meaning without the other. 

* Another very important factor is that the column must be **SELECTIVE**, that means that the values it contains are **rare** and correspond to very few rows. **Unique** columns and **PK** are extremely selective by definition. Other columns are a mixed bag. 

![](https://ws4.sinaimg.cn/large/006tNc79ly1fs1h89emrkj30f10bgdhe.jpg)

### Execution Plan

`explain <select statement>`

SQLServer `set showplan_all on`

Many tools also provide GUI on execution plan.

The main benefit of execution plans it to check whether the optimizer is more or less doing what you thought it would do. 

In particular whether an index is used. The optimizer may choose not to use it. It may also be unable to use it. 

## Cases impossible to use index

1. composite indexes

   The key of which is cancatenated values from several columns. The index can only be used if the lead column(s) appear in the WHERE condition of the query.

2. function()

3. Case-sensitive DBMS

   ![](https://ws3.sinaimg.cn/large/006tNc79gy1fs1hqijwbuj30fx0c80ug.jpg)

4. implicit conversions

    Index search is based on ordering (inside the tree) and ordering is different with different datatypes. Convert datatypes, you break the ordering. 

## Function and Index

![](https://ws4.sinaimg.cn/large/006tNc79ly1fs1hw46nplj30d909ydgv.jpg)

"**Deterministic**" means that the same input will always generate the same output. 

upper(column_name)

soundex(column_name)
