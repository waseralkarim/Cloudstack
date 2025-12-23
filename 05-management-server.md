# Section 5: Management Server Installation

The CloudStack Management Server is the central control component that manages the entire cloud infrastructure.

## Installation

### Install CloudStack Management Server

```bash
apt install cloudstack-management -y
```

This command will install:
- CloudStack Management Server
- Required dependencies
- System service files
- Web interface components

## What Gets Installed

The installation includes:
- **Management Server**: Core CloudStack management component
- **Tomcat Server**: Serves the web UI
- **API Server**: Handles API requests
- **Configuration Files**: Located in `/etc/cloudstack/management/`

## Post-Installation

### Verify Installation

Check that the package was installed successfully:

```bash
dpkg -l | grep cloudstack-management
```

### Check Installation Directory

The management server files are installed in:
```bash
ls -la /usr/share/cloudstack-management/
```

## Important Notes

- ⚠️ **Do not start the service yet** - Database setup is required first
- The management server will be initialized in the next steps
- Default port 8080 will be used for the web interface

## Service Management

After database setup, you'll manage the service with:

```bash
# Start service
systemctl start cloudstack-management

# Stop service
systemctl stop cloudstack-management

# Restart service
systemctl restart cloudstack-management

# Check status
systemctl status cloudstack-management
```

## Next Steps

With the management server installed, proceed to [Database Setup](06-database-setup.md) to initialize the CloudStack database.

---

[← Previous: CloudStack Repository](04-cloudstack-repository.md) | [Next: Database Setup →](06-database-setup.md)