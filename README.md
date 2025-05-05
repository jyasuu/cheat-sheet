
# Technical Cheat Sheet

## 🌟 Starship Cross-Shell Prompt
**Installation & Setup**
```bash
curl -sS https://starship.rs/install.sh | sh
eval "$(starship init bash)"

rustup update stable
rustup default $RUST_VERSION # stable-x86_64-unknown-linux-gnu
```
[Official Docs](https://github.com/starship/starship?tab=readme-ov-file)

---

## 🦀 Rust Cargo Package Manager
**Common Commands**
```bash
# Create a new project
cargo new my_project

# Build the project
cargo build

# Build the project in release mode
cargo build --release

# Run the project
cargo run

# Test the project
cargo test

# Check the project without building
cargo check

# Add a dependency
cargo add <crate-name> --features a,b,c

# Update dependencies
cargo update

# Clean the build artifacts
cargo clean

# Publish to crates.io
cargo publish

# Generate and open documentation for the project
cargo doc --open

```

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
kubectl get svc  -n demo-ns -o wide

# Debugging Tools
kubectl run -n demo-ns -it --rm --image=curlimages/curl:8.1.2 curly -- /bin/sh
kubectl exec -n demo-ns -it whoami -- /bin/bash

# Cleanup Resources
kubectl delete svc whoami-svc -n demo-ns
kubectl delete pod whoami -n demo-ns
kubectl delete pod curly -n demo-ns

# Others

kubectl get services -n demo-ns
kubectl get ingresses -n demo-ns
kubectl describe service whoami-svc -n demo-ns
kubectl rollout restart deployment.apps/spring-boot-app -n demo-ns
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


## Spring Boot

```sh

curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
sdk install java 17.0.10-zulu
sdk install springboot

spring init openai --build=maven --java-version=17  \
--name openai --packaging jar  \
--description 'openai application'  --artifact-id openai \
--boot-version 3.2.2  --dependencies=web,lombok,docker-compose,spring-ai-openai,spring-ai-vectordb-elasticsearch,spring-ai-vectordb-cassandra,spring-ai-vectordb-redis,spring-ai-vectordb-mongodb-atlas,spring-ai-vectordb-pgvector  \
--group-id org.jyasu --extract --force

spring init ollama --build=maven --java-version=17  \
--name ollama --packaging jar  \
--description 'ollama application'  --artifact-id ollama \
--boot-version 3.2.2  --dependencies=web,lombok,docker-compose,spring-ai-ollama  \
--group-id org.jyasu --extract --force


spring init postgresql --build=maven --java-version=17  \
--name postgresql --packaging jar  \
--description 'postgresql application'  --artifact-id postgresql \
--boot-version 3.2.2  --dependencies=web,lombok,docker-compose,postgresql,data-jpa,actuator,distributed-tracing  \
--group-id org.jyasu --extract --force



spring init cassandra --build=maven --java-version=17  \
--name cassandra --packaging jar  \
--description 'cassandra application'  --artifact-id cassandra \
--boot-version 3.2.2  --dependencies=web,lombok,docker-compose,data-cassandra,data-cassandra-reactive  \
--group-id org.jyasu --extract --force



spring init mongodb --build=maven --java-version=17  \
--name mongodb --packaging jar  \
--description 'mongodb application'  --artifact-id mongodb \
--boot-version 3.2.2  --dependencies=web,lombok,docker-compose,data-mongodb,data-mongodb-reactive,graphql  \
--group-id org.jyasu --extract --force


spring init elasticsearch --build=maven --java-version=17  \
--name elasticsearch --packaging jar  \
--description 'elasticsearch application'  --artifact-id elasticsearch \
--boot-version 3.2.2  --dependencies=web,lombok,docker-compose,data-elasticsearch  \
--group-id org.jyasu --extract --force


spring init redis --build=maven --java-version=17  \
--name redis --packaging jar  \
--description 'redis application'  --artifact-id redis \
--boot-version 3.2.2  --dependencies=web,lombok,docker-compose,data-redis,data-redis-reactive  \
--group-id org.jyasu --extract --force


spring init kafka --build=maven --java-version=17  \
--name kafka --packaging jar  \
--description 'kafka application'  --artifact-id kafka \
--boot-version 3.2.2  --dependencies=web,lombok,docker-compose,kafka,kafka-streams  \
--group-id org.jyasu --extract --force

spring init amqp --build=maven --java-version=17  \
--name amqp --packaging jar  \
--description 'amqp application'  --artifact-id amqp \
--boot-version 3.2.2  --dependencies=web,lombok,docker-compose,amqp-streams,amqp  \
--group-id org.jyasu --extract --force


spring init vault --build=maven --java-version=17  \
--name vault --packaging jar  \
--description 'vault application'  --artifact-id vault \
--boot-version 3.2.2  --dependencies=web,lombok,docker-compose,cloud-starter-vault-config  \
--group-id org.jyasu --extract --force

spring init shell --build=maven --java-version=17  \
--name shell --packaging jar  \
--description 'shell application'  --artifact-id shell \
--boot-version 3.2.2  --dependencies=lombok,docker-compose,native,spring-shell  \
--group-id org.jyasu --extract --force

spring init actuator --build=maven --java-version=17  \
--name actuator --packaging jar  \
--description 'actuator application'  --artifact-id actuator \
--boot-version 3.2.2  --dependencies=web,lombok,actuator  \
--group-id org.jyasu --extract --force


spring init example --build=maven --java-version=17 --artifact-id example --boot-version 3.3.7  --group-id com.jyasu --packaging=jar -d=web,jdbc,lombok,docker-compose,postgresql,data-jpa,actuator --name example --extract --force


```

## curl

```sh
curl --resolve example.com:443:127.0.0.1 https://example.com
curl --output example.html "https://example.com/"
curl --header "PRIVATE-TOKEN: ?" https://example.com/
curl  --basic --user 'test:test' https://example.com/
curl -fsSL https://example.com/install.sh | sh
curl --ftp-ssl --user "test:test" -l sftp://example.com:22/ --key ./id_rsa --pubkey ./id_rsa.pub
curl --request POST  --data "A=B&C=D" https://example.com
curl --request POST  --form "A=B" --form "C=D" https://example.com
curl --upload-file test.txt https://example.com
curl --ftp-ssl --user test:test -l ftp://example.com:21
```


## dd

```sh
dd if=/dev/zero of=./testfile bs=1G count=1 oflag=direct
# 1+0 records in
# 1+0 records out
# 1073741824 bytes (1.1 GB, 1.0 GiB) copied, 4.76527 s, 225 MB/s
```
