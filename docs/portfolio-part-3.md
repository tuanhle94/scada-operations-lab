# Portfolio Part 3: Preserve Evidence

## Official Ignition 8.3 manual

Use these references to review the features in this part. These are study references; adding them does not record that you have read them or reverified the running lab.

| Manual topic | What to review for this project |
|---|---|
| [Gateway Backup and Restore](https://www.docs.inductiveautomation.com/docs/8.3/platform/gateway/gateway-backup-and-restore) | Gateway configuration backup and restore scope. |
| [Project Export and Import](https://www.docs.inductiveautomation.com/docs/8.3/platform/projects/project-export-and-import) | Project resources versus Gateway-level configuration. |
| [Alarm Journal](https://www.docs.inductiveautomation.com/docs/8.3/platform/alarming/alarm-journal) | The stored events shown in the lifecycle evidence. |


Do not start this until [Part 2](portfolio-part-2.md) is complete.

## Cheat Sheet

- **Done means this:** a reviewer can see screenshots of the live value, trend, and alarm lifecycle, restore or inspect a Gateway backup, read one real problem you solved, and find a Git commit for the vertical slice.
- Ignition configuration still lives on the Gateway. The backup file is what goes in Git (or a documented export folder).
- Do not start a UDT, second pump, or .NET in this part.

## What

Part 3 packages Milestone 0 evidence: value, history, and alarm lifecycle.

## Why

Interviewers cannot log into your laptop. Screenshots, a backup, and a written failure are the portfolio. A working Designer project that exists only on one Gateway is not inspectable.

## How

### Session plan

| Block | Stop when |
|---|---|
| 1. Screenshots | Six images in `docs/evidence/part-3/` |
| 2. Gateway backup | `.gwbk` and/or project `.zip` under `backups/` (or documented path) |
| 3. Written problem | `docs/evidence/part-3/problems-solved.md` filled with real failures |
| 4. README + commit | Current Status updated; evidence committed |

If Docker was stopped: start Docker Desktop, then from the repo root run `docker compose up -d`. Confirm Gateway `PipelineOps` database is still **Valid** before capturing trends or alarms.

### Block 1: Screenshots

Create folder `docs/evidence/part-3/` (already started) and save images there. Suggested filenames are in that folder’s README.

**1. Tag Browser (Designer)**

1. Open Designer → project **PipelineOps**.
2. Open **Tag Browser** *(if hidden: **View → Panels → Tag Browser**)*.
3. Expand **default → Station1 → P101**.
4. Click **BearingTemperature**.
5. Confirm Quality **Good** and a live numeric value.
6. Capture the Tag Browser area. Save as `01-tag-browser-bearing-temperature.png`.

**2–3. Pump page LED and trend (Perspective client)**

1. Open `http://localhost:8088/data/perspective/client/PipelineOps/pump-p101`  
   *(or Launch Perspective and go to `/pump-p101`)*.
2. Confirm the LED / numeric display is changing.
3. Capture the page. Save as `02-pump-p101-led.png`.
4. Confirm the Power Chart shows a curve (wait ~30s if needed; do not capture an empty chart).
5. Capture again. Save as `03-pump-p101-trend.png`.

**4–5. Alarm status (force active, then ack)**

1. In Designer, double-click **BearingTemperature**.
2. Set **Expression** to `162`. **OK**, **Ctrl+S**.
3. Confirm Tag Browser **HighBearingTemp** is **Active, Unacknowledged**.
4. Open `/alarms-status` in the browser.
5. Capture the active unacked row. Save as `04-alarm-status-active.png`.
6. Select the row → **Acknowledge**. Comment: `Part 2 high bearing temp check` (or `Part 3 evidence check`).
7. Capture the acknowledged state. Save as `05-alarm-status-acked.png`.

**6. Alarm journal**

1. Restore the sine expression, **OK**, **Ctrl+S**, wait until the value is below ~153.
2. Open `/alarms-journal`. Widen the date range if needed.
3. Capture rows that show **Active**, **Ack**, and **Clear** for `HighBearingTemp`.
4. Save as `06-alarm-journal-lifecycle.png`.

### Block 2: Gateway backup (Ignition 8.3)

Do this in the **Gateway webpage** (`http://localhost:8088`), not Designer.

**Full Gateway backup (preferred for restore):**

1. Click **Platform** on the far-left icon rail.
2. Click **System**.
3. Click **Backup & Restore**.
4. On the **Backup** tab, click **Download Backup**.
5. Save the `.gwbk` under repo folder `backups/`  
   *(create `backups/` if it does not exist)*.
6. If the file is too large for Git, keep it locally and write `backups/README.md` with the filename, date, and restore steps. Do not invent a path.

**Project export (Designer resources only — does not include tags or JDBC):**

1. Still under **Platform → System**, open **Projects**.
2. Find **PipelineOps**.
3. Open the **⋯** menu on that row.
4. Click **Export**.
5. Save the `.zip` under `backups/` as well if you want both.

*(8.1 wording: **Config → System → Backup/Restore** and project export under Projects.)*

**Speak this:** the `.gwbk` has Ignition config. Historian and journal **rows** stay in Postgres.

Do not commit secrets you did not intend to share. Local lab Postgres passwords in `docker-compose.yml` are expected.

### Block 3: Written problem

Write `docs/evidence/part-3/problems-solved.md` with real problems from Part 1 or 2. Prefer failures you actually hit, for example:

- JDBC **Valid** but Storage Provider only offered Sample SQLite (needed SQL Historian)
- Native Windows Postgres on 5432 stole the Docker lab port
- Designer `Failed to commit` / `root view not found` on `PumpDetail`
- Alarm looked “broken” while the sine was below 155
- `Error_Configuration` on the expression tag
- Binding **Enabled** left off
- View with no page URL

Format:

```text
What broke:
What I thought it was:
What it actually was:
How I confirmed the fix:
```

Interview cards: [interview-concepts-part-3.md](interview-concepts-part-3.md).

### Block 4: README + commit

Update the repo [README.md](../README.md) **Current Status** to state that the first vertical slice (value, trend, alarm lifecycle) is packaged with evidence.

Commit the docs, screenshots, compose file, and backup or backup instructions.

## Checklist

- [x] Save screenshots of the changing value, trend, and alarm views.
- [x] Export or back up the Ignition project.
- [x] Add a short README section describing what works.
- [x] Record one problem encountered and how it was solved.
- [x] Commit the complete vertical slice.

When every box is checked, the first vertical slice is packaged. Next is [Part 4](portfolio-part-4.md).

Interview prep: [interview-concepts-part-3.md](interview-concepts-part-3.md). Claim Parts 1–3 only. Do not claim UDTs, OPC UA, or .NET yet.
