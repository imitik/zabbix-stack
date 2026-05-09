# Zabbix-stack

A collection of Zabbix monitoring solutions to automate the boring stuff. Customized templates, bulk host management, and maintenance windows.

---

## Contents

## ☰ Templates

Monitoring templates for systems where official coverage is weak or nonexistent.
Each template ships with discovery rules, triggers, dashboards, and macros — ready to import.

| Template | Monitor | How |
|----------|----------------|-----|
| [proxmox-backup-server](templates/proxmox-backup-server) | Datastores, backup/verify/sync/GC tasks, service health | HTTP agent >> PBS API |
| [wazuh](templates/wazuh) soon.. | Manager status, agent counts, indexer cluster health, alert stats | HTTP agent >> Wazuh API |

Import manual via *Configuration → Templates → Import*, or let [CI](#template-deployment--automationtemplate-deploy) do it for you.

### 🛠 Automation

No more clicking through the Zabbix UI. Define everything in YAML, push to git, and let the pipeline handle it.

```
YAML (git) → push → GitLab CI → Zabbix API
                        │
                        ├── plan: dry-run (auto)
                        └── deploy: apply (manual for prod)
```

### Hosts — `automation/hosts/`

Declarative host management. YAML in, Zabbix hosts out.

- One YAML file per project/environment
- Full support: host groups, templates, tags, macros, TLS/PSK, proxy, inventory
- **Reconciliation** — hosts removed from YAML get deleted from Zabbix. No orphans.

### Maintenance — `automation/maintenance/`

Maintenance windows as code. No more "who created that window and why?"

- Flexible scheduling: once, daily, weekly, monthly
- Same plan/deploy pipeline
- **Reconciliation** — orphaned windows get cleaned up automatically
- Manually created windows in Zabbix stay untouched

### Template Deployment — `automation/template-deploy/`

Push a template YAML, merge to main, CI imports it into Zabbix. Done.

---

## Requirements

| Component | Version |
|-----------|---------|
| Zabbix server | 6.0 LTS or 7.0 LTS |
| GitLab CI | Docker executor |

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
    ├── maintenance/
    │   ├── .gitlab-ci.yml
    │   ├── playbooks/
    │   │   ├── reconcile_maintenance.yml
    │   │   └── load_maintenance.yml
    │   └── maintenance/
    │       └── <project>/
    │           └── production.yml
    │
    └── template-deploy/
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