# calypso-deploy — multi-server deployment

This repository contains a small Ansible playbook and Docker Compose file(s) used to deploy services to one or more self-hosted servers.

Usage

- Deploy to all servers defined in `hosts.yaml` (group `servers`):

```bash
ansible-playbook -i hosts.yaml playbook.yaml
```

- Deploy to a single host by name (example: `calypso`):

```bash
ansible-playbook -i hosts.yaml playbook.yaml -e target=calypso
```

Notes

- `hosts.yaml` now contains a `servers` group. Add hosts under that group and set `ansible_host`, `ansible_user`, and optionally `deploy_path` for each host.
- By convention the playbook will use a per-host compose file named `docker-compose.<hostname>.yaml` by default (for example `docker-compose.calypso.yaml`). If a host defines a `compose_src` variable in `hosts.yaml`, that filename will be used instead.
- The playbook copies the resolved compose source to `docker-compose.yaml` at the target `deploy_path` (defaults to `/home/<ansible_user>`) and runs `community.docker.docker_compose_v2` there.
- To run against a subset of hosts you can also use `--limit` or set `target` to a group or host pattern.

Example hosts entry:

```yaml
all:
  children:
    servers:
      hosts:
        calypso:
          ansible_host: 192.168.10.10
          ansible_user: clay
          deploy_path: /home/clay
```

Next steps (suggested)

- Add per-host overrides for volumes or compose files if different servers need different stacks.
- Update your self-hosted GitHub Actions runner workflow to pass `target` or use an inventory limit when triggering the playbook, so you can control which server(s) the CI deploys to.
