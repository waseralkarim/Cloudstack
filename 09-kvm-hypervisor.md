# Section 9: KVM Hypervisor Installation and Configuration

Install and configure KVM hypervisor nodes that will host virtual machines in your CloudStack environment.

## Part 1: KVM Installation

### 1. Install KVM and Required Packages

```bash
apt install -y qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils libvirt-daemon-driver-lxc
apt install -y qemu-guest-agent
```

**Packages installed:**
- `qemu-kvm` - KVM hypervisor
- `libvirt-daemon-system` - Libvirt daemon
- `libvirt-clients` - Libvirt client tools
- `bridge-utils` - Network bridge utilities
- `libvirt-daemon-driver-lxc` - LXC driver for libvirt
- `qemu-guest-agent` - Guest agent for VM management

### 2. Verify KVM Installation

```bash
kvm-ok
```

Expected output:
```
INFO: /dev/kvm exists
KVM acceleration can be used
```

## Part 2: CloudStack Agent Installation

```bash
apt install cloudstack-agent -y
```

## Part 3: Libvirt Configuration

### 1. Configure QEMU

Edit QEMU configuration:

```bash
nano /etc/libvirt/qemu.conf
```

Find and modify the following line:

```
vnc_listen = "0.0.0.0"
```

This allows VNC connections from any IP address.

### 2. Configure Libvirtd

Edit libvirtd configuration:

```bash
nano /etc/libvirt/libvirtd.conf
```

Add or modify these lines:

```
listen_tls = 0
listen_tcp = 1
tcp_port = "16509"
auth_tcp = "none"
mdns_adv = 0
```

**Configuration explained:**
- `listen_tls = 0` - Disable TLS
- `listen_tcp = 1` - Enable TCP listening
- `tcp_port = "16509"` - Default libvirt port
- `auth_tcp = "none"` - No authentication (secure your network!)
- `mdns_adv = 0` - Disable mDNS advertising

### 3. Enable TCP Listening

Edit libvirtd defaults:

```bash
nano /etc/default/libvirtd
```

Set:

```
LIBVIRTD_ARGS="--listen"
```

### 4. Disable Socket Activation

```bash
systemctl mask libvirtd.socket libvirtd-tcp.socket libvirtd-tls.socket
```

### 5. Restart Libvirt

```bash
systemctl restart libvirtd
```

## Part 4: UEFI Support Configuration

### Configure UEFI Properties

Create or edit the UEFI properties file:

```bash
nano /etc/cloudstack/agent/uefi.properties
```

Add:

```properties
guest.nvram.template.secure=/usr/share/OVMF/OVMF_VARS_4M.ms.fd
guest.nvram.template.legacy=/usr/share/OVMF/OVMF_VARS_4M.fd
guest.nvram.path=/var/lib/libvirt/qemu/nvram/
guest.loader.secure=/usr/share/OVMF/OVMF_CODE_4M.secboot.fd
guest.loader.legacy=/usr/share/OVMF/OVMF_CODE_4M.fd
```

### Restart Services

```bash
systemctl restart cloudstack-agent
systemctl restart libvirtd
```

If you have the management server on the same host:

```bash
systemctl restart cloudstack-management
```

## Part 5: Local Storage Setup

This section covers setting up local storage on KVM hosts.

### Enable Local Storage in CloudStack

1. Login to CloudStack UI
2. Navigate to **Global Settings**
3. Search for `use.local.storage`
4. Set to `true`

### Storage Configuration Options

#### Option A: Using LVM

##### 1. Create Physical Volume

```bash
pvcreate /dev/sdb
```

##### 2. Create Volume Group

```bash
vgcreate cinder-volumes /dev/sdb
```

##### 3. Create Logical Volume

You can create either a fixed-size volume:

```bash
lvcreate -L 250G -n agent-01-lv-01 clvm-250g-agent-01
```

Or use all available space:

```bash
lvcreate -l 100%FREE -n prox-250g-nvme-agent1 prox-250g-nvme-agent1
```

##### 4. Format the Logical Volume

```bash
mkfs.ext4 /dev/clvm-250g-agent-01/agent-01-lv-01
```

#### Option B: Using Direct Partition

If using a direct partition instead of LVM:

```bash
mkfs.ext4 /dev/sdb1
```

### Mount Configuration

##### 1. Create Mount Directory

```bash
mkdir -p /mnt/primary1-agent1
```

##### 2. Get UUID of Storage Device

For LVM:

```bash
blkid /dev/clvm-250g-agent-01/agent-01-lv-01
```

For direct partition:

```bash
blkid /dev/sdb1
```

Example output:
```
/dev/mapper/clvm--250g--agent--01-agent--01--lv--01: UUID="bad54c26-0c33-499e-a109-b6887ab7cb46" TYPE="ext4"
```

##### 3. Add to /etc/fstab

Edit fstab:

```bash
nano /etc/fstab
```

Add entry (replace UUID with your actual UUID):

```
UUID=bad54c26-0c33-499e-a109-b6887ab7cb46   /mnt/primary1-agent1   ext4   defaults   0   0
```

##### 4. Test Mount

```bash
mount -a
```

Verify:

```bash
df -h | grep primary1-agent1
```

### Adding Storage in CloudStack UI

1. Navigate to **Infrastructure** → **Primary Storage**
2. Click **Add Primary Storage**
3. Configure:
   - **Scope**: Host
   - **Protocol**: FileSystem
   - **Path**: `/mnt/primary1-agent1`

## Part 6: Bridge Netfilter Configuration

Allow bridge traffic to bypass netfilter:

```bash
echo "net.bridge.bridge-nf-call-iptables = 0" >> /etc/sysctl.conf
echo "net.bridge.bridge-nf-call-arptables = 0" >> /etc/sysctl.conf
sysctl -p
```

## Verification

### Check KVM

```bash
# Verify KVM module is loaded
lsmod | grep kvm

# Check libvirt is running
systemctl status libvirtd

# List virtual networks
virsh net-list
```

### Check CloudStack Agent

```bash
# Check agent status
systemctl status cloudstack-agent

# View agent logs
tail -f /var/log/cloudstack/agent/agent.log
```

### Check Storage

```bash
# Verify mount points
df -h

# Check LVM volumes (if using LVM)
lvdisplay
vgdisplay
```

## Troubleshooting

### Agent Won't Start

```bash
# Check logs
tail -100 /var/log/cloudstack/agent/agent.log

# Verify libvirt connectivity
virsh list --all
```

### Storage Issues

```bash
# Check mount points
mount | grep primary

# Verify permissions
ls -la /mnt/primary1-agent1
```

### Network Issues

```bash
# Check bridge
brctl show

# Verify connectivity
ping <management-server-ip>
```

## Next Steps

Once KVM is installed and configured, proceed to [Final Steps](10-final-steps.md).

---

[← Previous: NFS Storage](08-nfs-storage.md) | [Next: Final Steps →](10-final-steps.md)