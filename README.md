# 🧠 Technical Cheat Sheet

A personal collection of CLI tools, language environments, and automation tips for system development and DevOps work.

---

## 🌟 Starship — Cross-Shell Prompt

**Installation & Setup**

```bash
curl -sS https://starship.rs/install.sh | sh
eval "$(starship init bash)"

rustup update stable
rustup default $RUST_VERSION  # e.g., stable-x86_64-unknown-linux-gnu
```

🔗 [Starship Official Docs](https://github.com/starship/starship?tab=readme-ov-file)

---

## 🦀 Rust — Cargo Package Manager

**Common Commands**

```bash
cargo new my_project             # Create new project
cargo build                      # Compile project
cargo build --release            # Compile optimized binary
cargo run                        # Run main.rs
cargo test                       # Run tests
cargo check                      # Type-check only
cargo add <crate> --features a,b # Add dependencies
cargo update                     # Update Cargo.lock
cargo clean                      # Remove build artifacts
cargo publish                    # Publish to crates.io
cargo doc --open                 # Generate and open docs
```

---

## 🐳 Podman — Container Engine

**Windows Installation**

```powershell
winget install -e --id RedHat.Podman
winget install -e --id RedHat.Podman-Desktop
```

**Initialize Podman**

```powershell
podman machine init
podman machine set --rootful
podman machine start
```

**Uninstallation**

```powershell
podman machine stop
podman machine rm -f
winget uninstall -e --id RedHat.Podman
winget uninstall -e --id RedHat.Podman-Desktop
```

**Run Example**

```bash
podman run --rm -it -v E:\data:/mnt/data alpine sh
```

---

## 📊 MongoDB — Quick Operations

**Connection**

```powershell
.\mongosh.exe "mongodb+srv://?/" --apiVersion 1 --username ?
```

**Basic Queries**

```javascript
db.inventory.insertMany([
  { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
  ...
]);

db.inventory.find({ item: { $in: ["paper"] } }, { item: 1 });
```

---

## 🐋 Docker — Quick Reference

🔗 [cheat.sh/docker](https://cheat.sh/docker)

---

## ☸️ Kubernetes — Common Commands

**Basic Operations**

```bash
kubectl create ns demo-ns
kubectl run whoami --image=traefik/whoami -n demo-ns
kubectl expose pod whoami --type=NodePort --port=80 --name=whoami-svc -n demo-ns
kubectl get pods -n demo-ns -o wide
kubectl get svc -n demo-ns -o wide
```

**Debugging**

```bash
kubectl run -n demo-ns -it --rm --image=curlimages/curl:8.1.2 curly -- /bin/sh
kubectl exec -n demo-ns -it whoami -- /bin/bash
```

**Cleanup**

```bash
kubectl delete svc whoami-svc -n demo-ns
kubectl delete pod whoami -n demo-ns
kubectl delete pod curly -n demo-ns
```

**Other Useful**

```bash
kubectl get deploy -n demo-ns
kubectl get rs -n demo-ns
kubectl get po -n demo-ns
kubectl get services -n demo-ns
kubectl get ingresses -n demo-ns
kubectl describe service whoami-svc -n demo-ns
kubectl rollout restart deployment.apps/spring-boot-app -n demo-ns
(
NAMESPACE=your-rogue-namespace
kubectl proxy &
kubectl get namespace $NAMESPACE -o json |jq '.spec = {"finalizers":[]}' >temp.json
curl -k -H "Content-Type: application/json" -X PUT --data-binary @temp.json 127.0.0.1:8001/api/v1/namespaces/$NAMESPACE/finalize
)
```

🔗 [Kubernetes Command Reference](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)

---

## 🐘 PostgreSQL — Monitoring

**Check Long-Running Queries**

```sql
SELECT now() - query_start, * 
FROM pg_catalog.pg_stat_activity 
WHERE state = 'active' 
  AND now() - query_start > interval '5 minute';
```

---

## ⎔ Neovim — Setup Guide

**Install on Windows**

```powershell
winget install Neovim.Neovim
```

**LazyVim Setup**

```powershell
Move-Item $env:LOCALAPPDATA\nvim $env:LOCALAPPDATA\nvim.bak
git clone https://github.com/LazyVim/starter $env:LOCALAPPDATA\nvim
Remove-Item $env:LOCALAPPDATA\nvim\.git -Recurse -Force
nvim
```

🔗 [LazyVim Extras](https://www.lazyvim.org/extras) ｜ [Java Setup Guide](https://github.com/nvim-java/nvim-java/wiki/Lazyvim)

---

## 📦 Vcpkg — C++ Package Manager

**Bootstrap**

```powershell
git clone https://github.com/microsoft/vcpkg
cd vcpkg; .\bootstrap-vcpkg.bat
```

**Env Setup**

```powershell
$env:VCPKG_ROOT = "D:\git\vcpkg"
$env:PATH = "$env:VCPKG_ROOT;$env:PATH"
```

**Install Package**

```powershell
vcpkg install libxml2:x64-windows
vcpkg integrate install
```

🔗 [Official Guide](https://learn.microsoft.com/en-us/vcpkg/get_started/get-started?pivots=shell-powershell)

---

## 🌱 Spring Boot — Project Init Examples

**Setup**

```bash
curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
sdk install java 17.0.10-zulu
sdk install springboot
```

**Init Templates**

```bash
spring init my-app --build=maven --java-version=17 --group-id org.jyasu \
--boot-version=3.2.2 --packaging jar --extract --force \
--dependencies=web,lombok,docker-compose # web,lombok,docker-compose,spring-ai-openai,spring-ai-vectordb-elasticsearch,spring-ai-vectordb-cassandra,spring-ai-vectordb-redis,spring-ai-vectordb-mongodb-atlas,spring-ai-vectordb-pgvector,web,lombok,docker-compose,spring-ai-ollama,postgresql,data-jpa,actuator,distributed-tracing,web,lombok,docker-compose,data-cassandra,data-cassandra-reactive,web,lombok,docker-compose,data-mongodb,data-mongodb-reactive,graphql,web,lombok,docker-compose,data-elasticsearch,web,lombok,docker-compose,data-redis,data-redis-reactive,web,lombok,docker-compose,kafka,kafka-streams,web,lombok,docker-compose,amqp-streams,amqp,web,lombok,docker-compose,cloud-starter-vault-config,lombok,docker-compose,native,spring-shell
```

🧪 Examples for:

* [x] PostgreSQL
* [x] MongoDB
* [x] Cassandra
* [x] Redis
* [x] ElasticSearch
* [x] Kafka
* [x] AMQP
* [x] Vault
* [x] Spring Shell
* [x] Actuator
* [x] Spring AI + Ollama

*Use `--artifact-id` and `--description` as needed.*

---

## 🌐 curl — Handy Examples

```bash
curl --resolve example.com:443:127.0.0.1 https://example.com
curl --output example.html "https://example.com/"
curl --header "PRIVATE-TOKEN: ?" https://example.com/
curl --basic --user 'test:test' https://example.com/
curl -fsSL https://example.com/install.sh | sh
curl --ftp-ssl --user "test:test" -l sftp://example.com:22/ --key ./id_rsa --pubkey ./id_rsa.pub
curl --request POST --data "A=B&C=D" https://example.com
curl --request POST --form "A=B" --form "C=D" https://example.com
curl --upload-file test.txt https://example.com
curl --ftp-ssl --user test:test -l ftp://example.com:21
```

---

## 💽 dd — Disk Benchmarking

```bash
dd if=/dev/zero of=./testfile bs=1G count=1 oflag=direct
# Example Output:
# 1+0 records in
# 1+0 records out
# 1073741824 bytes (1.1 GB, 1.0 GiB) copied, 4.76527 s, 225 MB/s
```

---

## 🌍 Terraform — Provisioning

**Install Script**

```bash
echo "⚙️ Installing Terraform..."
if command -v terraform &> /dev/null
then
    echo "✅ Terraform is already installed."
    terraform version
else
    wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
    sudo apt update && sudo apt install -y terraform
    if [ $? -eq 0 ]; then
        echo "✅ Terraform installed successfully."
        terraform version
    else
        echo "❌ Error installing Terraform. Please check the output above."
        exit 1
    fi
fi

```

**Workflow**

```bash
terraform init
terraform plan
terraform apply -auto-approve -var key=value
terraform destroy
terraform fmt
terraform validate
terraform version

terraform state rm $RESOURCE
```

---

## 🧬 OpenTofu — Terraform Alternative

**Installer**

```bash
echo "⚙️ Installing OpenTofu..."
if command -v tofu &> /dev/null
then
    echo "✅ OpenTofu is already installed."
    tofu version
else
    # Download the installer script
    curl --proto '=https' --tlsv1.2 -fsSL https://get.opentofu.org/install-opentofu.sh -o install-opentofu.sh
    # Alternatively: wget --secure-protocol=TLSv1_2 --https-only https://get.opentofu.org/install-opentofu.sh -O install-opentofu.sh

    # Give it execution permissions
    chmod +x install-opentofu.sh

    echo "ℹ️ Please inspect the downloaded OpenTofu installer script (install-opentofu.sh) if you wish."
    read -p "Proceed with OpenTofu installation? (y/N): " confirm_tofu
    if [[ "$confirm_tofu" =~ ^[Yy]$ ]]; then
        # Run the installer
        sudo ./install-opentofu.sh --install-method deb
        # Remove the installer
        rm -f install-opentofu.sh
        if [ $? -eq 0 ]; then
            echo "✅ OpenTofu installed successfully."
            tofu version
        else
            echo "❌ Error installing OpenTofu. Please check the output above."
            # exit 1 # Decided not to exit if Tofu fails, as Terraform might be primary
        fi
    else
        echo "⏩ Skipping OpenTofu installation."
        rm -f install-opentofu.sh
    fi
fi
```

**Workflow**

```bash
tofu init
tofu plan
tofu apply -auto-approve -var key=value
tofu destroy
tofu fmt
tofu validate
tofu version
```

---

## 📈 k6 — Load Testing

**Install**

```bash

sudo gpg -k
sudo gpg --no-default-keyring --keyring /usr/share/keyrings/k6-archive-keyring.gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C5AD17C747E3415A3642D57D77C6C491D6AC1D69
echo "deb [signed-by=/usr/share/keyrings/k6-archive-keyring.gpg] https://dl.k6.io/deb stable main" | sudo tee /etc/apt/sources.list.d/k6.list
sudo apt-get update
sudo apt-get install k6
```

**Run Test**

```bash
k6 run --out influxdb=http://localhost:8086 k6.js
```

---



## Rabbitmq

```bash
rabbitmqadmin -u admin -p Pcc.123456 export broker
 
rabbitmqadmin -u admin -p Pcc.123456 import broker

rabbitmqadmin -u admin -p admin publish \
    exchange=ex \
    routing_key=key \
    payload='{"a": "","b":{"c": ""}}' \
    properties='{"headers": {"id": "6D933FBEEA6D42E2AD0E1FA479A5DABA"}}'
```



## ufw

```bash

sudo ufw allow from 192.168.1.0/24 to any port 22
sudo ufw status numbered
sudo ufw insert 1  deny from 192.168.1.0/24 to any port 80,443 proto tcp
sudo ufw delete 1
```


## acli

```bash
curl -LO "https://acli.atlassian.com/linux/latest/acli_linux_amd64/acli"
chmod +x ./acli
sudo install -o root -g root -m 0755 acli /usr/local/bin/acli
acli rovodev auth login
```
