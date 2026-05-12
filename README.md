# Zabbix-stack

**🚧 Locked for now, until release**

A collection of Zabbix monitoring solutions to automate the boring stuff. Customized templates, bulk host management, and maintenance windows.

---

## Contents

## ☰ Templates

Monitoring templates for systems where official coverage is weak or nonexistent.
Each template ships with discovery rules, items/triggers, and macros, ready to import.

| Template | Monitor | How |
|----------|----------------|-----|
| [proxmox-backup-server](templates/proxmox-backup-server) *(coming soon)* | Datastores, backup/verify/sync/GC tasks, service health | HTTP agent >> PBS API |
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

### Hosts

Declarative host management

- One YAML file per project/environment
- Full support: host groups (even if not existing), templates, tags, macros, TLS/PSK, proxy, inventory
- **Reconciliation** smart tag to identify removed hosts from YAML get deleted from Zabbix. No orphans. (without affecting the hosts added manually or not from automation tool)

### Maintenances

Maintenance windows as code. No more "who created that window and why?"

- Flexible scheduling: once, daily, weekly, monthly
- Same plan/deploy pipeline
- **Reconciliation** — orphaned windows get cleaned up automatically (without affecting the maintenance added manually or not from automation tool)
- Manually created windows in Zabbix stay untouched

### Templates

Push a template YAML, merge to main, CI imports it into Zabbix.

- Auto-detects new, updated, and deleted templates per commit
- Creates missing host groups referenced in templates
- Deleted template files get pruned from Zabbix automatically

---

## Requirements

- Zabbix server **≥ 7.0 LTS**
- A Zabbix API token (component-specific scopes)
- GitLab CI runner with Docker executor

GitHub Actions support is comming

## Layout

```
zabbix-stack/
├── templates/
│   ├── proxmox-backup-server/
│   └── wazuh/
│
└── automation/
    ├── hosts/
    ├── maintenances/
    └── templates/
```

## Quick start

```bash
git clone https://github.com/imitik/zabbix-stack
cd zabbix-stack
```

*Each component has its own README*

---

## License

MIT — see [`LICENSE`](LICENSE).