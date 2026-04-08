# Containers Cheat Sheet

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

## 🐋 Docker — Quick Reference

🔗 [cheat.sh/docker](https://cheat.sh/docker)

```sh
docker run --restart always -v /tmp:/tmp -v /conf:/conf -e PASS=pass -e ACCT=acct -p 80:80 -p 443:443 -d --name nginx nginx
docker exec -it nginx sh
docker system prune -af
docker save postgres:15.3 > postgres.tar
docker load < postgres.tar
docker buildx build -t test .
docker compose up -d --build
docker compose ps
docker compose logs
docker compose exec -it nginx sh
docker compose restart
docker compose stop
docker compose start

```

```sh
#!/usr/bin/env bash
set -euo pipefail

BACKUP_ROOT="$PWD/docker-ipam-migration"
BACKUP_DIR="$BACKUP_ROOT/backup"
DAEMON_JSON="/etc/docker/daemon.json"

mkdir -p "$BACKUP_DIR"/{compose,networks,containers}
date > "$BACKUP_DIR/date.txt"

echo "== Docker IPAM Migration using docker compose ls =="

#############################
# 1. Backup daemon.json
#############################
if [[ -f "$DAEMON_JSON" ]]; then
  cp "$DAEMON_JSON" "$BACKUP_DIR/daemon.json.bak"
fi

#############################
# 2. Backup compose projects
#############################
docker compose ls --format json > "$BACKUP_DIR/compose/compose-ls.json"

jq -r '.[] | "\(.Name)|\(.ConfigFiles)"' \
  "$BACKUP_DIR/compose/compose-ls.json" |
while IFS='|' read -r name files; do
  mkdir -p "$BACKUP_DIR/compose/$name"
  IFS=',' read -ra CFGS <<< "$files"
  for f in "${CFGS[@]}"; do
    cp "$f" "$BACKUP_DIR/compose/$name/"
  done
done

echo "[OK] Compose files backed up"

#############################
# 3. Backup networks
#############################
for net in $(docker network ls --format '{{.Name}}'); do
  docker network inspect "$net" \
    > "$BACKUP_DIR/networks/$net.json" || true
done

#############################
# 4. Backup containers metadata
#############################
for c in $(docker ps -a --format '{{.Names}}'); do
  docker inspect "$c" \
    > "$BACKUP_DIR/containers/$c.json"
done

echo "[OK] Metadata backup completed"

#############################
# 5. Stop all compose stacks
#############################
jq -r '.[].Name' "$BACKUP_DIR/compose/compose-ls.json" |
while read -r project; do
  docker compose -p "$project" down
done

#############################
# 6. Remove any remaining containers
#############################
docker rm -f $(docker ps -aq) 2>/dev/null || true

#############################
# 7. Remove user-defined networks
#############################
docker network prune -f

#############################
# 8. Update Docker IPAM config
#############################
cat > "$DAEMON_JSON" <<'EOF'
{
  "data-root": "/data/docker",
  "default-address-pools": [
    {
      "base": "192.168.255.1/24",
      "size": 24
    },
    {
      "base": "192.168.254.0/24",
      "size": 28
    }
  ],
  "log-driver": "json-file",
  "log-opts": {
      "max-file": "3",
      "max-size": "10m"
  }
}
EOF

#############################
# 9. Restart Docker
#############################
systemctl restart docker
sleep 3


#############################
# 10. Recreate networks
#############################
for net_json in "$BACKUP_DIR/networks/"*.json; do
  name=$(jq -r '.[0].Name' "$net_json")
  driver=$(jq -r '.[0].Driver' "$net_json")

  [[ "$name" =~ ^(bridge|host|none)$ ]] && continue

  docker network create --driver "$driver" "$name"
done


#############################
# 11. Restore compose stacks
#############################
jq -r '.[] | "\(.Name)|\(.ConfigFiles)"' \
  "$BACKUP_DIR/compose/compose-ls.json" |
while IFS='|' read -r name files; do
  IFS=',' read -ra CFGS <<< "$files"

  CMD=(docker compose -p "$name")
  for f in "${CFGS[@]}"; do
    CMD+=(-f "$f")
  done

  "${CMD[@]}" up -d
done

echo "== Docker IPAM Migration COMPLETE =="
```

---

## ☸️ Kubernetes — Common Commands

**Basic Operations**

```bash
kubectl create ns demo-ns
kubectl run whoami --image=traefik/whoami -n demo-ns
kubectl expose pod whoami --type=NodePort --port=80 --name=whoami-svc -n demo-ns
kubectl get pods -n demo-ns -o wide
kubectl get svc -n demo-ns -o wide
kubectl run -it --rm --env="PGPASSWORD=" --env="PGUSER=" --env="PGDATABASE=" --env="PGHOST=" --image=postgres postgres -- psql
kubectl attach postgres -c postgres -i -t
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
kubectl get resourcequota -n demo-ns
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

kubectl get crd gateways.gateway.networking.k8s.io &> /dev/null || \
  kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.3.0/standard-install.yaml

kubectl create secret docker-registry gitlab-registry --docker-server=https://docker.io --docker-username=jyasu  --docker-password=jyasu --docker-email=jyasu@example.com --dry-run=client -o yaml
```

```yml

# --- Deployment ---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: <app-name>                      # e.g., uptime
spec:
  replicas: 1                           # how many Pods
  revisionHistoryLimit: 3               # how many old ReplicaSets to keep
  strategy:
    type: Recreate                      # good for stateful/unique ports
  selector:
    matchLabels:
      app: <app-name>                   # must match template.labels
  template:
    metadata:
      labels:
        app: <app-name>
    spec:
      containers:
      - name: <container-name>          # usually same as app-name
        image: <registry>/<repo>:<tag>  # e.g. louislam/uptime-kuma:1
        ports:
        - containerPort: <port>         # e.g. 3001
        imagePullPolicy: IfNotPresent   # IfNotPresent | Always | Never
        volumeMounts:
        - name: <volume-name>           # must match volumes.name
          mountPath: /data              # path inside container
        env:
        - name: TZ
          value: Asia/Taipei
        # inline env vars from ConfigMap/Secret
        envFrom:
        - configMapRef:
            name: <configmap-name>
        resources:
          requests:
            cpu: "100m"
            memory: "128Mi"
          limits:
            cpu: "200m"
            memory: "256Mi"
      volumes:
      - name: <volume-name>
        nfs:                            # or pvc/emptyDir/hostPath, etc.
          server: <nfs-server>
          path: <nfs-path>

---

# --- Service ---
apiVersion: v1
kind: Service
metadata:
  name: <svc-name>                      # e.g., uptime-service
spec:
  selector:
    app: <app-name>                     # must match Pod labels
  ports:
  - port: <svc-port>                    # cluster port
    targetPort: <container-port>        # containerPort above
  type: ClusterIP                       # ClusterIP | NodePort | LoadBalancer

---

# --- HTTPRoute (Gateway API) ---
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: <route-name>
spec:
  parentRefs:
  - name: <gateway-name>                # e.g., http-gateway
    sectionName: https                  # listener name on the Gateway
    namespace: <gateway-namespace>      # namespace of the Gateway
  - name: <gateway-name>
    sectionName: http
    namespace: <gateway-namespace>
  hostnames:
  - "<host.example.com>"                # FQDN exposed by Gateway
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /                        # route prefix
    backendRefs:
    - name: <svc-name>                  # must equal Service.metadata.name
      port: <svc-port>                  # must equal Service.spec.ports[*].port

```

🔗 [Kubernetes Command Reference](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)


## Helm

```ps1

helm template helm-app .  --values values.yaml -n helm-demo

helm template helm-demo
helm package helm-demo
helm install helm-demo --generate-name -n helm-demo --debug
helm list -n helm-demo
helm upgrade helm-demo-1757482975 helm-demo -n helm-demo --debug
helm uninstall helm-demo-1757482975 helm-demo -n helm-demo --debug 
```


## Argocd

```ps1

$version = (Invoke-RestMethod https://api.github.com/repos/argoproj/argo-cd/releases/latest).tag_name
$url = "https://github.com/argoproj/argo-cd/releases/download/" + $version + "/argocd-windows-amd64.exe"
$output = "argocd.exe"
Invoke-WebRequest -Uri $url -OutFile $output
argocd login argocd.example.com --sso


argocd repo add https://github.com/jyasuu/helm-demo.git `
 --username ? `
 --password ?

argocd app create helm-app `
  --repo https://github.com/jyasuu/helm-demo.git `
  --path . `
  --revision main `
  --dest-server https://kubernetes.default.svc `
  --dest-namespace helm-demo `
  --sync-policy automated `
  --values values-lab.yaml `
  --project helm-app 

argocd app list
argocd app sync helm-app
argocd app delete helm-app
argocd app history helm-app
argocd app manifests helm-app
```


