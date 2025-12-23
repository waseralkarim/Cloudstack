# Section 10: Final Steps

Complete the KVM host configuration with these final required steps.

## 1. Add Host UUID

Each KVM host needs a unique UUID for libvirt identification.

### Install UUID Tool

```bash
apt install uuid -y
```

### Generate and Save Host UUID

```bash
uuidgen > /etc/libvirt/host_uuid
```

### Verify UUID

```bash
cat /etc/libvirt/host_uuid
```

## 2. Restart Required Services

Restart all services to apply configurations:

```bash
# Restart libvirt
systemctl restart libvirtd

# Restart CloudStack agent
systemctl restart cloudstack-agent
```

## 3. Verify Service Status

Check that all services are running properly:

```bash
# Check libvirt
systemctl status libvirtd

# Check CloudStack agent
systemctl status cloudstack-agent
```

## 4. Enable Services on Boot

Ensure services start automatically on system boot:

```bash
systemctl enable libvirtd
systemctl enable cloudstack-agent
```

## Verification Checklist

Before proceeding to the UI, verify the following:

### Network Configuration
```bash
# Verify bridge exists
ip addr show cloudbr0

# Test connectivity
ping -c 4 <management-server-ip>
```

### Storage Configuration
```bash
# Check mount points
df -h | grep -E 'export|primary'

# Verify NFS exports (if using NFS)
showmount -e localhost
```

### Services Status
```bash
# All services should be active
systemctl status mysql
systemctl status cloudstack-management
systemctl status nfs-kernel-server  # If storage server
systemctl status libvirtd           # On KVM hosts
systemctl status cloudstack-agent   # On KVM hosts
```

### Firewall Configuration (if applicable)

If you're using UFW or other firewall:

```bash
# Allow CloudStack management port
ufw allow 8080/tcp

# Allow libvirt
ufw allow 16509/tcp

# Allow NFS (if storage server)
ufw allow from 192.168.7.0/24 to any port nfs

# Allow VNC
ufw allow 5900:6100/tcp
```

### Log Files Check

Verify no critical errors in logs:

```bash
# Management server logs
tail -50 /var/log/cloudstack/management/management-server.log

# Agent logs (on KVM hosts)
tail -50 /var/log/cloudstack/agent/agent.log
```

## Common Issues and Solutions

### Agent Not Connecting to Management Server

**Symptoms:** Agent status shows disconnected in CloudStack UI

**Solutions:**
1. Check network connectivity:
   ```bash
   ping <management-server-ip>
   telnet <management-server-ip> 8250
   ```

2. Verify libvirt is accessible:
   ```bash
   virsh -c qemu+tcp://localhost/system list
   ```

3. Check agent configuration:
   ```bash
   cat /etc/cloudstack/agent/agent.properties
   ```

### Storage Not Accessible

**Symptoms:** Cannot add primary/secondary storage

**Solutions:**
1. Verify NFS exports:
   ```bash
   exportfs -v
   showmount -e localhost
   ```

2. Test NFS mount manually:
   ```bash
   mount -t nfs <storage-ip>:/export/primary /tmp/test
   ```

3. Check permissions:
   ```bash
   ls -la /export/primary
   ls -la /export/secondary
   ```

### Management Server Issues

**Symptoms:** Cannot access web UI or API

**Solutions:**
1. Check if port 8080 is listening:
   ```bash
   netstat -tlnp | grep 8080
   ```

2. Verify database connectivity:
   ```bash
   mysql -u cloud -pcloud -e "USE cloud; SELECT COUNT(*) FROM host;"
   ```

3. Review management logs for errors:
   ```bash
   grep ERROR /var/log/cloudstack/management/management-server.log
   ```

## Performance Verification

### System Resources

```bash
# Check CPU
top
htop

# Check memory
free -h

# Check disk I/O
iostat -x 1

# Check network
iftop
```

### Service Response Times

```bash
# Test management server API response
time curl -s http://localhost:8080/client/api > /dev/null

# Test database query response
time mysql -u cloud -pcloud -e "USE cloud; SELECT COUNT(*) FROM host;"
```

## Next Steps

With all services configured and verified, proceed to [Web UI Access](11-web-ui-access.md) to begin configuring your cloud infrastructure.

---

[← Previous: KVM Hypervisor](09-kvm-hypervisor.md) | [Next: Web UI Access →](11-web-ui-access.md)