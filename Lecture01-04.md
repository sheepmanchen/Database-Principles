# Mid-term Review for Databases
# Lecture01
## Databases mostly have two purposes:
	1. CONTROL changes
	2. RETRIEVE data

## Short Introduction
In 1970, Ted Todd came out with the relational theory.
Idea: Store data into *tables* known as *“relations”*
> Column: each stores a piece of data.  
> Row: each represents a fact.  
> All the pieces of data in a row are related, hence “relational”.

**Codd’s Big Idea**

* **Relations** are **sets**.
* We can **operate** on them, and get new sets.
* select / project / join (three most famous operations)

**Tables are variables (sort of)**

* We can change their content, combine them in complex operations to obtain new result, (like what we do with variables)
* However, obey rules (cannot multiple)

## Modelling
1. ORDER of rows and columns doesn’t matter
> What matters is that all the data in one row relates to the same film, and that data in a column matches the corresponding header.  
2. Duplicates are forbidden in relational tables.
  For example, would be the risk of getting wrong results when counting films by country
3. Need to **IDENTIFY** what to **differentiate** one row from another
  * May be one column, or one set of columns, “**KEY**”


### Normalization
Each column should only contain ONE piece of information


### Directors Table
First name and surname **uniquely** identify someone. 

Can also add a numerical id, and make it the **primary key**.

Intermediate table: Directed_By:  Stores the link

* Need to merge directors and actors to one table: People
* Because with actors and directors, if update, have risk missing one and having inconsistencies

### Credits

To tell in which capacity a person intervened in a film.
### Too many things to consider
* need to set limits somewhere


* must also anticipate future needs
* Making the right choices according to different circumstances

## Entity & Relationship
Entities: people, films, countries, etc.
Relationship: **connect entities**, they have no life of their own.
#### E/R Diagram
A way to represent tables in a database.
#### Cardinality
(1, n)    country - movie
(0, n)    main language for movies
(m, n)    many-to-many relationship. Implies a relationship table. 

### Normalization
All about splitting

1. Simple attributes
2. Attributes depend on the **full key**
  Bad: column as Country (one of the composite key) and Continent (not …)
3. Non-key attributes not dependent on each other
  Bad: Currency and Symbol

>  Every non key attribute must provide a fact about the key, the whole key, and nothing but the key.  




# Lecture02
> Manage data in a database - Special language SQL 

* select… from… where…
  names of the columns you want to return, name of the tables that you query, filtering conditions

## Two main components
Codd: a good database language should allow to deal as easily with contents (data) as containers (tables), something that SQL does reasonably well.

### Data Definition Language (DDL)
Deals with *tables*
CREATE/ALTER/DROP
	* commands are enough fro creating a new table, changing its structure (adding a new column) or deleting it.
### Data Manipulation Language (DML)
Deals with *data*
INSERT/UPDATE/DELETE/SELECT

## Text datatypes
char(*length*) 
char 
varchar(*max length*)
varchar2(*max length*)
text  clob

> char is same as char(1)  
> char() is for fixed-size columns(data is padded with spaces if shorter). Used for codes.  
> an empty varchar2() is the same as nothing, not an empty varchar(). varchars don’t pad.  

## Number datatypes
int
float
numeric (*precision,* *scale*)
number (*precision*, *scale*)
> Oracle knows mostly one number datatype, NUMBER, which can optionally take a precision (number of digits) and a scale (number of these digits after the decimal point). Something equivalent is called NUMERIC (sometimes DECIMAL) with other products, but other products also use INT and FLOAT far more.  

### `CREATE`
```sql
create table people (
peopleid int not null,
first_name varchar(30),
surname varchar(30) not null,
born numeric(4) not null,
died numeric(4)) 
```

> One important concept in relational databases is the concept of  “nothingness", represented in SQL by something called NULL, which isn't a value.   
> not null:  NULL isn't acceptable for this column  


## CONSTRAINTS
Constraints are declarative rules that the DBMS will check every time new data will be added, when data is changed, or even when data is deleted, in order to prevent any inconsistency. Any operation that violates a constraint fails and returns an error.

`PRIMARY KEY` tells which is the main key for the table, and indicates two things:

1) that the value is **mandatory** (the additional **NOT NULL** doesn't hurt but is **redundant**), and

2) that the values are **unique** (no duplicates allowed in the column)

So far, nothing would prevent us from entering two Audrey Hepburns with different ids, add the last line.

```sql
create table people (
peopleid int not null,
				primary key,
first_name varchar(30),
surname varchar(30) not null,
born numeric(4) not null,
died numeric(4),
	unique (first_name, surname)) 
```

Still, some products is case sensitive, need to normalization or CHECK
`check (year_released >= 1895)`

## Referential Integrity
better than CHECK, example: country code
Known as a reference table: a country that stores all country codes, and corresponding country names, with the code as primary key (and the country name declared as unique). We'll only accept a country code if we find it in this table (which can be modified, with new codes added)

```sql
create table movies (movieid int not null primary key,
					title varchar(60) not null,
					country char(2) not null,
					year_released numeric(4) not null
					 check(year_released >= 1895), 
				unique (title, country, year_released),
					foreign key(country) 
						references contries(country_code))
```

> **Only primary keys and columns declared as UNIQUE** can be **referenced**.   

> A foreign key can be composed of a combination of columns (rare).  
> In summary, creating tables requires:

1. Proper modeling (cardinalities)
2. Defining keys (what identifies rows)
3. Determining correct data types
4. Defining constraints

### `INSERT`
Basic syntax

```sql
insert into table_name
	(list of columns)
values (list of values)
```

* Values must match column-names one by one.
* What happens if you omit a column name from the list? Nothing is entered into it. If the column is mandatory, the INSERT statement fails and nothing at all is done.
  Example:

```sql
insert into countries(country_code,
country_name, continent)
values('us', 'United States', 'AMERICA')
```

#### Entering a date
Two ways:
	1. As a string
	2. As the result of a function or computation

* Important things to remember about SQL and databases.

> SQL : THE database language.  
> Connection : server, port, database, username, password.   
> Create table: data types + constraints.  
> Keywords and identifiers: not case-sensitive  
> Data : can be CASE-SENSITIVE  

## SELECT
Select * displays the full content of the table and is a bit like printing the table variable.

* *number*
* *’constant’*
* *column*
> where title = Jaws  *Column doesn’t exist!*  
> where title = 'Jaws'  
* Must single quoted. If they aren't quoted, they will be interpreted as column names. 

Note that a filtering condition returns a subset. If you return all the columns from a table without duplicates, it won't contain duplicates either and will be a valid "relation".

#### and / or / not
* and > or 先执行and再执行or
* where country = 'us' or 
  ~country = 'gb'~
  ~and year_released between 1940 and 1949~
* where （country = 'us' or country = 'gb’）
  and year_released between 1940 and 1949
* BETWEEN 1949 AND 1940 might look equivalent to you but would return no rows.
* country not in ('us', 'gb')
  两个都不在。

#### <> / !=

#### like %   _
1. % meaning "any number of characters, including none" 
2. _ meaning "one and only one character"
```sql
select * from movies
where title not like ‘%A%’
  and title not like ‘%a%’
```
This expression for instance returns films the title of which doesn't contain any A. This A might be the first or last character as well.



# Lecture03

## null

~~where column_name = null~~

~~Much wow~~

~~where column_name <> null~~
when column_name is (not) null
## transform data
`‘hello’ || ‘world’`
Example (give the “virtual column” a name *movie_release*
```sql
select title || ' was released in' 
				|| year_released  movie_release
from movies
where country = 'us'
```

`case… end`
```sql
case upper(color)
	when ‘Y’ then ‘Color’
	when ’N’ then ‘B&W’
	else    ‘?’
end as color,
…
```
If no match is found and there is no ELSE, the SQL engine doesn't know what to return, and returns NULL.
~~when null then~~
```sql
case
	when died is null then
			'alive and kicking'
	else 'passed away'
end as status
```
This can also be used for conditions involving two columns from the same row, such as testing whether COL_A contains a value greater than the one in COL_B.

## Some useful functions
round (3.141592, 3)    			3.142
trunk   (3.141592, 3)    		3.141
upper (),		lower () 
substr ('Citizen Kane', 5, 3)  	 zen
trim (' Oops ')				'Oops'
replace ('Sheep', 'ee', 'i')		’Ship’
length()

## Refine Queries
`select country from movies`
* may obtaining a table, but it’s not a relation because rows cannot be distinguished
  `select distinct country from movies`

`Aggregate Functions`
* aggregate function will aggregate all rows that share a feature (such as being films from the same country)
  `group by`
```sql
select country,
		 count(*) number_of_movies
from movies
group by country
```
* Every column that isn't an aggregate function and appears after SELECT must also appear after GROUP BY.
* it is perfectly valid to have nothing other than one (or several) aggregate functions after SELECT, in which case no GROUP BY is required:
  `select count(*) number_of_movies from movies`
  count(*) / count(col)
  in(col)		max(col)
  vg(col)		sum(col)
* Aggregate functions ignore nulls
```sql
 select max(died) most_recent_death
 from people
 where died is not null (redundant)
```

```sql
select count(*) number_of_acting_directors
from(
select peopleid, count(*) as number_of_roles 	from (
	select distinct
	peopleid,
	credited_as from credits
	where credited_as
	in ('A', 'D')
	)
group by peopleid
having count(*) = 2)
```

`JOIN`
In practice, ~most~ joins will link tables through foreign key constraints.
However, this is *no obligation*: relational joins are only driven by values.
```sql
select title, 
	country_name,
	year_released 
from movies
          join countries
		on country_code = country
where country_code <> 'us'
```
## Self-Join
[![https://gyazo.com/2b3d06921b6c44d75c2c4c231d552245](https://i.gyazo.com/2b3d06921b6c44d75c2c4c231d552245.png)](https://gyazo.com/2b3d06921b6c44d75c2c4c231d552245)


# Lecture04

## outer join

An outer join tells to complete the result set with NULLs if we can't find a match in the outer joined table.
```sql
select c.country_name,
case 
		when x.number_of_movies is null then 0
		else x.number_of_movies
end as number_of_movies
 from countries c
      left outer join
      (select country as country_code,
              count(*) as number_of_movies
       from movies
       group by country) x
           on x.country_code = c.country_code
```

```sql
select c.country_name,
       coalesce(x.number_of_movies, 0)
                             number_of_movies
from countries c
     left outer join
     (select country as country_code,
             count(*) as number_of_movies
      from movies
      group by country) x
          on x.country_code = c.country_code
```
* COALESCE() function (available with all products) takes an indeterminate number of parameters and returns the first one that isn't NULL.

* If you want to see all entries from table1, table1 must come first with a left outer join.

* Q: British movie titles with director names when available?

  ![](https://ws2.sinaimg.cn/large/006tKfTcgy1fqrfxzloerj30hv0dcdit.jpg)

  ​

  ![](https://ws3.sinaimg.cn/large/006tKfTcly1fqrfywmzepj30hr0d577f.jpg)
  ![](https://ws4.sinaimg.cn/large/006tKfTcly1fqrfz7yjvwj30hp0d5772.jpg)

* The problem was this:
  Get movie titles and director name if available

* The query does this:
  Get movie titles and people involved, then display if director known or no people found
  ![](https://ws3.sinaimg.cn/large/006tKfTcly1fqrfzwgtfdj30hu0d5mzk.jpg)

  Summary: with LEFT OUTER JOINs, apply all conditions before joining

Joins:
1. Filtering
  筛选信息
2. Qualifying 
  Only to return additional information that make the result set more legible or intelligible
* unless finding a match is significant, because I might find only some values in the other table. 
* ~An outer join is always a qualifying join~, unless it is associated with an IS NULL condition, meaning that not finding a match is significant (for instance: countries for which no film is found in MOVIES)
  Distinguish:
  Remove Join, MORE rows?
* YES —> filtering
* NO —> qualifying
* Principle: 
  * filtering as soon as possible
  * qualifying later

## Set Operators
`UNION`
Requires two commonsensical conditions:
	1. must return the same number of columns

​	2. data types of corresponding columns must match
UNION eliminated duplicates

![](https://ws3.sinaimg.cn/large/006tKfTcly1fqrg0gz31bj30dr0a176f.jpg)

`UNION ALL`
Doesn’t mean that you want duplicates, it means that you know that there cannot be any duplicates between the two queries.



![](https://ws4.sinaimg.cn/large/006tKfTcly1fqrg0snwpjj30dl0a6tb9.jpg)

`INTERSECT`
Returns the common rows in two tables
`Except` `Minus`
Returns the rows from the first table, minus those that can also be found in the second table.
* intersect —> inner join 
* except —> outer join

## SUBQUERIES
## CORRELATION
When you run a join, you link tables through common values in one (sometimes several) column.
![](https://ws4.sinaimg.cn/large/006tKfTcly1fqrg1h56mzj30dq0a50t0.jpg)
```sql
select m.title, m.year_released,
       c.country_name
from movies m
     join countries c
       on c.country_code = m.country
where m.country <> 'us'
```
A subquery after the select:
```sql
select m.title, m.year_released,
		(select c.country_name
			from countries c
			where c.country_code = m.country) as country_name
from movies m
where m.country <> 'us'
```
The subquery is said to be CORRELATED with the outer (main) query.
* What happens if we don't find the country name ? (which won't happen here because of the foreign key)
  * The subquery would return nothing, also known as NULL
* A subquery after the SELECT is more equivalent to a LEFT OUTER JOIN.
* A subquery in the FROM cannot be correlated
### in (select col from… where…)
```sql
select country, year_released, title from movies
where country in
       (select country_code
        from countries
        where continent = 'EUROPE')
```


 IN () means an implicit DISTINCT in the 
subquery.

![screenshot](https://ws3.sinaimg.cn/large/006tKfTcly1fqrg2cg5e3j30dn0a8abt.jpg)
![screenshot](https://ws1.sinaimg.cn/large/006tKfTcly1fqrg2oi3xbj30dt0aaq41.jpg)

```sql
select * from people where born >= 1970
and first_name not in
		(select first_name
			from people
			where born < 1970
			and first_name is not null)
```

### Correlated in where
Used with:
`exists `
`not exists`
* NEVER try to correlate an IN()!

Example of where correlated:
```sql
select title from movies m
where exists 
(select null from credits c
inner join people p on c.peopleid = p.peopleid
where c.credited_as = 'A'
	and p.born >1970
	and c.movieid = m.movieid)
```

![screenshot](https://ws3.sinaimg.cn/large/006tKfTcly1fqrg2xnbqjj30sh0a7jv0.jpg)

Summary: we can switch correlated and uncorrelated subqueries, and turn them into joins.

## Relational Theory
* Relational operations are only concerned with retrieving data, a set, which is unordered.
* `order by col1 desc, col2 asc,...`
```sql
order by
      case credited_as
        when 'D' then 1
        when 'P' then 2
        when 'A' then 3
end
```



