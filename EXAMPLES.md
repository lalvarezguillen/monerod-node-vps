## Example configurations (`hosts.yml`)

### Single Hetzner node, using default settings

```yaml
all:
  children:
    hetzner_nodes:
      hosts:
        hetznernode1:
```

### Two Hetzner Nodes. One using default settings (`hetznernode1`), other using custom settings (`hetznernode2`)

```yaml
all:
  children:
    hetzner_nodes:
      hosts:
        hetznernode1:
        hetznernode2:
          network_flag: --stagenet
          pruned: false
          hetzner_volume_gb_size: 30
```

### Two nodes using default settings. One on Hetzner and one on DigitalOcean

```yaml
all:
  children:
    hetzner_nodes:
      hosts:
        hetznernode1:
    digitalocean_nodes:
      hosts:
        donode1:
```