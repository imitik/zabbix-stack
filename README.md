# zabbix-stack

A collection of zabbix monitoring templates and automation scripts I develop
and maintain. Everything Zabbix-related lives here, templates, automation workload

---

## Contents

### 📊 Templates — `templates/`

Custom monitoring templates for systems without good official coverage.
Imported through *Configuration → Templates → Import* in the Zabbix UI.

| Template | Monitors | Method |
|----------|----------|--------|
| [proxmox-backup-server](templates/proxmox-backup-server) | PBS datastores, backup jobs, verify, GC, service health | HTTP agent (PBS API) |
#| [longhorn-backup](templates/longhorn-backup) | Longhorn backup targets, snapshots, job health | HTTP agent (Longhorn API) |
| [wazuh](templates/wazuh) | Wazuh manager, agent counts, indexer cluster health | HTTP agent (Wazuh API) |

### ⚙️ Automation — `automation/`

Python scripts that talk to the Zabbix API to manage things in bulk.
Run from your laptop, a CI runner, or a cron job.

| Tool | Purpose |
|------|---------|
| [hosts](automation/hosts) | Bulk create / update / delete hosts from CSV or YAML |
| [maintenance-periods](automation/maintenance-periods) | Create and manage maintenance windows programmatically |
| [templates](automation/templates) | Bulk-assign or unassign templates to host groups |

---

## Requirements

| Component | Version |
|-----------|---------|
| Zabbix server | 6.0 LTS or 7.0 LTS |
| Python (for automation) | 3.10 or newer |
| Zabbix API token | with appropriate permissions per task |

---

## Repository layout

```
zabbix-stack/
├── README.md
├── LICENSE
├── CHANGELOG.md
├── SECURITY.md
├── .gitignore
│
├── templates/
│   ├── README.md
│   ├── proxmox-backup-server/
│   └── wazuh/
│
└── automation/
    ├── README.md
    ├── _common/                  shared API helpers
    ├── hosts/
    ├── maintenance-periods/
    └── templates/
```

---

## Getting started

```bash
git clone https://github.com/imitik/zabbix-stack
cd zabbix-stack
```

Then jump into the subfolder you need:

- **Importing a template** → see `templates/<name>/README.md`
- **Running an automation script** → see `automation/<name>/README.md`

---

## Contributing

Issues and pull requests are welcome. When opening an issue, please include:

- Zabbix server version
- Which template or script is affected
- Error message or unexpected behavior
- Reproduction steps if possible

---

## Security

If you find a security issue, please **do not** open a public issue.
See [`SECURITY.md`](SECURITY.md) for the private reporting process.

---

## License

MIT — see [`LICENSE`](LICENSE).
