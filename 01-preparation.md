# Section 1: Preparation

This section covers the initial setup and requirements for your CloudStack deployment.

## Server Requirements

### Hardware Specifications

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| Management Server | 4 vCPU, 8 GB RAM | 8 vCPU, 16 GB RAM |
| KVM Host | 8 vCPU, 16 GB RAM | 16 vCPU, 32 GB RAM |
| Storage Server | 4 vCPU, 8 GB RAM | 8 vCPU, 16 GB RAM |

### Software Requirements

| Component | Version |
|-----------|---------|
| Operating System | Ubuntu 24.04 LTS |
| Virtualization | KVM + libvirt |

## Installation Steps

### 1. Update System Packages

Update your system to ensure all packages are current:

```bash
apt update -y && apt upgrade -y
```

### 2. Install Required Packages

Install basic utilities needed for CloudStack:

```bash
apt install -y vim htop curl wget net-tools bridge-utils nfs-kernel-server duf lynx
```

**Package descriptions:**
- `vim` - Text editor
- `htop` - System monitoring tool
- `curl`, `wget` - Download utilities
- `net-tools` - Network configuration tools
- `bridge-utils` - Network bridge management
- `nfs-kernel-server` - NFS server for storage
- `duf` - Disk usage utility
- `lynx` - Text-based web browser

### 3. Enable KVM Nested Virtualization (if running on VM)

If you're setting up CloudStack in a nested virtualization environment (VM on top of another hypervisor), enable nested virtualization:

**For Hyper-V (Windows Host):**
```powershell
Set-VMProcessor -VMName <your-vm-name> -ExposeVirtualizationExtensions $true
```

**For VMware:**
Enable "Virtualize Intel VT-x/EPT or AMD-V/RVI" in VM settings.

**For KVM/QEMU:**
```bash
# Check if nested virtualization is enabled
cat /sys/module/kvm_intel/parameters/nested  # For Intel
cat /sys/module/kvm_amd/parameters/nested    # For AMD
```

## Verification

Verify your system is ready:

```bash
# Check Ubuntu version
lsb_release -a

# Check available resources
free -h
lscpu
df -h

# Verify nested virtualization (if applicable)
egrep -c '(vmx|svm)' /proc/cpuinfo
```

## Next Steps

Once preparation is complete, proceed to [Network Configuration](02-network-configuration.md).

---

[← Back to README](README.md) | [Next: Network Configuration →](02-network-configuration.md)
