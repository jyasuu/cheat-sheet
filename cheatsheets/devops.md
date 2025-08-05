# DevOps Cheat Sheet


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