# CLI Tools Cheat Sheet

## 🌟 Starship — Cross-Shell Prompt

**Installation & Setup**

```bash
curl -sS https://starship.rs/install.sh | sh
eval "$(starship init bash)"

rustup update stable
rustup default $RUST_VERSION  # e.g., stable-x86_64-unknown-linux-gnu
rustup target add x86_64-unknown-linux-gnu
cargo install cross --git https://github.com/cross-rs/cross (windows depend docker)
cross build --target x86_64-unknown-linux-gnu --release
```

🔗 [Starship Official Docs](https://github.com/starship/starship?tab=readme-ov-file)

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

## ufw

```bash
sudo ufw allow from 192.168.1.0/24 to any port 22
sudo ufw status numbered
sudo ufw insert 1 deny from 192.168.1.0/24 to any port 80,443 proto tcp
sudo ufw delete 1
```

---

## acli

```bash
curl -LO "https://acli.atlassian.com/linux/latest/acli_linux_amd64/acli"
chmod +x ./acli
sudo install -o root -g root -m 0755 acli /usr/local/bin/acli
acli rovodev auth login
```

---

## gcloud

```bash
curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-linux-x86_64.tar.gz
tar -xf google-cloud-cli-linux-x86_64.tar.gz
./google-cloud-sdk/install.sh
./google-cloud-sdk/bin/gcloud init
gcloud auth application-default login
gcloud container clusters get-credentials $cluster --region ? --project ?
gcloud components install gke-gcloud-auth-plugin
gcloud services enable compute.googleapis.com --project=?
gcloud compute addresses list
gcloud compute ssl-certificates list
gcloud compute ssl-certificates describe supabase-ssl-cert 
```



## ngrok

```bash
wget https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.tgz
sudo tar -xvzf ./ngrok-v3-stable-linux-amd64.tgz -C /usr/local/bin
ngrok config add-authtoken '?'
ngrok http http://localhost:8080
```


## istio

```bash
curl -L https://istio.io/downloadIstio | sh -
cd istio-1.26.3
export PATH=$PWD/bin:$PATH
istioctl version
istioctl install --set profile=ambient --skip-confirmation

```

### example
```bash

kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.26/samples/bookinfo/platform/kube/bookinfo.yaml
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.26/samples/bookinfo/platform/kube/bookinfo-versions.yaml
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.26/samples/bookinfo/gateway-api/bookinfo-gateway.yaml
kubectl annotate gateway bookinfo-gateway networking.istio.io/service-type=ClusterIP --namespace=default
kubectl get gateway
kubectl port-forward svc/bookinfo-gateway-istio 8080:80
kubectl label namespace default istio.io/dataplane-mode=ambient
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.26/samples/addons/prometheus.yaml
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.26/samples/addons/kiali.yaml
istioctl dashboard kiali
for i in $(seq 1 100); do curl -sSI -o /dev/null http://localhost:8080/productpage; done

kubectl apply -f - <<EOF
apiVersion: security.istio.io/v1
kind: AuthorizationPolicy
metadata:
  name: productpage-ztunnel
  namespace: default
spec:
  selector:
    matchLabels:
      app: productpage
  action: ALLOW
  rules:
  - from:
    - source:
        principals:
        - cluster.local/ns/default/sa/bookinfo-gateway-istio
EOF
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.26/samples/curl/curl.yaml
kubectl exec deploy/curl -- curl -s "http://productpage:9080/productpage"
istioctl waypoint apply --enroll-namespace --wait
kubectl get gtw waypoint
kubectl apply -f - <<EOF
apiVersion: security.istio.io/v1
kind: AuthorizationPolicy
metadata:
  name: productpage-waypoint
  namespace: default
spec:
  targetRefs:
  - kind: Service
    group: ""
    name: productpage
  action: ALLOW
  rules:
  - from:
    - source:
        principals:
        - cluster.local/ns/default/sa/curl
    to:
    - operation:
        methods: ["GET"]
EOF
kubectl apply -f - <<EOF
apiVersion: security.istio.io/v1
kind: AuthorizationPolicy
metadata:
  name: productpage-ztunnel
  namespace: default
spec:
  selector:
    matchLabels:
      app: productpage
  action: ALLOW
  rules:
  - from:
    - source:
        principals:
        - cluster.local/ns/default/sa/bookinfo-gateway-istio
        - cluster.local/ns/default/sa/waypoint
EOF
# This fails with an RBAC error because you're not using a GET operation
kubectl exec deploy/curl -- curl -s "http://productpage:9080/productpage" -X DELETE
# This fails with an RBAC error because the identity of the reviews-v1 service is not allowed
kubectl exec deploy/reviews-v1 -- curl -s http://productpage:9080/productpage
# This works as you're explicitly allowing GET requests from the curl pod
kubectl exec deploy/curl -- curl -s http://productpage:9080/productpage | grep -o "<title>.*</title>"

kubectl apply -f - <<EOF
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: reviews
spec:
  parentRefs:
  - group: ""
    kind: Service
    name: reviews
    port: 9080
  rules:
  - backendRefs:
    - name: reviews-v1
      port: 9080
      weight: 90
    - name: reviews-v2
      port: 9080
      weight: 10
EOF
kubectl exec deploy/curl -- sh -c "for i in \$(seq 1 100); do curl -s http://productpage:9080/productpage | grep reviews-v.-; done"

```


## sed

```bash
sed -i 's/\r//g'
```

## find

```bash
find ./ -type f -size +100M
find ./ -type f -name '*.log' -exec rm {} +
```



