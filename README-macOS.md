# ArkCase K8s Init - macOS Compatibility

This repository has been updated to support macOS in addition to Linux. The scripts now automatically detect the operating system and use appropriate commands.

## Prerequisites for macOS

1. **Homebrew** - Package manager for macOS
   - Will be automatically installed if not present
   - https://brew.sh

2. **Docker Desktop** - Required for Docker and Kubernetes support
   - Install manually from https://www.docker.com/products/docker-desktop
   - Or via Homebrew: `brew install --cask docker`
   - Ensure Kubernetes is enabled in Docker Desktop preferences

3. **Python 3** - Required for readlink compatibility
   - Usually pre-installed on macOS
   - If not: `brew install python3`

## Key Differences from Linux

### Package Management
- **Linux**: Uses `apt-get` (Ubuntu/Debian)
- **macOS**: Uses `brew` (Homebrew)

### Service Management
- **Linux**: Uses `systemctl` for service control
- **macOS**: Uses Docker Desktop GUI or `launchctl`

### Kubernetes Setup
- **Linux**: Uses `kubeadm` to initialize cluster
- **macOS**: Uses Docker Desktop's built-in Kubernetes or `kind` (Kubernetes in Docker)

### Certificate Management
- **Linux**: Uses `update-ca-certificates` or `update-ca-trust`
- **macOS**: Uses macOS Keychain (`security` command)

### File Paths
- **Linux**: Standard paths like `/usr/bin/date`
- **macOS**: BSD-style paths like `/bin/date`

## Running the Scripts

1. **Initialize the environment:**
   ```bash
   ./initialize
   ```

2. **Create ArkCase CA:**
   ```bash
   ./create-arkcase-ca
   ```

3. **Trust ArkCase CA:**
   ```bash
   ./trust-arkcase-ca
   ```

4. **Clear node (reset):**
   ```bash
   ./clear-node
   ```

## macOS-Specific Notes

### Docker Desktop
- Must be running before executing scripts
- Kubernetes must be enabled in Docker Desktop settings
- The scripts will wait for Docker to be available

### kind (Alternative to Docker Desktop Kubernetes)
- If Docker Desktop's Kubernetes is not available, the scripts will install and use `kind`
- kind creates a Kubernetes cluster inside Docker containers
- Useful for development and testing

### Swap Management
- macOS handles virtual memory differently than Linux
- Swap disabling is not required or performed on macOS

### Kernel Updates
- macOS doesn't have user-manageable kernel versions like Linux
- Kernel update checks are skipped on macOS

## Troubleshooting

### Docker Not Running
If you see "Docker is not running" error:
1. Open Docker Desktop from Applications
2. Wait for it to fully start
3. Re-run the script

### Permission Issues
- Scripts will automatically request sudo access when needed
- You may be prompted for your password

### Homebrew Installation
- If Homebrew installation fails, install manually from https://brew.sh
- Then re-run the scripts

### Certificate Trust Issues
- macOS may prompt you to trust certificates
- You'll need administrator access to add certificates to the System keychain

## Compatibility Status

✅ **Fully Compatible:**
- initialize
- create-arkcase-ca
- trust-arkcase-ca
- clear-node
- tools/k8s

⚠️ **May Need Additional Testing:**
- Scripts in .scripts/ directory that interact with specific Kubernetes configurations
- Advanced networking configurations

## Contributing

When adding new scripts or features, please ensure:
1. Use OS detection: `if [[ "$OSTYPE" == "darwin"* ]]`
2. Provide both Linux and macOS implementations
3. Test on both platforms when possible