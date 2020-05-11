---
title: "Tips to improve SQL Server Databases"
header:
  image: /assets/posts/en/tips-to-improve-sql-server-databases/header.jpg
tags: [ English, SQL, Azure, Databases ]
categories: [ SQL, English, Databases ]
lang: en
ref: 9
permalink: /en/tips-to-improve-sql-server-databases/
---

A few days ago I learned a valuable lesson about what to do and not in databases. A few small *tips* that will help us to optimize our **SQL Server** databases and thus make queries faster.

> Disponible en español [aquí](http://www.nocountryforgeeks.com/tips-para-mejorar-bases-de-datos-sql-server/)

## Never use reserved words

There are certain words that are usually reserved in **SQL Server**. Words like `Id` or `Name` can be used as column names, but it is not recommended, the reason, apart from the fact that by convention they should not be used, a more practical one is the speed at which we make manual queries with `joins` by means of.

Hay ciertas palabras que suelen estar reservadas en **SQL Server**. Palabras como `Id` o `Name` pueden usarse como nombre de columnas, pero no está recomendado, la razón, aparte de que por convención no se deben usar, una más práctica es la velocidad a la que hacemos consultas manuales con *joins* de por medio.

Let's say for example that we want to make a complete *Join* of two tables, let's make it simple and not cut columns to show. Suppose there is a `1 to N` relationship between *Table1* and *Table2*.

```sql
SELECT * FROM Table1 JOIN Table2 ON Table1.Table1Id = Table2.Table1Id
```

If you don't use reserved words, the *Join* of tables would be as simple as that, since *Table1* would have its primary key *Table1Id*, and *Table2* would have its primary key *Table2Id*.

If we used the reserved word `Id` and `Name`, the query would be erroneous as it would not know if we were referring to the *Id* and *Name* of *Table1* or *Table2*. To fix it we would have to do a **Select** for each column we want to show. Counting on just having the columns `Id` and `Name` in both tables would look like this:

```sql
SELECT Table1.Id, Table1.Name, Table2.Id, Table2.Name
FROM Table1 JOIN Table2 ON Table1.Id = Table2.Table1Id
```

All of this increases the time it takes us to write the enquiries, especially those we want to do quickly to make small checks. Imagine if both tables have 15 columns and we want to show all of them, we would have to make a **SELECT** indicating each of them separately.

## Use varchar instead of char

There will probably be old databases where varchar can't be used, but most likely you will have access to this type of data for your columns.

Whenever you can use **varchar** instead of **char**. The reason is the size of both. While **char** occupies all the size that we indicate in a fixed way, **varchar** will only occupy the size that we need in each moment.

## Watch out for the n

Many people usually include **n** in all the default **char**/**varchar**, often without knowing its meaning or *because everyone else does it*.

The difference between **varchar** and **nvarchar**, lies in the fact that **nvarchar** uses *unicode* characters, this means that it will occupy **THE DOUBLE** that **varchar** but will give us support for different languages and special characters. If the database is only going to be used in a local region and we don't intend to expand it to the rest of the world, use **varchar** to halve the space used in all these columns.

## Indicates the size of columns where possible

Another point where we can optimize is to indicate the size of our columns. In the case of **char**/**nchar**, it will be the size that they always occupy, in the case of **varchar**/**nvarchar**, the maximum size that they will occupy. Here are a few examples:

- If we are going to save **Urls** in our column, the proper, given the specification of a *Url*, is that the data type of the column is `nvarchar(255)`.
- If we're going to save a **full description**, we can use `nvarchar(MAX)` to allow the user to type in whatever they think is necessary.
- For the **names** the usual is to use `nvarchar(100)` or `nvarchar(255)`.

## Keep the relationship of enums also in the Database

If you are going to have a types column, for example, the currency used in a transaction table, do not save the type as `Euros` or `Dollars`. Save a **byte** with the numeric value referenced to another table where you have all the coins saved. In this way, consulting the list of currency types is very quick and we have a direct reference that takes up very little space.

## Conclusion

With these short tips we will improve the performance of our databases and optimize the space so that queries are less expensive and faster.
