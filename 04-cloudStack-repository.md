# Section 4: CloudStack Repository Setup

This section covers adding the CloudStack package repository to your system.

## Setup Steps

### 1. Create Keyrings Directory

```bash
mkdir -p /etc/apt/keyrings
```

### 2. Download and Add CloudStack GPG Key

```bash
wget -O- http://packages.shapeblue.com/release.asc \
  | gpg --dearmor | tee /etc/apt/keyrings/cloudstack.gpg > /dev/null
```

### 3. Add CloudStack Repository

```bash
echo "deb [signed-by=/etc/apt/keyrings/cloudstack.gpg] \
http://packages.shapeblue.com/cloudstack/upstream/debian/4.22 /" \
 | tee /etc/apt/sources.list.d/cloudstack.list
```

### 4. Update Package Lists

```bash
apt update
```

## Verification

Verify the repository was added successfully:

```bash
# Check repository file
cat /etc/apt/sources.list.d/cloudstack.list

# Search for CloudStack packages
apt search cloudstack-management
```

You should see CloudStack packages available in the output.

## Understanding the Setup

- **ShapeBlue Packages**: ShapeBlue maintains CloudStack packages for various distributions
- **Version 4.22**: This guide uses CloudStack 4.22 (adjust version number for different releases)
- **GPG Key**: Ensures package authenticity and integrity

## Troubleshooting

If you encounter GPG errors:

```bash
# Remove old key if exists
rm /etc/apt/keyrings/cloudstack.gpg

# Re-download the key
wget -O- http://packages.shapeblue.com/release.asc \
  | gpg --dearmor | tee /etc/apt/keyrings/cloudstack.gpg > /dev/null

# Update again
apt update
```

## Next Steps

Once the repository is configured, proceed to [Management Server Installation](05-management-server.md).

---

[← Previous: MySQL Installation](03-mysql-installation.md) | [Next: Management Server →](05-management-server.md)
