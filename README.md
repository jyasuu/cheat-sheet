# cheat-sheet


## starship

https://github.com/starship/starship?tab=readme-ov-file

```sh
curl -sS https://starship.rs/install.sh | sh
eval "$(starship init bash)"
```


## podman

```powershell
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


## mongodb

```powershell
.\mongosh.exe "mongodb+srv://?/" --apiVersion 1 --username ?
```

```mongo
db.inventory.insertMany([
   { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "A" },
   { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
   { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
   { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" }
]);
 db.inventory.find();
```

## docker

[cheat.sh](https://cheat.sh/docker)


## k8s

[cheat.sh](https://cheat.sh/kubectl)
[reference](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)


## postgres

```sql

select now() - query_start ,*--,pg_cancel_backend(pid),pg_terminate_backend(pid)
 from pg_catalog.pg_stat_activity psa 
 where state = 'active'
 and now() - query_start  > interval '5 minute';
```
