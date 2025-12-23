# Section 7: Management Server Initialization

Initialize and start the CloudStack Management Server and configure essential global settings.

## Initialization Steps

### 1. Run Management Setup

```bash
cloudstack-setup-management
```

This command configures the management server for first-time use.

### 2. Restart Management Service

```bash
systemctl restart cloudstack-management
```

### 3. Verify Service Status

```bash
systemctl status cloudstack-management
```

The service should show as "active (running)".

### 4. Monitor Startup Logs

Watch the logs to ensure successful startup:

```bash
tail -f /var/log/cloudstack/management/management-server.log
```

Look for messages indicating successful startup. Press `Ctrl+C` to exit.

## CloudStack Usage Server Installation

The Usage Server tracks resource usage for billing and monitoring.

### Install Usage Server

```bash
apt install cloudstack-usage
```

### Enable and Start Usage Server

```bash
systemctl enable cloudstack-usage
systemctl start cloudstack-usage
systemctl status cloudstack-usage
```

## Global Settings Configuration

These settings should be configured via the CloudStack web UI after first login.

### Navigate to Global Settings

1. Login to CloudStack UI (see [Web UI Access](11-web-ui-access.md))
2. Go to **Infrastructure** → **Global Settings**
3. Search for and update the following settings:

### Required Global Settings

| Setting | Value | Description |
|---------|-------|-------------|
| `network.throttling.rate` | `enable` | Enable network throttling |
| `use.local.storage` | `enable` | Allow use of local storage |
| `dynamic.scaling` | `enable` | Enable dynamic resource scaling |

### Secondary Storage Configuration

**Setting**: `secstorage.allowed.internal.sites`

**Value**: Comma-separated list of internal CIDR ranges that can host template download servers

**Example**: `192.168.0.0/16,10.0.0.0/8`

**Important**: Do not use `0.0.0.0` as it is not a valid site.

## Service Management

### Common Commands

```bash
# Check service status
systemctl status cloudstack-management

# View logs
tail -f /var/log/cloudstack/management/management-server.log

# Restart service
systemctl restart cloudstack-management

# Stop service
systemctl stop cloudstack-management
```

### Log Files

Important log file locations:
- Management logs: `/var/log/cloudstack/management/management-server.log`
- API logs: `/var/log/cloudstack/management/apilog.log`
- Usage logs: `/var/log/cloudstack/usage/usage.log`

## Verification

### Check Management Server is Running

```bash
# Check process
ps aux | grep cloudstack

# Check port 8080 is listening
netstat -tlnp | grep 8080
# or
ss -tlnp | grep 8080
```

### Test API Endpoint

```bash
curl http://localhost:8080/client/api
```

You should receive an XML response indicating the API is accessible.

## Troubleshooting

### Service Won't Start

1. Check logs for errors:
   ```bash
   tail -100 /var/log/cloudstack/management/management-server.log
   ```

2. Verify database connection:
   ```bash
   mysql -u cloud -pcloud -e "USE cloud; SHOW TABLES;"
   ```

3. Check disk space:
   ```bash
   df -h
   ```

### Port Already in Use

If port 8080 is already in use:
```bash
# Find what's using port 8080
lsof -i :8080

# Kill the process if needed or change CloudStack port
```

## Next Steps

Once the management server is running, proceed to [NFS Storage Setup](08-nfs-storage.md).

---

[← Previous: Database Setup](06-database-setup.md) | [Next: NFS Storage →](08-nfs-storage.md)