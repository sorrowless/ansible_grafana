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

#### How to Add / Edit Dashboards

##### Option 1: Via Git Repository (Recommended)
1. Add or update your `.json` dashboard file in `dashboards/` directory of repository `https://github.com/oom-ag/grafana-dashboards.git`.
2. Ensure the dashboard contains valid `title` and `uid`.
3. Commit and push to `main` branch. Grafana GitSync will automatically pull new changes.

##### Option 2: Via Grafana UI
1. Open Grafana UI and navigate to the dashboard.
2. Make your edits and click **Save**.
3. Select **Create Pull Request / Merge Request** option in Grafana UI to sync changes back to Git.

#### Local Testing (Docker / VM)

1. **Run Playbook**:
   ```bash
   ansible-playbook -i tests/inventory tests/test.yml
   ```

2. **Verify Grafana Health**:
   ```bash
   curl -s http://localhost:3000/api/health
   ```

3. **Verify Synchronized Dashboards via API**:
   ```bash
   curl -s -u admin:password http://localhost:3000/api/search | jq .
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
