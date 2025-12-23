# Section 3: MySQL Installation and Configuration

CloudStack requires MySQL as its database backend. This section covers installation and configuration optimized for CloudStack.

## Installation

### Install MySQL Server

```bash
apt install mysql-server -y
```

## Configuration

### 1. Edit MySQL Configuration

Open the MySQL configuration file:

```bash
nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

### 2. Add CloudStack-Specific Settings

Add the following lines inside the `[mysqld]` section:

```ini
[mysqld]
server-id=1
innodb_rollback_on_timeout=1
innodb_lock_wait_timeout=600
max_connections=1000
log-bin=mysql-bin
binlog-format="ROW"
sql-mode="STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION,ERROR_FOR_DIVISION_BY_ZERO,NO_ZERO_DATE,NO_ZERO_IN_DATE"
```

**Configuration parameters explained:**

| Parameter | Value | Purpose |
|-----------|-------|---------|
| `server-id` | 1 | Unique server identifier for replication |
| `innodb_rollback_on_timeout` | 1 | Rollback entire transaction on timeout |
| `innodb_lock_wait_timeout` | 600 | Lock wait timeout in seconds |
| `max_connections` | 1000 | Maximum concurrent connections |
| `log-bin` | mysql-bin | Enable binary logging |
| `binlog-format` | ROW | Binary log format for replication |
| `sql-mode` | (various) | SQL mode settings for data integrity |

### 3. Restart MySQL Service

Apply the configuration changes:

```bash
systemctl restart mysql
```

### 4. Verify MySQL is Running

Check MySQL status:

```bash
systemctl status mysql
```

## Secure MySQL Installation (Optional but Recommended)

Run the MySQL security script:

```bash
mysql_secure_installation
```

Follow the prompts to:
- Set root password (remember this for later steps!)
- Remove anonymous users
- Disallow root login remotely
- Remove test database
- Reload privilege tables

## Verification

### Test MySQL Connection

```bash
mysql -u root -p
```

Enter your root password when prompted.

### Check Configuration

Inside MySQL shell, verify settings:

```sql
SHOW VARIABLES LIKE 'max_connections';
SHOW VARIABLES LIKE 'innodb_lock_wait_timeout';
SHOW VARIABLES LIKE 'binlog_format';
EXIT;
```

## Important Notes

- 🔑 **Remember your MySQL root password** - you'll need it for CloudStack database setup
- The MySQL configuration is optimized for CloudStack's requirements
- Binary logging enables database replication if needed in the future

## Troubleshooting

If MySQL fails to start after configuration changes:

```bash
# Check MySQL error log
tail -f /var/log/mysql/error.log

# Verify configuration syntax
mysqld --help --verbose
```

## Next Steps

Once MySQL is installed and configured, proceed to [CloudStack Repository Setup](04-cloudstack-repository.md).

---

[← Previous: Network Configuration](02-network-configuration.md) | [Next: CloudStack Repository →](04-cloudstack-repository.md)
