# Section 11: CloudStack Web UI Access

Access the CloudStack web interface to begin configuring your cloud infrastructure.

## Accessing the UI

### 1. Open Web Browser

Navigate to:

```
http://<MANAGEMENT-SERVER-IP>:8080/client
```

**Example:**
```
http://192.168.7.10:8080/client
```

### 2. Login Credentials

Use the default administrator credentials:

**Username:** `admin`  
**Password:** `password`

⚠️ **Important:** Change the default password immediately after first login!

## First Login Steps

### 1. Change Default Password

After logging in:
1. Click on **admin** (top right corner)
2. Select **Change Password**
3. Enter a strong new password
4. Save changes

### 2. Explore the Dashboard

The main dashboard provides:
- **Overview** - System status and statistics
- **Instances** - Running VMs
- **Infrastructure** - Hosts, storage, networks
- **Events** - System events and alerts
- **Accounts** - User management

## Verifying Installation

### Check Infrastructure Status

Navigate to **Infrastructure** to verify:

1. **Dashboard** → Should show system is ready
2. **Zones** → Should be empty (you'll add this next)
3. **Pods** → Should be empty
4. **Clusters** → Should be empty
5. **Hosts** → Should be empty

This is expected for a new installation.

## Troubleshooting Access Issues

### Cannot Access Web UI

**Issue:** Page doesn't load or connection refused

**Solutions:**

1. **Verify management server is running:**
   ```bash
   systemctl status cloudstack-management
   ```

2. **Check if port 8080 is listening:**
   ```bash
   netstat -tlnp | grep 8080
   # or
   ss -tlnp | grep 8080
   ```

3. **Check firewall:**
   ```bash
   # If using UFW
   ufw status
   ufw allow 8080/tcp
   
   # If using iptables
   iptables -L -n | grep 8080
   ```

4. **Test local connectivity:**
   ```bash
   curl http://localhost:8080/client
   ```

5. **Check management logs:**
   ```bash
   tail -100 /var/log/cloudstack/management/management-server.log
   ```

### Login Issues

**Issue:** Invalid credentials or login fails

**Solutions:**

1. **Verify database is accessible:**
   ```bash
   mysql -u cloud -pcloud -e "USE cloud; SELECT * FROM user WHERE username='admin';"
   ```

2. **Reset admin password (if needed):**
   ```bash
   # Stop management server
   systemctl stop cloudstack-management
   
   # Reset password in database
   mysql -u cloud -pcloud cloud
   ```
   
   In MySQL:
   ```sql
   UPDATE user SET password=MD5('newpassword') WHERE username='admin';
   EXIT;
   ```
   
   ```bash
   # Start management server
   systemctl start cloudstack-management
   ```

### Slow Loading or Timeouts

**Issue:** UI loads slowly or times out

**Solutions:**

1. **Check system resources:**
   ```bash
   # Check CPU and memory
   top
   
   # Check disk I/O
   iostat -x
   ```

2. **Verify database performance:**
   ```bash
   mysql -u cloud -pcloud -e "SHOW PROCESSLIST;"
   ```

3. **Check management server heap size:**
   ```bash
   # View current Java process
   ps aux | grep cloudstack
   
   # If needed, adjust heap in /etc/default/cloudstack-management
   ```

## Browser Compatibility

CloudStack UI works best with modern browsers:

✅ **Recommended:**
- Google Chrome (latest)
- Mozilla Firefox (latest)
- Microsoft Edge (latest)
- Safari (latest)

⚠️ **Not Recommended:**
- Internet Explorer (any version)
- Outdated browser versions

## UI Navigation Tips

### Quick Navigation

| Section | Purpose |
|---------|---------|
| **Dashboard** | Overview and quick stats |
| **Instances** | Manage VMs |
| **Storage** | Primary and secondary storage |
| **Network** | Network configuration |
| **Templates** | VM templates and ISOs |
| **Infrastructure** | Physical resources (hosts, storage) |
| **Projects** | Multi-user projects |
| **Events** | System logs and events |
| **Accounts** | User and account management |

### Keyboard Shortcuts

- `Ctrl + K` - Quick search
- `ESC` - Close modal dialogs
- `F5` - Refresh page

## Security Best Practices

### After First Login

1. ✅ Change default admin password
2. ✅ Create separate user accounts for different roles
3. ✅ Configure SSL/TLS for HTTPS access (recommended)
4. ✅ Set up proper firewall rules
5. ✅ Enable audit logging
6. ✅ Configure session timeout

### Enable HTTPS (Optional but Recommended)

For production environments, configure HTTPS:

1. Obtain SSL certificate
2. Configure Tomcat for SSL
3. Update CloudStack configuration
4. Access via `https://<MANAGEMENT-SERVER-IP>:8443/client`

Refer to CloudStack documentation for detailed HTTPS setup.

## Next Steps

Once you have successfully accessed the web UI, proceed to [Post-Installation Configuration](12-post-installation.md) to set up your first zone and add infrastructure.

---

[← Previous: Final Steps](10-final-steps.md) | [Next: Post-Installation →](12-post-installation.md)# Section 11: CloudStack Web UI Access

Access the CloudStack web interface to begin configuring your cloud infrastructure.

## Accessing the UI

### 1. Open Web Browser

Navigate to:

```
http://<MANAGEMENT-SERVER-IP>:8080/client
```

**Example:**
```
http://192.168.7.10:8080/client
```

### 2. Login Credentials

Use the default administrator credentials:

**Username:** `admin`  
**Password:** `password`

⚠️ **Important:** Change the default password immediately after first login!

## First Login Steps

### 1. Change Default Password

After logging in:
1. Click on **admin** (top right corner)
2. Select **Change Password**
3. Enter a strong new password
4. Save changes

### 2. Explore the Dashboard

The main dashboard provides:
- **Overview** - System status and statistics
- **Instances** - Running VMs
- **Infrastructure** - Hosts, storage, networks
- **Events** - System events and alerts
- **Accounts** - User management

## Verifying Installation

### Check Infrastructure Status

Navigate to **Infrastructure** to verify:

1. **Dashboard** → Should show system is ready
2. **Zones** → Should be empty (you'll add this next)
3. **Pods** → Should be empty
4. **Clusters** → Should be empty
5. **Hosts** → Should be empty

This is expected for a new installation.

## Troubleshooting Access Issues

### Cannot Access Web UI

**Issue:** Page doesn't load or connection refused

**Solutions:**

1. **Verify management server is running:**
   ```bash
   systemctl status cloudstack-management
   ```

2. **Check if port 8080 is listening:**
   ```bash
   netstat -tlnp | grep 8080
   # or
   ss -tlnp | grep 8080
   ```

3. **Check firewall:**
   ```bash
   # If using UFW
   ufw status
   ufw allow 8080/tcp
   
   # If using iptables
   iptables -L -n | grep 8080
   ```

4. **Test local connectivity:**
   ```bash
   curl http://localhost:8080/client
   ```

5. **Check management logs:**
   ```bash
   tail -100 /var/log/cloudstack/management/management-server.log
   ```

### Login Issues

**Issue:** Invalid credentials or login fails

**Solutions:**

1. **Verify database is accessible:**
   ```bash
   mysql -u cloud -pcloud -e "USE cloud; SELECT * FROM user WHERE username='admin';"
   ```

2. **Reset admin password (if needed):**
   ```bash
   # Stop management server
   systemctl stop cloudstack-management
   
   # Reset password in database
   mysql -u cloud -pcloud cloud
   ```
   
   In MySQL:
   ```sql
   UPDATE user SET password=MD5('newpassword') WHERE username='admin';
   EXIT;
   ```
   
   ```bash
   # Start management server
   systemctl start cloudstack-management
   ```

### Slow Loading or Timeouts

**Issue:** UI loads slowly or times out

**Solutions:**

1. **Check system resources:**
   ```bash
   # Check CPU and memory
   top
   
   # Check disk I/O
   iostat -x
   ```

2. **Verify database performance:**
   ```bash
   mysql -u cloud -pcloud -e "SHOW PROCESSLIST;"
   ```

3. **Check management server heap size:**
   ```bash
   # View current Java process
   ps aux | grep cloudstack
   
   # If needed, adjust heap in /etc/default/cloudstack-management
   ```

## Browser Compatibility

CloudStack UI works best with modern browsers:

✅ **Recommended:**
- Google Chrome (latest)
- Mozilla Firefox (latest)
- Microsoft Edge (latest)
- Safari (latest)

⚠️ **Not Recommended:**
- Internet Explorer (any version)
- Outdated browser versions

## UI Navigation Tips

### Quick Navigation

| Section | Purpose |
|---------|---------|
| **Dashboard** | Overview and quick stats |
| **Instances** | Manage VMs |
| **Storage** | Primary and secondary storage |
| **Network** | Network configuration |
| **Templates** | VM templates and ISOs |
| **Infrastructure** | Physical resources (hosts, storage) |
| **Projects** | Multi-user projects |
| **Events** | System logs and events |
| **Accounts** | User and account management |

### Keyboard Shortcuts

- `Ctrl + K` - Quick search
- `ESC` - Close modal dialogs
- `F5` - Refresh page

## Security Best Practices

### After First Login

1. ✅ Change default admin password
2. ✅ Create separate user accounts for different roles
3. ✅ Configure SSL/TLS for HTTPS access (recommended)
4. ✅ Set up proper firewall rules
5. ✅ Enable audit logging
6. ✅ Configure session timeout

### Enable HTTPS (Optional but Recommended)

For production environments, configure HTTPS:

1. Obtain SSL certificate
2. Configure Tomcat for SSL
3. Update CloudStack configuration
4. Access via `https://<MANAGEMENT-SERVER-IP>:8443/client`

Refer to CloudStack documentation for detailed HTTPS setup.

## Next Steps

Once you have successfully accessed the web UI, proceed to [Post-Installation Configuration](12-post-installation.md) to set up your first zone and add infrastructure.

---

[← Previous: Final Steps](10-final-steps.md) | [Next: Post-Installation →](12-post-installation.md)