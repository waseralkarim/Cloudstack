# Section 6: CloudStack Database Setup

Initialize the CloudStack database using the setup script provided with the management server.

## Database Setup Command

### Command Syntax

```bash
cloudstack-setup-databases cloud:cloud@localhost \
--deploy-as=root:<MYSQL_ROOT_PASSWORD> \
-i <MANAGEMENT-SERVER-IP>
```

### Parameter Explanation

| Parameter | Description |
|-----------|-------------|
| `cloud:cloud@localhost` | CloudStack database user:password@host |
| `--deploy-as=root:<PASSWORD>` | MySQL root credentials for creating databases |
| `-i <MANAGEMENT-SERVER-IP>` | IP address of the management server |

### Example Usage

```bash
cloudstack-setup-databases cloud:cloud@localhost \
--deploy-as=root:Pass123 \
-i 192.168.7.10
```

**Replace:**
- `Pass123` - Your MySQL root password
- `192.168.7.10` - Your management server IP address

## What This Command Does

The setup script will:
1. Create the CloudStack database
2. Create the `cloud` database user
3. Set up database schema and tables
4. Configure database permissions
5. Initialize default data

## Expected Output

You should see output indicating:
- Database creation
- Table creation
- Schema initialization
- Success message

```
Mysql Server: localhost
Mysql User: cloud
Mysql Password: cloud
...
CloudStack database setup completed successfully
```

## Verification

### Check Database Creation

Login to MySQL and verify:

```bash
mysql -u root -p
```

Inside MySQL:

```sql
-- Show databases
SHOW DATABASES;

-- Should see 'cloud' database
USE cloud;

-- Show tables
SHOW TABLES;

-- Exit
EXIT;
```

### Verify CloudStack User

```bash
mysql -u cloud -pcloud -e "SHOW DATABASES;"
```

## Troubleshooting

### Connection Errors

If you get connection errors:
```bash
# Check MySQL is running
systemctl status mysql

# Test root connection
mysql -u root -p
```

### Permission Issues

If permission errors occur:
```bash
# Reset root password if needed
sudo mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'newpassword';
FLUSH PRIVILEGES;
EXIT;
```

### Re-running Setup

If you need to re-run the setup:

```bash
# Drop existing database
mysql -u root -p -e "DROP DATABASE IF EXISTS cloud;"

# Re-run setup command
cloudstack-setup-databases cloud:cloud@localhost \
--deploy-as=root:Pass123 \
-i 192.168.7.10
```

## Important Notes

- 🔑 Remember the database credentials (`cloud:cloud`)
- Keep your MySQL root password secure
- The database must be successfully created before starting management server

## Next Steps

Once the database is set up, proceed to [Management Server Initialization](07-management-initialization.md).

---

[← Previous: Management Server Installation](05-management-server.md) | [Next: Management Initialization →](07-management-initialization.md)