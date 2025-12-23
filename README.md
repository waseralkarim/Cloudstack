# CloudStack Installation Guide

A comprehensive guide for installing and configuring Apache CloudStack 4.22 on Ubuntu 24.04 LTS with KVM hypervisor.

## Table of Contents

- [Overview](#overview)
- [Documentation Structure](#documentation-structure)
- [Quick Start](#quick-start)
- [Prerequisites](#prerequisites)
- [Support](#support)

## Overview

This guide walks you through setting up a complete CloudStack environment including:

- Management Server
- MySQL Database
- KVM Hypervisor Nodes
- NFS Storage (Primary & Secondary)
- Network Configuration
- Post-installation Configuration

## Documentation Structure

The installation process is organized into the following documents:

1. **[Preparation](01-preparation.md)** - Server requirements and initial setup
2. **[Network Configuration](02-network-configuration.md)** - Bridge setup and networking
3. **[MySQL Installation](03-mysql-installation.md)** - Database server setup
4. **[CloudStack Repository](04-cloudstack-repository.md)** - Adding CloudStack packages
5. **[Management Server](05-management-server.md)** - Installing the management server
6. **[Database Setup](06-database-setup.md)** - CloudStack database initialization
7. **[Management Initialization](07-management-initialization.md)** - Starting management services
8. **[NFS Storage](08-nfs-storage.md)** - Primary and secondary storage setup
9. **[KVM Hypervisor](09-kvm-hypervisor.md)** - Installing and configuring KVM hosts
10. **[Final Steps](10-final-steps.md)** - Completing the installation
11. **[Web UI Access](11-web-ui-access.md)** - Accessing CloudStack interface
12. **[Post-Installation](12-post-installation.md)** - Zone setup and configuration

## Quick Start

```bash
# Clone this repository
git clone <repository-url>
cd cloudstack-installation-guide

# Follow the documentation in order
cd docs
```

## Prerequisites

- Ubuntu 24.04 LTS servers
- Root or sudo access
- Static IP addresses configured
- Internet connectivity for package downloads
- Basic understanding of Linux system administration

### Minimum Hardware Requirements

| Component | Minimum Specs |
|-----------|--------------|
| Management Server | 4 vCPU, 8 GB RAM |
| KVM Host | 8 vCPU, 16 GB RAM |
| Storage Server | 4 vCPU, 8 GB RAM |

See [Preparation Guide](docs/01-preparation.md) for detailed requirements.

## Support

- **CloudStack Documentation**: https://docs.cloudstack.apache.org/
- **Community Support**: https://cloudstack.apache.org/mailing-lists.html
- **Issues**: Please report issues in this repository

## License

This documentation is provided as-is for educational and deployment purposes.

## Contributing

Contributions and improvements to this guide are welcome. Please submit pull requests or open issues for any corrections or enhancements.

---

**Note**: This guide is based on CloudStack 4.22. Always refer to the official CloudStack documentation for the latest updates and best practices.
