# Velero Plugin for AWS - CleanStart Container

A containerized version of the Velero Plugin for AWS that enables Velero to backup and restore Kubernetes cluster resources to AWS S3 storage.

## Overview

This container provides the **Velero Plugin for AWS** binary, packaged as a lightweight init container image. It is designed to be used as an init container in Velero deployments to make the AWS plugin available to the main Velero server.

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

## Use Cases

1. **Kubernetes Backup to S3**: Backup your Kubernetes cluster resources to AWS S3
2. **Disaster Recovery**: Restore cluster state from S3 backups
3. **Cluster Migration**: Migrate workloads between clusters using S3 as intermediary storage
4. **Compliance**: Meet backup and retention requirements using S3 lifecycle policies
5. **Multi-Region Backups**: Store backups in different AWS regions for redundancy

## Quick Start

Test the plugin functionality without requiring AWS or Helm by using the test deployment in the `kubernetes/` directory. This deploys a test environment that verifies the plugin binary is correctly copied and available.

For detailed testing instructions and production deployment steps, see `kubernetes/README.md`.

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

## 📚 Documentation

- **Kubernetes Deployment Guide**: See `kubernetes/README.md` for detailed testing and production deployment instructions
- **Integration Examples**: See `kubernetes/deployment.yaml` for complete YAML examples

**Note**: This container is designed to work with Velero. See `kubernetes/README.md` for production deployment instructions.

