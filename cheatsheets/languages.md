# Languages Cheat Sheet

## 🦀 Rust — Cargo Package Manager

**Common Commands**

```bash

rustup update stable
rustup default $RUST_VERSION  # e.g., stable-x86_64-unknown-linux-gnu
rustup target add x86_64-unknown-linux-gnu
cargo install cross --git https://github.com/cross-rs/cross (windows depend docker)
cross build --target x86_64-unknown-linux-gnu --release

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

🔗 [LazyVim Extras](https://www.lazyvim.org/extras) | [Java Setup Guide](https://github.com/nvim-java/nvim-java/wiki/Lazyvim)

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
    curl --proto '=https' --tlsv1.2 -fsSL https://get.opentofu.org/install-opentofu.sh -o install-opentofu.sh
    chmod +x install-opentofu.sh

    echo "ℹ️ Please inspect the downloaded OpenTofu installer script (install-opentofu.sh) if you wish."
    read -p "Proceed with OpenTofu installation? (y/N): " confirm_tofu
    if [[ "$confirm_tofu" =~ ^[Yy]$ ]]; then
        sudo ./install-opentofu.sh --install-method deb
        rm -f install-opentofu.sh
        if [ $? -eq 0 ]; then
            echo "✅ OpenTofu installed successfully."
            tofu version
        else
            echo "❌ Error installing OpenTofu. Please check the output above."
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
