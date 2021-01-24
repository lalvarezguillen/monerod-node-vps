## Example configurations (`hosts.yml`)

A combination of the configurations described below should be possible. That'd allow you
to run/manage a fleet of nodes on different clouds and locations. For more info, refer
to ansible's inventory documentation: https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html

### Single Hetzner node, using default settings

```yaml
all:
  children:
    hetzner_nodes:
      hosts:
        hetznernode1:
    digitalocean_nodes:
      hosts:
    vultr_nodes:
      hosts:
    preexisting_nodes:
      hosts:
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
    digitalocean_nodes:
      hosts:
    vultr_nodes:
      hosts:
    preexisting_nodes:
      hosts:
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
    vultr_nodes:
      hosts:
    preexisting_nodes:
      hosts:
```

### The old machine running in my grandma's basement

Assuming that: 

* its ip is `1.2.3.4`
* it has ssh access
* it has a user named `luis` in its `sudoers` group with password `nicepassword`
* it doesn't have an external drive attached, so we store the blockchain on the main drive

```yaml
all:
  children:
    hetzner_nodes:
      hosts:
    digitalocean_nodes:
      hosts:
        donode1:
    vultr_nodes:
      hosts:
    preexisting_nodes:
      hosts:
        grandmasbasement1:
          ansible_host: 1.2.3.4
          ansible_user: luis
          ansible_become: true
          ansible_become_password: nicepassword
          device_name: ''
          volume_mountpoint: ''
```

But if it has an external drive that we can use to store the blockchain, attached on `/dev/sda2`;
we can do the following:

```yaml
all:
  children:
    hetzner_nodes:
      hosts:
    digitalocean_nodes:
      hosts:
        donode1:
    vultr_nodes:
      hosts:
    preexisting_nodes:
      hosts:
        grandmasbasement1:
          ansible_host: 1.2.3.4
          ansible_user: luis
          ansible_become: true
          ansible_become_password: nicepassword
          device_name: /dev/sda2
          volume_mountpoint: '/mnt/monero'
```