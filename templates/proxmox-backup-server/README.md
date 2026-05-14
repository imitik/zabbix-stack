# Proxmox Backup Server Template

Zabbix template for monitoring Proxmox Backup Server with **per-VM/CT granularity** for backup and verify status.

🌱 Still under under development, more metrics (GC, prune, sync, and other jobs) will be added later.

## Features

- **Per-VM/CT discovery** >> one item and trigger per backup group, automatically maintained via LLD
- **Backup status tracking** >> alerts when a VM's latest backup fails, clears automatically on next successful run (Interval ≤5 min)
- **Verify state tracking** >> alerts when PBS verification fails on a VM's latest snapshot, ignores pending/queued verifications
- **Human-readable format** >> e.g `OK` / `<timestamp> >> <error>` instead of raw JSON output

## Requirements

- Zabbix 7.4 or higher
- Proxmox Backup Server with API token access

## Setup

### 1. Create an API token and user on PBS

```
Datacenter >> Permissions >> User/API Tokens
```

Required permissions on `/`: `*.Audit`

### 2. Import the template

`Data collection >> Templates >> Import the YAML file`

### 3. Link to a host and set macros

| Macro | Description | Example |
|---|---|---|
| `{$PBS.HOST}` | PBS hostname/FQDN | `pbs.example.com` |
| `{$PBS.PORT}` | PBS API port | `8007 or 80-443 (if you use a reverse-proxy)` |
| `{$PBS.DATASTORE}` | Datastore name to monitor | `datastore-name` |
| `{$PBS.TOKEN.ID}` | API token ID | `e.g monitoring@pbs!zabbix` |
| `{$PBS.TOKEN.SECRET}` | API token secret | `xxxx-xxxx-xxxx-xxxx` |
| `{$PBS.BACKUPS.TIME}` | Backup task lookback window (days) | `1` |
| `{$PBS.SNAPSHOTS.TIME}` | Snapshot lookback window (days) | `7` |

## How it works

The template uses two master HTTP items that fan out to per machine dependent items via low-level discovery:

```
   ┌──────────────────────┐
   │ PBS: Get backups     │──┐
   └──────────────────────┘  │
                             ├──▶ PBS: VMs discovery (LLD) ──▶ per machine itemprototype + triggerprototype
   ┌──────────────────────┐  │
   │ PBS: Get snapshots   │──┘
   └──────────────────────┘
```

- **`pbs.backup.error[type,id]`** >> latest backup task status per VM. Alerts if not `OK`.
- **`pbs.verify.state[type,id]`** >> latest snapshot's verification state per VM. Alerts only on `failed`.

Recovery is automatic on the next master poll (default 5 min) once a successful backup or verification runs.

## Tested on

- Zabbix 7.4.5
- Proxmox Backup Server 4.2 API

## Reference tool used

- PBS API [Doc](https://pbs.proxmox.com/docs/api-viewer/index.html)
- Bruno lightweight API [Client](https://www.usebruno.com/) 

## License

MIT - see [`LICENSE`](../../LICENSE)