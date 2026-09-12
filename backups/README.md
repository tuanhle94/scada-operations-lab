# Backups

## Gateway backup (Part 3)

| File | Notes |
|---|---|
| `Ignition-MHmaytinh_Ignition-backup-maker20260912-1606.gwbk` | Full Gateway backup from Ignition Maker 8.3 (**Platform → System → Backup & Restore → Download Backup**), 2026-09-12 |

### What this file contains

- Gateway configuration: projects (including `PipelineOps`), tags, database connections, SQL Historian, alarm journal profile, and related Gateway settings.

### What it does **not** contain

- Historian and alarm journal **rows** in Postgres (those live in the Docker `pipelineops` database / volume).
- Docker images or `docker-compose` runtime state.

### Restore (lab)

1. Gateway webpage → **Platform → System → Backup & Restore**.
2. Use the **Restore** tab and select this `.gwbk`.
3. Confirm you intend to overwrite the current Gateway.
4. Bring up Postgres (`docker compose up -d`) so JDBC `PipelineOps` can become Valid again.

Lab Postgres credentials in `docker-compose.yml` (`ignition` / `ignition`) are intentional for this Maker lab. Do not reuse them anywhere real.
