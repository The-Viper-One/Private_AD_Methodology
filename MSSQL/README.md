# MSSQL

## Common Findings

### xp_dirtree
Should not be enabled on low privileged roles and only available to administrative users such as SA.
This check should be performed from the perspective of a low privileged user.

```sql
SELECT HAS_PERMS_BY_NAME('master..xp_dirtree','OBJECT','EXECUTE') AS has_execute;
```
> Example Output (Pass)
```
has_execute   
-----------   
          0 
```
> Example Output (Failure)
```
has_execute   
-----------   
          1
```

#### Remediation

```sql
EXECUTE sp_configure 'show advanced options', 1;
RECONFIGURE;
EXECUTE sp_configure 'xp_cmdshell', 0;
RECONFIGURE; 
GO 
EXECUTE sp_configure 'show advanced options', 0;
RECONFIGURE;
```

### xp_fileexist
Should not be enabled on low privileged roles and only available to administrative users such as SA.
This check should be performed from the perspective of a low privileged user.

```sql
SELECT HAS_PERMS_BY_NAME('master..xp_fileexist','OBJECT','EXECUTE') AS has_execute;
```
> Example Output (Pass)
```
has_execute   
-----------   
          0 
```
> Example Output (Failure)
```
has_execute   
-----------   
          1
```

#### Remediation
```sql
USE master;
GO
REVOKE EXECUTE ON xp_fileexist FROM PUBLIC;
-- optionally revoke from specific users/roles
REVOKE EXECUTE ON xp_fileexist FROM [SomeLowPrivUserOrRole];
GO
```
