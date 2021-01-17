### Creates a public Monero node on a VPS

The purpose of this project is to simplify the creation and maintenance
of **public** Monero nodes on virtual private servers. Where maintenance
involves the following tasks:

* Upgrading Monero version.
* Enabling, disabling and updating flags passed to `monerod`.
* Extending disk space allocated to store the blockchain.

The project aims to:

* Be as simple as possible.
* Produce a node that seeds the blockchain (public node).
* Produce a secure and stable setup.
* Produce an inexpensive setup by default.
* Make maintenance as easy as posible.
* Allow creating mainnet, stagenet or testnet nodes.

The project doesn't intend to produce a setup optimized for mining.

### Requirements

* Python
* Ansible
* Ansible's `hcloud` collection: https://galaxy.ansible.com/hetzner/hcloud
* Ansible's `community.general` collection: https://galaxy.ansible.com/community/general

You also need the appropriate API Keys for the VPS provider (Hetzner and DigitalOcean only,
for the time being), and public SSH key to setup in the server.

### Long term goals

* Extend to be able to setup nodes in other VPS providers, besides Hetzner and DigitalOcean
* Extend to be able to create/maintain several nodes, across different
clouds, with different configurations.
* Tor setup.
* Maybe include automated CI setup.

### Usage

1. Set up the following environment variables

    * `HETZNER_API_KEY`
    * `DO_API_KEY`
    * `PUBLIC_SSH_KEY_NAME`
    * `PUBLIC_SSH_KEY`

2. Run

    * `ansible-playbook -i hosts.yml setup-hetzner-nodes.yml` to set up your nodes on Hetzner
    * `ansible-playbook -i hosts.yml setup-do-nodes.yml` to set up your DigitalOcean nodes

That should create:

* A public Monero mainnet node
* Running on the server type that has 1vcu and 2gb RAM
* With a 50GB volume attached
* Keeping a pruned blockchain
* It'd expose port 22 for ssh, port 18080 for p2p, and port 18089 for restricted RPC

The estimated cost of this setup is:

* less than 6€/month for the Hetzner node
* about 12€/month for the DigitalOcean node


Further configuration can be achieved via other environment variables (they're
listed below), and through `hosts.yml` file (see `EXAMPLES.md` for reference)

### Environment variables reference

* `HETZNER_API_KEY`: Your API key for Hetzner. It's required to run the Hetzner playbook.
It should be an API key with read and write permissions.

* `DO_API_KEY`: Your API key for DigitalOcean. It's required to run the DigitalOcean playbook.
It should be an API key with read and write permissions.

* `PUBLIC_SSH_KEY_NAME`: A name for your public SSH key in Hetzner

* `PUBLIC_SSH_KEY`: Your public SSH key. It'd be created in Hetzner and
embedded in your node; to be able to set up the node, and access it
in general.

* `NETWORK_FLAG`:
    
    * Empty by default, which indicates mainnet
    * `--stagenet`
    * `--testnet`

* `HETZNER_REGION`: The Hetzner datacenter where the node should be created.
Defaults to `fsn1`. See [this](https://docs.hetzner.com/general/others/data-centers-and-connection/) for context

* `HETZNER_SERVER_TYPE`: The type of Hetzner server to create for the node.
Defaults to `cx11`, which is the smallest available. See Prices Section
[here](https://www.hetzner.com/cloud)

* `HETZNER_IMAGE`: The Hetzner machine image to use on the server. Defaults
to `ubuntu-20.04`.

* `HETZNER_VOLUME_GB_SIZE`: The size of he volume that will hold the blochain,
expressed in gb. Defaults to `50`

* `DO_REGION`: The DigitalOcean datacenter where the node should be created.
Defaults to `nyc1`. See [this](https://www.digitalocean.com/docs/platform/availability-matrix/) for context

* `DO_SERVER_TYPE`: The type of DigitalOcean droplet to create for the node.
Defaults to `s-1vcpu-2gb`, which is the smallest available with 2gb RAM.
See prices section [here](https://www.digitalocean.com/pricing/)

* `DO_IMAGE`: The DigitalOcean machine image to use on the server. Defaults
to `ubuntu-20-04-x64`

* `DO_VOLUME_GB_SIZE`: The size of the volume that will hold the blockchain,
expressed in gb. Defaults to `50`

* `MONERO_DESIRED_VERSION`: The version of Monero that the node should be running.
Defaults to `v0.17.1.9` at the moment.

* `MONERO_DOWNLOAD_URL`: The URL where Monero should be downloaded from. Defaults
to `https://downloads.getmonero.org/cli/linux64`

* `MONERO_DOWNLOAD_CHECKSUM`: The checksum of the Monero download, to verify
its integrity. Defaults to the checksum of Monero v0.17.1.9 at the moment:
`sha256:0fb6f53b7b9b3b205151c652b6c9ca7e735f80bfe78427d1061f042723ee6381`

* `PRUNED_NODE`: Whether the node should run a pruned blockchain, to
save disk space. Defaults to `True`

* `ENABLE_DNS_BLOCKLIST`: Whether the node should run with `--enabled-dns-blocklist`.
Defaults to `True`. See [this](https://github.com/monero-project/monero/pull/7139)
for context

* `LIMIT_RATE_UP` and `LIMIT_RATE_DOWN`: Limit the upload speed available to the Monero node. Defaults to empty, which means that the default limits configured
by Monero apply.

* `OUT_PEERS` and `IN_PEERS`: Limit the number of p2p connections. Defaults
to empty, which applies the default limits configured by Monero.

* `MEMORY_MAX`: Prevents the node from using more than this amount of RAM.
Otherwise it gets restarted. Defaults to `1536M`. See `systemd`'s docs for
[context](https://manpages.debian.org/testing/systemd/systemd.resource-control.5.en.html)

* `MONERO_BAN_LIST_URL`: If provided, downloads a list of IPs of Monero nodes
to ban. Defaults to empty.

* `ANSIBLE_HOST_KEY_CHECKING`: Set to `False` to avoid having to manually confirm
the authenticity of host ssh keys. See [this StackOverflow](https://stackoverflow.com/questions/32297456/how-to-ignore-ansible-ssh-authenticity-checking)
thread for context

### Upgrading Monero Version

The envvar `MONERO_DESIRED_VERSION` governs the version of Monero we want
installed on the node. If the version doesn't match, this project downloads
Monero again from `MONERO_DOWNLOAD_URL`. So, the process of upgrading your
node to the latest version would look like:

1. Set `MONERO_DESIRED_VERSION` to the latest version
2. Set `MONERO_DOWNLOAD_CHECKSUM` to the checksum of the version you're
downloading. This is provided on [Monero's website](https://www.getmonero.org/downloads/)
3. Run this project

### Extending the volume allocated to store the blockchain

Simply increasing `HETZNER_VOLUME_GB_SIZE` should take care of extending
the volume and the filesystem, for nodes hosted on Hetzner.

Right now this is not possible on DigitalOcean nodes. Because Ansible modules
for that provider don't allow extending the size of existing volumes. I'll
work on patching those modules, or crafting an alternative.

### Allocating more network resources

[This Reddit thread](https://www.reddit.com/r/Monero/comments/ko51jj/running_a_public_node_data_transfer_rate_limits/) suggests that increasing
`OUT_PEERS`, `IN_PEERS`, `LIMIT_RATE_UP`, `LIMIT_RATE_DOWN` would increase
the node's contribution to the network, by connecting to more peers, accepting
concurrent connections from more peers, and allocating more up/down bandwith
to the node.

The setup suggested in that thread can be achived with:

* `OUT_PEERS=512`
* `IN_PEERS=512`
* `LIMIT_RATE_UP=92160`
* `LIMIT_RATE_DOWN=92160`

The extra traffic might generate additional costs though. Check Pricing section of:

* https://www.hetzner.com/cloud
* https://www.digitalocean.com/docs/billing/bandwidth/
