# MySQL

## User

### New

```sql
CREATE USER 'username'@'localhost' IDENTIFIED BY 'NewPassword';
```
### Alter

```sql
ALTER USER 'username'@'localhost' IDENTIFIED BY 'NewPassword';
```

### Old password system

```sql
ALTER USER 'username'@'localhost' IDENTIFIED WITH mysql_native_password BY 'NewPassword';
```

## Grants

``sql
GRANT ALL ON `database`.* TO 'user'@'localhost';
```