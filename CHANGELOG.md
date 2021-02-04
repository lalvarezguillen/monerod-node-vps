## v0.2.4

- Adapted to be able to resize DigitalOcean volumes
- Consolidated Ansible requirements in `requirements.yml`
- Adapted to be able to run `ansible-playbook` with `--limit` flag
- Upgraded `ngine_io.vultr` to version `1.1.0`

## v0.2.3

- Allow running and maintaining nodes on Vultr

## v0.2.2

- Allow running and maintaining nodes on preexisting infrastructure. This
would bypass all the server-creation logic, and go straight to configuring
the preexisting server.

## v0.2.1

- Allow running and maintaining nodes on DigitalOcean as well. The only caveat is
that volumes can't be resized on DigitalOcean at this point in time. (`v0.2.4` fixed this)

## v0.2.0

- Switched to using yaml for hosts file
- Allow running several Monero public nodes on Hetzner, with different configurations

## v0.1.0

- Initial setup
- Allows running and maintaining a single Monero public node on Hetzner
