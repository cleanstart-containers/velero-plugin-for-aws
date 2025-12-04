# Velero Plugin for AWS - CleanStart Container

A containerized version of the Velero Plugin for AWS that enables Velero to backup and restore Kubernetes cluster resources to AWS S3 storage. The CleanStart Velero-Plugin-For-AWS image provides a production-ready, security-hardened container optimized for enterprise environments. Built on a minimal base OS with comprehensive security hardening, this image delivers reliable application execution with advanced security features.

**📌 CleanStart Foundation:** Security-hardened, minimal base OS designed for enterprise containerized environments.

**Image Path:** `cleanstart/velero-plugin-for-aws`

**Registry:** CleanStart Registry

---

## Overview

This container provides the **Velero Plugin for AWS** binary, packaged as a lightweight init container image. It is designed to be used as an init container in Velero deployments to make the AWS plugin available to the main Velero server. This Velero-Plugin-For-AWS container is part of the CleanStart application suite, featuring enterprise-grade security hardening, automated vulnerability management, and compliance with industry standards.

### What is Velero?

Velero is an open-source tool for safely backing up and restoring Kubernetes cluster resources and persistent volumes. It provides:

- **Backup and restore** of Kubernetes cluster resources
- **Disaster recovery** capabilities
- **Data migration** between clusters
- **Scheduled backups** with retention policies

### What Does This Plugin Do?

The Velero Plugin for AWS extends Velero's capabilities to use **Amazon S3** as a backup storage destination. This plugin enables:

- **S3 Storage Backend**: Store backups in AWS S3 buckets
- **AWS Integration**: Leverage AWS IAM, encryption, and lifecycle policies
- **Cross-Region Backups**: Store backups in any AWS region
- **Cost Optimization**: Use S3 storage classes (Standard, Glacier, etc.)

### How It Works

This container is used as an **init container** in Velero deployments:

1. **Init Container Phase**: The container runs before the main Velero container starts
2. **Plugin Copy**: It copies the Velero AWS plugin binary from `/plugins/velero-plugin-for-aws` to `/target/velero-plugin-for-aws` on a shared volume
3. **Main Container Phase**: The Velero server container starts and reads the plugin from `/plugins/` on the same shared volume
4. **Result**: Velero can now use AWS S3 as a backup storage destination

---

## About CleanStart

CleanStart is a comprehensive container registry providing security-hardened, enterprise-ready container images. Our images are designed with security-first principles, featuring minimal attack surfaces, regular security updates, and compliance with industry standards.

### About CleanStart Images

CleanStart images are built on secure, minimal base operating systems and optimized for production environments. Each image undergoes rigorous security testing, vulnerability scanning, and compliance validation to ensure enterprise-grade security and reliability.

---

## 🖼️ Image Information

**Image Name:** `cleanstart/velero-plugin-for-aws:latest-dev`

**Image Details:**
- **Base Architecture**: `amd64`
- **OS**: `linux`
- **Entrypoint**: `/usr/bin/cp-plugin /plugins/velero-plugin-for-aws /target/velero-plugin-for-aws`
- **User**: `clnstrt` (non-root, UID 1000)
- **SSL Certificates**: Pre-configured at `/etc/ssl/certs/ca-certificates.crt`
- **Size**: ~76MB (plugin binary)

**Security Features:**
- Runs as non-root user (`clnstrt`, UID 1000)
- Minimal attack surface (single-purpose container)
- SSL/TLS certificates pre-configured
- Security context with dropped capabilities

---

## Key Features

- **Security-First Design**: Built with minimal attack surfaces and security hardening
- **Enterprise Compliance**: Meets industry standards including FIPS, STIG, and CIS benchmarks
- **Regular Updates**: Automated security patches and vulnerability management
- **Multi-Architecture Support**: Available for AMD64 and ARM64 architectures
- **Production Ready**: Optimized for enterprise deployment and scaling
- **Comprehensive Documentation**: Detailed guides and best practices for each image
- Lightweight init container for Velero AWS plugin integration
- AWS S3 storage backend support
- Automated plugin binary deployment
- Non-root execution for enhanced security

---

## Use Cases

Typical scenarios where this container excels:

1. **Kubernetes Backup to S3**: Backup your Kubernetes cluster resources to AWS S3
2. **Disaster Recovery**: Restore cluster state from S3 backups
3. **Cluster Migration**: Migrate workloads between clusters using S3 as intermediary storage
4. **Compliance**: Meet backup and retention requirements using S3 lifecycle policies
5. **Multi-Region Backups**: Store backups in different AWS regions for redundancy
6. **Cost-Effective Storage**: Leverage S3 storage classes for optimized backup costs

---

## Quick Start

### Pull Commands
```bash
docker pull cleanstart/velero-plugin-for-aws:latest
docker pull cleanstart/velero-plugin-for-aws:latest-dev
```

### Run Commands

Basic test:
```bash
docker run -it --name velero-plugin-for-aws-test cleanstart/velero-plugin-for-aws:latest-dev
```

Production deployment:
```bash
docker run -d --name velero-plugin-for-aws-prod \
  --read-only \
  --security-opt=no-new-privileges \
  --user 1000:1000 \
  cleanstart/velero-plugin-for-aws:latest
```

### Testing

Test the plugin functionality without requiring AWS or Helm by using the test deployment in the `kubernetes/` directory. This deploys a test environment that verifies the plugin binary is correctly copied and available.

For detailed testing instructions and production deployment steps, see `kubernetes/README.md`.

---

## 🔧 Technical Details

### Container Entrypoint

The container uses a custom entrypoint script (`/usr/bin/cp-plugin`) that:

- Copies the plugin binary from the source location (`/plugins/velero-plugin-for-aws`)
- Places it in the target location (`/target/velero-plugin-for-aws`)
- Ensures proper permissions and ownership

### Volume Requirements

- **Init Container**: Mount shared volume at `/target` (write access)
- **Velero Container**: Mount same volume at `/plugins` (read access)
- **Volume Type**: `emptyDir` for ephemeral storage, or persistent volume for persistence

### Integration Pattern

The plugin is integrated by adding it as an init container alongside the main Velero container. The init container copies the plugin binary to a shared volume, which is then mounted in the Velero container at the `/plugins` directory. This allows Velero to discover and load the AWS plugin automatically.

---

## Architecture Support

CleanStart images support multiple architectures to ensure compatibility across different deployment environments:

- **AMD64**: Intel and AMD x86-64 processors
- **ARM64**: ARM-based processors including Apple Silicon and ARM servers

### Architecture-based Pull Commands
```bash
docker pull --platform linux/amd64 cleanstart/velero-plugin-for-aws:latest
docker pull --platform linux/arm64 cleanstart/velero-plugin-for-aws:latest
```

---

## 📚 Documentation

- **Kubernetes Deployment Guide**: See `kubernetes/README.md` for detailed testing and production deployment instructions
- **Integration Examples**: See `kubernetes/deployment.yaml` for complete YAML examples

**Note**: This container is designed to work with Velero. See `kubernetes/README.md` for production deployment instructions.

---

## Resources

- **Official Documentation:** https://velero.io/docs/
- **Velero AWS Plugin Documentation:** https://github.com/vmware-tanzu/velero-plugin-for-aws
- **Provenance / SBOM / Signature:** https://images.cleanstart.com/images/velero-plugin-for-aws
- **Docker Hub:** https://hub.docker.com/r/cleanstart/velero-plugin-for-aws
- **CleanStart All Images:** https://images.cleanstart.com
- **CleanStart Community Images:** https://hub.docker.com/u/cleanstart

---

## Disclaimer & License

### Disclaimer

**Disclaimer:** This documentation is provided for informational purposes only. Users are responsible for ensuring compliance with applicable laws, regulations, and security requirements. CleanStart makes no warranties regarding the suitability of these images for specific use cases or environments.

### License

Apache-2.0

---

## Vulnerability Disclaimer

CleanStart offers Docker images that include third-party open-source libraries and packages maintained by independent contributors. While CleanStart maintains these images and applies industry-standard security practices, it cannot guarantee the security or integrity of upstream components beyond its control.

Users acknowledge and agree that open-source software may contain undiscovered vulnerabilities or introduce new risks through updates. CleanStart shall not be liable for security issues originating from third-party libraries, including but not limited to zero-day exploits, supply chain attacks, or contributor-introduced risks.

**Security remains a shared responsibility:** CleanStart provides updated images and guidance where possible, while users are responsible for evaluating deployments and implementing appropriate controls.
