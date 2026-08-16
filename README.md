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


#### Managing Dashboards (Observability as Code)

Dashboards are managed declaratively using **Grafana GitSync**. Each dashboard in the repository resides in its own slug folder: `dashboards/<slug>/<dashboard>.json`.

##### Role Configuration Variables

Configure GitSync settings in your `defaults/main.yml` or `host_vars`:

```yaml
# Enable GitSync provisioning & cleanup of legacy static configs
grafana_gitsync_enabled: true
grafana_cleanup_old_provisioning: true

# Source repository settings (URL without .git suffix)
grafana_dashboards_repo_url: "https://github.com/oom-ag/grafana-dashboards"
grafana_dashboards_repo_version: "main"
grafana_gitsync_token: "{{ vault_grafana_dashboards_repo_token }}"

# List of dashboard slugs to deploy on this host/group
grafana_sync_dashboards:
  - "postgresql-patroni"
  - "node-exporter-summary"
```

##### Declarative Dashboard Lifecycle

* **Deploy / Add Dashboard**: Add the dashboard's directory slug (e.g. `"postgresql-patroni"`) to `grafana_sync_dashboards` in `host_vars` and run the playbook.
* **Remove / Unprovision Dashboard**: Remove the slug from `grafana_sync_dashboards` and re-run the playbook. The role will automatically purge the GitSync connection and remove the dashboard from Grafana via API.

##### Option 1: Adding a New Dashboard via Git (Recommended)

1. Create a new slug directory in the dashboard repository: `dashboards/<slug>/`.
2. Place your formatted (pretty-printed) `.json` dashboard file inside `dashboards/<slug>/<Name>.json`.
3. Commit and push changes to the repository's `main` branch.
4. Add `<slug>` to `grafana_sync_dashboards` in Ansible inventory and run the playbook.

##### Option 2: Editing Existing Dashboards via Grafana UI

1. Open Grafana UI, navigate to the dashboard, and make your edits.
2. Click **Save**.
3. Select **Create Pull Request / Merge Request** in the Grafana UI dialog to sync your changes back to GitHub.


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
