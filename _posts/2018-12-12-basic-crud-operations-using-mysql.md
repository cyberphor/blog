---
layout: post
title: 'Basic CRUD Operations Using MySQL'
permalink: 'basic-crud-operations-using-mysql'
category: notes
---

This blog post outlines how to perform basic CRUD (create, read, update, and delete) operations on MySQL databases. Whenever I am tinkering with databases like this, I try to use a standard naming convention by using “camel case” and prefacing an element with it’s type. Login using `mysql -u root -p password4root`. Lastly, it may be trivial but use `[cmd]` + `[l]` to clear the console screen.

Naming convention examples:
* databases = dbNameOfDatabase
* tables = tbNameofTable
* columns = colNameOfColumn

## Table of Contents
* [Create operations](#create-operations) 
* [Read operations](#read-operations)
* [Update operations](#update-operations)
* [Delete operations](#delete-operations)

## Create operations
Create a user account with root-level privileges
```sql
GRANT ALL PRIVILEGES ON *.* TO 'mike'@'localhost' IDENTIFIED BY 'password4mike';
```
Create a database
```sql
CREATE DATABASE dbLAMP;
```
Engage a database
```sql
USE dbLAMP; -- you must specify your target db for the operations below
```
Create a table within a database
```sql
CREATE TABLE tbMovies ( 
	-> colMovieID int(5) NOT NULL AUTO_INCREMENT,
	-> colMovieName varchar(50) DEFAULT NULL,
	-> colMovieGenre varchar(50) DEFAULT NULL,
	-> colMovieYear varchar(50) DEFAULT NULL,
	-> PRIMARY KEY(colMovieID)
	);
```

## Read operations
Show all databases
```sql
SHOW databases;
```
List all users of a database
```sql
SELECT user FROM mysql.user GROUP BY user; 
```
Display the layout of a database
```sql
DESCRIBE dbLAMP; 
```
Read everything from a table
```sql
SELECT * FROM tbMovies; 
```
## Update operations

Add a record into a table
```sql
INSERT INTO tbMovies VALUES ( 
	->1, 
	->'Alien', 
	->'Science Fiction', 	
	->'1984' 
	);
```
Your input must match the column types of your target table. For example, the first value is 1 as the first column requires integers. There are other ways to add records for each column by the way.

Add a new column to an existing table
```sql
ALTER TABLE tbUsers ADD COLUMN colPassword varchar(256) NOT NULL;
```
Change the name of a column (don’t forget to use the same data type!)
```sql
ALTER TABLE tbUsers CHANGE lastName lastname varchar(50);
```
Update a cell within a record
```sql
UPDATE tbUsers SET colPassword = 'password' WHERE colUserID=1; 
```
This command sentence changed the password of an account with the UserID of 1.

## Delete operations

Delete a database
```sql
DROP database tikiwiki;
```
Delete a database user
```sql
DROP USER 'tikiuser'@'localhost';
DELETE FROM mysql.user WHERE user = 'mike'; 
```
Delete a record from table
```sql
DELETE FROM tbUsers WHERE colUserID=1;
```
Remove a column from a table
```sql
ALTER TABLE tbMovies DROP COLUMN colMovieYear;
```
