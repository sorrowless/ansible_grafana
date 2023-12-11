sbog/grafana
============

Simple role to install grafana

#### Requirements

Ansible 2.4

#### Role Variables

```yaml
grafana:
  # Host dir to store grafana data
  grafana_storage: /var/grafana/storage
  # Docker labels
  docker_labels: []

# Docker-related networking settings
docker:
  network_name: internal.loc
  network_subnet: 172.22.101.0/24
  network_gateway: 172.22.101.1
  network_iprange: 172.22.101.128/25
```

If you use docker swarm, you should specify variables for the host on which
the container is supposed to be run, and also indicate the name of the swarm
manager through which the stack will be deployed in variable
`grafana_swarm_manager`
```yaml
grafana_swarm_manager: swarm-manager01
```

#### Dependencies

None

#### Example Playbook

```yaml
- name: Install and configure Grafana
  hosts: grafana
  remote_user: root
  roles:
    - { role: grafana, tags: [ 'grafana' ] }
```

#### License

Apache 2.0

#### Author Information

Stanislaw Bogatkin (https://sbog.ru)
