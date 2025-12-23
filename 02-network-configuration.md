# Section 2: Network Configuration

CloudStack requires a Linux bridge bound to a physical network interface for handling various traffic types (public, guest, storage).

## Requirements

- A Linux bridge (e.g., `cloudbr0`)
- Bound to a physical NIC (e.g., `ens18`)
- Used by CloudStack KVM traffic (public/guest/storage)

## Configuration Steps

### 1. Identify Your Network Interface

First, identify your primary network interface:

```bash
ip addr show
```

Look for your main interface (commonly `ens18`, `eth0`, or similar).

### 2. Configure Netplan

Edit the netplan configuration file:

```bash
nano /etc/netplan/00-installer-config.yaml
```

### 3. Add Bridge Configuration

Replace the contents with the following configuration (adjust values for your network):

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens18:
      dhcp4: false
      dhcp6: false
  bridges:
    cloudbr0:
      interfaces: [ens18]
      dhcp4: false
      addresses:
        - 192.168.7.10/24
      routes:
        - to: default
          via: 192.168.7.1
      nameservers:
        addresses: [8.8.8.8]
      parameters:
        stp: false
        forward-delay: 0
```

**Configuration parameters explained:**
- `ens18` - Your physical interface (replace with your actual interface name)
- `192.168.7.10/24` - Static IP address for your server
- `192.168.7.1` - Your default gateway
- `8.8.8.8` - DNS server (you can use your preferred DNS)
- `stp: false` - Disables Spanning Tree Protocol
- `forward-delay: 0` - Sets bridge forward delay to 0

### 4. Apply Network Configuration

Apply the netplan configuration:

```bash
netplan apply
```

### 5. Verify Configuration

Check that the bridge is created and working:

```bash
# Check bridge status
ip addr show cloudbr0

# Check bridge details
brctl show

# Test connectivity
ping -c 4 8.8.8.8
```

## Troubleshooting

If you lose connectivity after applying netplan:

1. **Check netplan syntax:**
   ```bash
   netplan --debug apply
   ```

2. **Revert changes if needed:**
   - If you have physical access or console access, you can restore the original configuration
   - Keep a backup of your original netplan file before making changes

3. **Common issues:**
   - Incorrect indentation in YAML (use spaces, not tabs)
   - Wrong interface name
   - Incorrect IP address or gateway

## Important Notes

- ⚠️ **Warning**: Network configuration changes can disconnect your SSH session. Ensure you have console access or physical access to the server before making changes.
- Always test configuration with `netplan try` first if available
- Keep a backup of your working configuration

## Next Steps

Once network configuration is complete, proceed to [MySQL Installation](03-mysql-installation.md).

---

[← Previous: Preparation](01-preparation.md) | [Next: MySQL Installation →](03-mysql-installation.md)
