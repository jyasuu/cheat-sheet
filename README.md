# Technical Cheat Sheet 🔧

## Table of Contents
1. [Terminal Tools](#-terminal-tools)
2. [Containerization](#-containerization)
3. [Databases](#-databases)
4. [Orchestration](#-orchestration)
5. [Editors & IDEs](#-editors--ides)
6. [Package Management](#-package-management)

---

## 🖥️ Terminal Tools

### 🌟 Starship Cross-Shell Prompt
**Core Function**: Unified prompt for all shells  
```bash
# One-line install & activation
curl -sS https://starship.rs/install.sh | sh && eval "$(starship init bash)"
```
📚 [Configuration Guide](https://starship.rs/config/)

---

## 📦 Containerization

### 🐳 Podman (Windows)
```powershell
# Full lifecycle management
winget install -e --id RedHat.Podman
podman machine init --rootful
podman machine start

# Clean removal script
podman machine stop && podman machine rm -f
winget uninstall -e --id RedHat.{Podman,Podman-Desktop}

# Volume mapping example
podman run -it --rm -v ${HOME}/data:/app/data alpine:latest
```

### 🐋 Docker Quick Reference
```bash
# Container lifecycle cheatsheet
docker build -t myapp . && docker run -dp 3000:3000 myapp
docker compose up --detach --build
```
🚀 [Advanced Patterns](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

---

## 🗃️ Databases

### 📊 MongoDB
**Connection Template**:
```powershell
mongosh "mongodb+srv://<cluster>.mongodb.net/" --apiVersion 1 --username <user>
```

**Indexed Query Example**:
```javascript
// Create index first
db.inventory.createIndex({item: 1})

// Optimized find operation
db.inventory.find(
  { item: { $in: ["paper"] } },
  { projection: { _id: 0, item: 1 } }
).explain("executionStats")
```

### 🐘 PostgreSQL Performance
```sql
-- Long-running queries + termination
SELECT pg_terminate_backend(pid)
FROM pg_stat_activity 
WHERE state = 'active' 
AND now() - query_start > '5 minutes'::interval;
```

---

## ☸️ Orchestration

### Kubernetes Survival Kit
```bash
# Cluster bootstrap
kubectl create ns demo && kubectl config set-context --current --namespace=demo

# Deployment pipeline
kubectl apply -f deployment.yaml
kubectl rollout status deployment/myapp
kubectl port-forward svc/myapp 8080:80

# Debug arsenal
kubectl get events --sort-by=.metadata.creationTimestamp
kubectl debug -it pod/myapp --image=busybox -- sh
```
🔍 [Production Checklist](https://kubernetes.io/docs/tasks/debug/debug-cluster/)

---

## ⌨️ Editors & IDEs

### ⎔ NeoVim Supercharge
**Windows Setup**:
```powershell
# Clean install script
irm https://raw.githubusercontent.com/LazyVim/starter/main/install.ps1 | iex
```

**Essential Plugins**:
```lua
-- Add to config.lua
return {
  "nvim-treesitter/nvim-treesitter",
  "williamboman/mason.nvim",
  "mfussenegger/nvim-dap"
}
```

---

## 📦 Package Management

### Vcpkg Power User Setup
```powershell
# Bootstrap with manifest support
git clone https://github.com/microsoft/vcpkg
./vcpkg/bootstrap-vcpkg.bat -disableMetrics

# Multi-arch workflow
vcpkg install --triplet=x64-windows-static fmt spdlog
vcpkg integrate powershell
```
```cmake
# CMake Integration
find_package(fmt CONFIG REQUIRED)
target_link_libraries(myapp PRIVATE fmt::fmt)
```

---
