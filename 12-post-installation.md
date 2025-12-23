# Section 12: Post-Installation Configuration

Configure your CloudStack environment by adding zones, storage, and hosts through the web UI.

## Overview

After installation, you need to configure:
1. Zone (physical datacenter)
2. Primary Storage (VM disks)
3. Secondary Storage (templates, ISOs)
4. KVM Hosts (hypervisors)
5. Optional: Debug log optimization

## Part 1: Add Zone

### Zone Configuration Wizard

1. Login to CloudStack UI
2. Navigate to **Infrastructure** → **Zones**
3. Click **Add Zone**

### Zone Settings

Choose the following options:

| Setting | Value | Description |
|---------|-------|-------------|
| **Network Type** | Advanced Networking | Full network isolation and advanced features |
| **Hypervisor** | KVM | Your hypervisor choice |
| **Bridge** | cloudbr0 | The bridge created in network configuration |

### Zone Setup Steps

#### 1. Zone Information
- **Name**: Enter a meaningful name (e.g., "Zone1", "Production-Zone")
- **DNS**: Enter your DNS server (e.g., 8.8.8.8)
- **Internal DNS**: Same as DNS or your internal DNS
- **Hypervisor**: Select **KVM**

#### 2. Network Configuration
- **Physical Network**: Select or create
- **Guest Traffic**: Assign to cloudbr0
- **Public Traffic**: Assign to cloudbr0
- **Management Traffic**: Assign to cloudbr0
- **Storage Traffic**: Assign to cloudbr0

#### 3. Pod Configuration
- **Pod Name**: Enter name (e.g., "Pod1")
- **Reserved System Gateway**: Your network gateway
- **Reserved System Netmask**: Your network netmask
- **Start/End Reserved System IPs**: IP range for system VMs

#### 4. Guest Network
Configure IP ranges for guest VMs:
- **Gateway**: Guest network gateway
- **Netmask**: Guest network netmask
- **Start IP**: Beginning of IP range
- **End IP**: End of IP range

#### 5. Cluster
- **Cluster Name**: Enter name (e.g., "Cluster1")
- **Hypervisor**: KVM

#### 6. Complete Zone Creation
- Review settings
- Click **Launch Zone**

## Part 2: Add Primary Storage

Primary storage stores virtual machine disk volumes.

### NFS Primary Storage

1. Navigate to **Infrastructure** → **Primary Storage**
2. Click **Add Primary Storage**

Configure:

| Field | Value |
|-------|-------|
| **Scope** | Zone-wide or Cluster |
| **Protocol** | NFS |
| **Server** | IP address of storage server |
| **Path** | /export/primary |

**Example:**
```
nfs://192.168.7.10/export/primary
```

### Local Storage

If using local storage:

1. Enable in **Global Settings**:
   - Search: `use.local.storage`
   - Set to: `true`

2. Add Primary Storage:
   - **Scope**: Host
   - **Protocol**: FileSystem
   - **Path**: `/mnt/primary1-agent1` (your mount point)

## Part 3: Add Secondary Storage

Secondary storage stores templates, ISOs, and snapshots.

### Configure Secondary Storage

1. Navigate to **Infrastructure** → **Secondary Storage**
2. Click **Add Secondary Storage**

Configure:

| Field | Value |
|-------|-------|
| **Provider** | NFS |
| **Zone** | Select your zone |
| **Server** | IP address of storage server |
| **Path** | /export/secondary |

**Example:**
```
nfs://192.168.7.10/export/secondary
```

### Verify Secondary Storage

After adding:
- System VMs will be created automatically
- Wait for Secondary Storage VM to start
- Check **Infrastructure** → **System VMs**

## Part 4: Add KVM Hosts

Add your KVM hypervisor hosts to the CloudStack infrastructure.

### Add Host

1. Navigate to **Infrastructure** → **Hosts**
2. Click **Add Host**

Configure:

| Field | Value | Example |
|-------|-------|---------|
| **Zone** | Select your zone | Zone1 |
| **Pod** | Select your pod | Pod1 |
| **Cluster** | Select your cluster | Cluster1 |
| **Host name** | Hostname or IP | 192.168.7.20 |
| **Username** | SSH username | root |
| **Password** | SSH password | your-password |

### Host Addition Process

After clicking **OK**:
1. CloudStack will connect to the host via SSH
2. Install necessary components
3. Configure the host
4. Add it to the cluster

### Verify Host Status

Check host status:
- Navigate to **Infrastructure** → **Hosts**
- Status should show **Up**
- Resource state should be **Enabled**

## Part 5: Debug Log Optimization

Reduce log verbosity to improve performance.

### Disable Debug Logging

Edit log configuration:

```bash
nano /etc/cloudstack/management/log4j2.xml
```

### Find and Modify Log Levels

Locate these lines:

```xml
<Logger name="com.cloud" level="DEBUG"/>
<Logger name="org.apache.cloudstack" level="DEBUG"/>
<Logger name="org.apache.cloudstack.api.command" level="DEBUG"/>
<Logger name="apiserver.com.cloud" level="DEBUG"/>
<Logger name="apiserver.com.cloud" level="DEBUG" additivity="false">
<ThresholdFilter level="DEBUG" onMatch="ACCEPT" onMismatch="DENY"/>
```

Change `DEBUG` to `INFO`:

```xml
<Logger name="com.cloud" level="INFO"/>
<Logger name="org.apache.cloudstack" level="INFO"/>
<Logger name="org.apache.cloudstack.api.command" level="INFO"/>
<Logger name="apiserver.com.cloud" level="INFO"/>
<ThresholdFilter level="INFO" onMatch="ACCEPT" onMismatch="DENY"/>
```

### Restart Management Server

```bash
systemctl restart cloudstack-management
```

### Benefits of Reduced Logging

- Smaller log files
- Better performance
- Easier troubleshooting (less noise)
- Reduced disk I/O

## Verification Steps

### 1. Check Zone Status

Navigate to **Infrastructure** → **Zones**
- Zone should show **Enabled**
- All resources should be visible

### 2. Verify Storage

Check both storage types:
- **Primary Storage**: Should show available capacity
- **Secondary Storage**: System VMs should be running

### 3. Verify Hosts

Navigate to **Infrastructure** → **Hosts**
- All hosts should show **Up** status
- Resource state **Enabled**

### 4. Check System VMs

Navigate to **Infrastructure** → **System VMs**

You should see:
- **Console Proxy VM** - Running
- **Secondary Storage VM** - Running

## Common Post-Installation Tasks

### Upload Templates

1. Navigate to **Templates**
2. Click **Register Template**
3. Provide template URL and details
4. Wait for download to complete

### Create Networks

1. Navigate to **Network** → **Guest Networks**
2. Click **Add Network**
3. Configure network settings

### Create Instances

1. Navigate to **Instances**
2. Click **Add Instance**
3. Follow the wizard to create your first VM

## Troubleshooting

### Hosts Won't Add

**Check:**
- SSH connectivity: `ssh root@<host-ip>`
- CloudStack agent: `systemctl status cloudstack-agent`
- Libvirt: `systemctl status libvirtd`

### Storage Not Showing Capacity

**Check:**
- NFS exports: `exportfs -v`
- Mount accessibility: `showmount -e <storage-ip>`
- Permissions: `ls -la /export/primary`

### System VMs Not Starting

**Check:**
- Secondary storage is accessible
- Templates are downloaded
- Network connectivity
- System VM logs in `/var/log/cloudstack/management/`

## Next Steps

Your CloudStack environment is now fully configured! You can:

1. **Upload OS templates** for creating VMs
2. **Create user accounts** and domains
3. **Configure networking** (VLANs, VPCs)
4. **Set up storage** policies and offerings
5. **Create service offerings** for different VM sizes
6. **Launch your first instances**

## Additional Resources

- **CloudStack Documentation**: https://docs.cloudstack.apache.org/
- **Community Wiki**: https://cwiki.apache.org/confluence/display/CLOUDSTACK
- **Mailing Lists**: https://cloudstack.apache.org/mailing-lists.html
- **API Documentation**: http://your-server:8080/client/api

## Backup Recommendations

Before going to production:

1. **Backup MySQL database regularly**
   ```bash
   mysqldump -u cloud -pcloud cloud > cloudstack-backup.sql
   ```

2. **Backup configuration files**
   ```bash
   tar -czf cloudstack-config-backup.tar.gz /etc/cloudstack/
   ```

3. **Document your configuration**
   - IP addresses
   - Network ranges
   - Storage paths
   - Custom settings

---

**Congratulations!** Your CloudStack installation is complete and ready for use.

---

[← Previous: Web UI Access](11-web-ui-access.md) | [Back to README](README.md)