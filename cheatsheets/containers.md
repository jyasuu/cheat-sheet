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


argocd repo add [https://pcggit.pouchen.com/sd2ap1/mda/mda-helm.git](https://github.com/jyasuu/helm-demo.git) `
 --username ? `
 --password ?

argocd app create helm-app `
  --repo https://github.com/jyasuu/helm-demo.git `
  --path . `
  --revision main `
  --dest-server https://kubernetes.default.svc `
  --dest-namespace helm-demo `
  --sync-policy automated `
  --helm-set-file values=values.yaml
```


