# Zabbix-stack

A collection of Zabbix monitoring solutions to automate the boring stuff. Customized templates, bulk host management, and maintenance windows.

---

## Contents

## ☰ Templates

Monitoring templates for systems where official coverage is weak or nonexistent.
Each template ships with discovery rules, items/triggers, and macros, ready to import.

| Template | Monitor | How |
|----------|----------------|-----|
| [proxmox-backup-server](templates/proxmox-backup-server) | Datastores, backup/verify/sync/GC tasks, service health | HTTP agent >> PBS API |
| [wazuh](templates/wazuh) *(coming soon)* | Manager status, agent counts, indexer cluster health, alert stats | HTTP agent >> Wazuh API |

Import manual via *Data collection → Templates → Import*, or let [CI](#template-deployment--automationtemplate-deploy) do it for you.

### 🛠 Automation

Tired of adding hosts one by one through the UI? Need to onboard 50 hosts and don't want to spend your afternoon clicking? Define everything in YAML, push to git, and let the pipeline handle it. Powered by Ansible and GitLab CI/CD.

```
YAML (git) → push → GitLab CI → Zabbix API
                        │
                        ├── plan-mode: dry-run (auto)
                        └── deploy-mode: apply (manual for production)
```

### Hosts — `automation/hosts/`

Declarative host management. YAML in, Zabbix hosts out.

- One YAML file per project/environment
- Full support: host groups (even if not existing), templates, tags, macros, TLS/PSK, proxy, inventory
- **Reconciliation** — smart tag to hosts removed from YAML get deleted from Zabbix. No orphans. (without affecting the hosts added manually or not from automation tool)

### Maintenances — `automation/maintenances/`

Maintenance windows as code. No more "who created that window and why?"

- Flexible scheduling: once, daily, weekly, monthly
- Same plan/deploy pipeline
- **Reconciliation** — orphaned windows get cleaned up automatically (without affecting the maintenance added manually or not from automation tool)
- Manually created windows in Zabbix stay untouched

### Templates — `automation/templates/`

Push a template YAML, merge to main, CI imports it into Zabbix. (always update keep only needed to template and update them since that's not covered by zabbix)

---

## Requirements

| Component | Version |
|-----------|---------|
| Zabbix server | > 7.0 LTS |
| GitLab CI | Runner Docker executor |

See each component's README for specific dependencies.

## Layout

```
zabbix-stack/
├── templates/
│   ├── proxmox-backup-server/
│   │   ├── README.md
│   │   └── template.yaml
│   └── wazuh/
│       ├── README.md
│       └── template.yaml
│
└── automation/
    ├── hosts/
    │   ├── .gitlab-ci.yml
    │   ├── playbooks/
    │   │   ├── reconcile_hosts.yml
    │   │   └── load_hosts.yml
    │   └── hosts/
    │       └── <project>/
    │           ├── production.yml
    │           └── stage.yml
    │
    ├── maintenances/
    │   ├── .gitlab-ci.yml
    │   ├── playbooks/
    │   │   ├── reconcile_maintenance.yml
    │   │   └── load_maintenance.yml
    │   └── maintenance/
    │       └── <project>/
    │           └── production.yml
    │
    └── templates/
        └── .gitlab-ci.yml
```

## Quick start

```bash
git clone https://github.com/imitik/zabbix-stack
cd zabbix-stack
```

Each component has its own README:

- `templates/<name>/README.md`
- `automation/hosts/README.md`
- `automation/maintenance/README.md`

---

## License

MIT — see [`LICENSE`](LICENSE).