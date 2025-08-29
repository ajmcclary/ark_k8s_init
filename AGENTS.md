# Repository Guidelines

## Project Structure & Module Organization
- Root scripts: `./initialize`, `./create-arkcase-ca`, `./trust-arkcase-ca`, `./clear-node` (entry points).
- `.scripts/`: implementation modules (e.g., `init-*`, `wait-for-pods`, `init-kubeconfig`). Called by `initialize`.
- `tools/`: helper commands (`k8s`, `login-aws`, `wait-pod`, etc.). Safe to run directly.
- `conf/`: Kubernetes/Helm manifests and values (e.g., `k8s-dashboard.yaml`, `haproxy-ingress-values.yaml`).
- `README-macOS.md`: macOS prerequisites and behavior differences.

## Build, Test, and Development Commands
- Bootstrap environment: `./initialize` — installs Docker/Helm/kubectl, configures local cluster (Docker Desktop/kind on macOS; kubeadm on Linux), and waits for pods.
- Certificates: `./create-arkcase-ca` then `./trust-arkcase-ca` — generates and trusts ArkCase Root CA.
- Reset: `./clear-node` — cleans/reinitializes local node state.
- Helpers: `./tools/k8s ...` (kubectl wrapper), `./tools/wait-pod`, `./tools/login-aws`.
- Apply examples: `./tools/k8s apply -f conf/k8s-dashboard.yaml`.

## Coding Style & Naming Conventions
- Language: Bash with `#!/bin/bash` and `set -euo pipefail`.
- OS handling: use `[[ "$OSTYPE" == "darwin"* ]]` branches; avoid platform-specific paths when possible.
- Structure: small functions (`say/ok/doing/err/fail`), constants via uppercase vars, quote all expansions, prefer arrays for package lists.
- Naming: kebab-case executables (`init-*`, `wait-*`), descriptive file names; place reusable logic under `.scripts/` or `tools/`.

## Testing Guidelines
- Lint: `shellcheck initialize .scripts/* tools/*` (fix warnings before PR).
- Sanity checks: `docker ps`, `helm version`, `kubectl --context local get nodes` (or `kind-local` on macOS).
- Validate manifests: `./tools/k8s apply --dry-run=client -f conf/*.yaml`.
- Prefer testing on both Linux and macOS when changing OS branches.

## Commit & Pull Request Guidelines
- Commits: short, imperative subject (e.g., "Bump K8s to 1.32", "BUGFIX: k8s helper"); include scope when helpful.
- PRs must include: purpose, key changes, affected scripts, platforms tested (Linux/macOS), and sample outputs (e.g., `kubectl get nodes`, versions).
- Link related issues; add screenshots/logs for failures or UI-related steps.

## Security & Configuration Tips
- Scripts escalate with `sudo`; do not embed secrets. AWS config is created per-user (`tools/login-aws`).
- Do not commit generated keys/certs; CA trust is handled by `trust-arkcase-ca`.
- Respect env overrides like `CLUSTER_CIDR` and `SERVICE_CIDR` when provided.
