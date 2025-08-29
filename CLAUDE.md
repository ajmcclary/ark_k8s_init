# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is the ArkCase K8s Init repository - a collection of bash scripts for initializing and managing a local Kubernetes development environment for ArkCase. The project supports both Linux (Ubuntu/Debian) and macOS environments.

## Key Commands

### Environment Setup
- `./initialize` - Main initialization script that sets up the entire environment (Docker, Kubernetes, Helm, AWS CLI)
- `./create-arkcase-ca` - Creates the root Certificate Authority for ArkCase
- `./trust-arkcase-ca` - Trusts the ArkCase CA in the system certificate store
- `./clear-node` - Resets/clears the Kubernetes node and removes configurations

### Kubernetes Management
- `tools/k8s start` - Start Kubernetes and Docker services
- `tools/k8s stop` - Stop Kubernetes and Docker services  
- `tools/k8s restart` - Restart all services
- `tools/k8s unjam` - Restart Calico pods to resolve networking issues

### Utility Tools
- `tools/wait-pod` - Wait for pods to become ready
- `tools/wait-log` - Wait for specific log entries
- `tools/login-aws` - AWS authentication helper
- `tools/purge-old-kernels` - Linux-only kernel cleanup utility

## Architecture

### Directory Structure
- `/` - Main initialization scripts (initialize, clear-node, create-arkcase-ca, trust-arkcase-ca)
- `/.scripts/` - Supporting initialization scripts called by the main initialize script
- `/tools/` - Utility scripts installed to /usr/local/bin during initialization
- `/conf/` - Configuration files (HAProxy Ingress values, K8s dashboard, AWS pull configs)

### Script Execution Flow
1. **initialize** (entry point) → elevates privileges → calls sub-scripts:
   - OS detection and package installation (apt/brew)
   - Docker installation (Docker Desktop on macOS, docker-ce on Linux)
   - Kubernetes setup (kind on macOS, kubeadm on Linux)
   - Helm installation
   - AWS CLI setup
   - Certificate trust configuration
   - DNS resolver configuration

### Cross-Platform Compatibility
Scripts detect OS via `$OSTYPE` and use appropriate commands:
- **Package Management**: `apt-get` (Linux) vs `brew` (macOS)
- **Service Control**: `systemctl` (Linux) vs Docker Desktop GUI (macOS)
- **K8s Setup**: `kubeadm` (Linux) vs `kind` (macOS)
- **Certificate Trust**: `update-ca-certificates` (Linux) vs macOS Keychain
- **Path Resolution**: GNU readlink (Linux) vs Python-based resolution (macOS)

### Key Variables
- `K8S_VER` - Kubernetes version (default: 1.32)
- `HELM_VER` - Helm version (default: 3.15.2)
- `CRI_VER` - CRI-Docker version for Linux (default: 0.3.16)
- `CLUSTER_CIDR` - Kubernetes cluster CIDR
- `SERVICE_CIDR` - Kubernetes service CIDR

## Development Notes

- All scripts use bash with `set -euo pipefail` for error handling
- Scripts automatically escalate privileges when needed via sudo
- macOS requires Docker Desktop to be running before script execution
- The project uses Calico for container networking
- HAProxy is configured as the ingress controller
- Scripts support both root and non-root execution with automatic privilege escalation