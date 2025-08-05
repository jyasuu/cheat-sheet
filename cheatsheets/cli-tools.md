# CLI Tools Cheat Sheet

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
gcloud container clusters get-credentials cluster --region ? --project ?
```