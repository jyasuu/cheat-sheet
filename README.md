# cheat-sheet


## starship

https://github.com/starship/starship?tab=readme-ov-file

```sh
curl -sS https://starship.rs/install.sh | sh
eval "$(starship init bash)"
```


## podman

```ps1
winget install -e --id RedHat.Podman
winget install -e --id RedHat.Podman-Desktop 
podman machine init 
podman machine set --rootful
podman machine start

# uninstall
podman machine stop
podman machine rm -f
winget uninstall -e --id RedHat.Podman
winget uninstall -e --id RedHat.Podman-Desktop

# others
podman run --rm -it -v E:\data:/mnt/data alpine sh
```
