# Section 8: NFS Primary & Secondary Storage Setup

CloudStack requires storage for virtual machine disks (primary storage) and templates/ISOs (secondary storage). This guide uses NFS for both.

## Prerequisites

- Dedicated disks for storage (e.g., `/dev/sdb`, `/dev/sdc`)
- NFS kernel server installed (done in [Preparation](01-preparation.md))

## Storage Overview

| Storage Type | Purpose | Example Device |
|--------------|---------|----------------|
| Primary Storage | VM disk images and snapshots | `/dev/sdb` |
| Secondary Storage | Templates, ISOs, snapshots | `/dev/sdc` |

## Setup Steps

### 1. Format Disks

Partition the disks:

```bash
fdisk /dev/sdb
```

In fdisk:
- Press `n` for new partition
- Press `p` for primary
- Press `1` for partition number
- Press Enter twice to use full disk
- Press `w` to write changes

Repeat for the second disk:

```bash
fdisk /dev/sdc
```

Format the partitions:

```bash
mkfs.ext4 /dev/sdb1
mkfs.ext4 /dev/sdc1
```

### 2. Create Mount Points

```bash
mkdir -p /export/primary
mkdir -p /export/secondary
```

### 3. Mount the Disks

```bash
mount /dev/sdb1 /export/primary
mount /dev/sdc1 /export/secondary
```

### 4. Update /etc/fstab for Persistent Mounts

Add entries to `/etc/fstab`:

```bash
echo "/dev/sdb1 /export/primary ext4 defaults 0 0" >> /etc/fstab
echo "/dev/sdc1 /export/secondary ext4 defaults 0 0" >> /etc/fstab
```

Verify fstab entries:

```bash
cat /etc/fstab
```

Test the fstab configuration:

```bash
umount /export/primary
umount /export/secondary
mount -a
```

### 5. Configure NFS Exports

Edit the NFS exports file:

```bash
nano /etc/exports
```

Add the following lines:

```
/export/primary *(rw,async,no_root_squash,no_subtree_check)
/export/secondary *(rw,async,no_root_squash,no_subtree_check)
```

**Export options explained:**
- `*` - Allow all hosts (restrict to specific networks in production)
- `rw` - Read-write access
- `async` - Asynchronous writes (better performance)
- `no_root_squash` - Don't map root user to anonymous
- `no_subtree_check` - Disable subtree checking

### 6. Apply NFS Configuration

```bash
exportfs -a
systemctl restart nfs-kernel-server
```

## Verification

### Check Mount Points

```bash
df -h | grep export
```

You should see both mount points listed.

### Check NFS Exports

```bash
exportfs -v
```

This shows all active NFS exports with their options.

### Test NFS Mount (from another host)

From a different machine or the same host:

```bash
# Show available NFS exports
showmount -e <storage-server-ip>

# Test mount
mkdir -p /tmp/test-mount
mount -t nfs <storage-server-ip>:/export/primary /tmp/test-mount

# Verify
df -h | grep test-mount

# Unmount
umount /tmp/test-mount
```

## Production Considerations

### Security

For production, restrict NFS access to specific networks:

```bash
# Edit /etc/exports
nano /etc/exports
```

Change from `*` to specific networks:

```
/export/primary 192.168.7.0/24(rw,async,no_root_squash,no_subtree_check)
/export/secondary 192.168.7.0/24(rw,async,no_root_squash,no_subtree_check)
```

Apply changes:

```bash
exportfs -ra
```

### Performance Tuning

For better performance, consider:

```bash
# Edit /etc/exports
nano /etc/exports
```

Add performance options:

```
/export/primary *(rw,async,no_root_squash,no_subtree_check,no_wdelay)
/export/secondary *(rw,async,no_root_squash,no_subtree_check,no_wdelay)
```

### Monitoring

Monitor NFS performance:

```bash
# Check NFS statistics
nfsstat

# Check mounted filesystems
nfsstat -m
```

## Troubleshooting

### NFS Service Issues

```bash
# Check NFS service status
systemctl status nfs-kernel-server

# View NFS logs
journalctl -u nfs-kernel-server -f
```

### Mount Issues

```bash
# Check if exports are available
exportfs -v

# Verify network connectivity
ping <storage-server-ip>

# Check firewall (if enabled)
ufw status
```

### Permission Issues

```bash
# Set proper permissions
chmod 755 /export/primary
chmod 755 /export/secondary

# Set ownership (if needed)
chown nobody:nogroup /export/primary
chown nobody:nogroup /export/secondary
```

## Next Steps

Once NFS storage is configured, proceed to [KVM Hypervisor Installation](09-kvm-hypervisor.md).

---

[← Previous: Management Initialization](07-management-initialization.md) | [Next: KVM Hypervisor →](09-kvm-hypervisor.md)