
# Technical Cheat Sheet

## 🌟 Starship Cross-Shell Prompt
**Installation & Setup**
```bash
curl -sS https://starship.rs/install.sh | sh
eval "$(starship init bash)"
```
[Official Docs](https://github.com/starship/starship?tab=readme-ov-file)

---

## 🐳 Podman Container Engine
**Windows Installation**
```powershell
winget install -e --id RedHat.Podman
winget install -e --id RedHat.Podman-Desktop 

# Initialize Machine
podman machine init 
podman machine set --rootful
podman machine start

# Uninstallation
podman machine stop
podman machine rm -f
winget uninstall -e --id RedHat.Podman
winget uninstall -e --id RedHat.Podman-Desktop

# Common Commands
podman run --rm -it -v E:\data:/mnt/data alpine sh
```

---

## 📊 MongoDB Operations
**Connection Command**
```powershell
.\mongosh.exe "mongodb+srv://?/" --apiVersion 1 --username ?
```

**Query Examples**
```javascript
// Insert test data
db.inventory.insertMany([
   { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "A" },
   { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
   { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
   { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" }
]);

// Find documents
db.inventory.find({"item": {"$in": ["paper"]}},{"item": 1})
```

---

## 🐋 Docker Quick Reference
[Command Cheat Sheet](https://cheat.sh/docker)

---

## ☸️ Kubernetes Commands
**Essential Operations**
```bash
# Namespace Management
kubectl create ns demo-ns

# Pod Operations
kubectl run whoami --image=traefik/whoami -n demo-ns
kubectl get pods -n demo-ns -o wide

# Service Exposure
kubectl expose --type=NodePort pod whoami --port=80 --name=whoami-svc -n demo-ns

# Debugging Tools
kubectl run -n demo-ns -it --rm --image=curlimages/curl:8.1.2 curly -- /bin/sh
kubectl exec -n demo-ns -it whoami -- /bin/bash

# Cleanup Resources
kubectl delete svc whoami-svc -n demo-ns
```
[Full Command Reference](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)

---

## 🐘 PostgreSQL Monitoring
**Long-Running Query Check**
```sql
SELECT now() - query_start, * 
FROM pg_catalog.pg_stat_activity psa 
WHERE state = 'active'
AND now() - query_start > interval '5 minute';
```

---

## ⎔ Neovim Setup
**Windows Installation**
```powershell
winget install Neovim.Neovim
```

**LazyVim Configuration**
```powershell
# Backup existing config
Move-Item $env:LOCALAPPDATA\nvim $env:LOCALAPPDATA\nvim.bak

# Initialize LazyVim
git clone https://github.com/LazyVim/starter $env:LOCALAPPDATA\nvim
Remove-Item $env:LOCALAPPDATA\nvim\.git -Recurse -Force
nvim
```
[Extensions](https://www.lazyvim.org/extras)｜[Java Setup](https://github.com/nvim-java/nvim-java/wiki/Lazyvim)

---

## 📦 Vcpkg C++ Package Manager
**Initialization**
```powershell
git clone https://github.com/microsoft/vcpkg
cd vcpkg; .\bootstrap-vcpkg.bat

# Environment Setup
$env:VCPKG_ROOT = "D:\git\vcpkg"
$env:PATH = "$env:VCPKG_ROOT;$env:PATH"

# Package Installation
vcpkg install libxml2:x64-windows
vcpkg integrate install
```
[Official Guide](https://learn.microsoft.com/en-us/vcpkg/get_started/get-started?pivots=shell-powershell)

---
