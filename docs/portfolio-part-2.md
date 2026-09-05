# Portfolio Part 2: History and One Alarm

Do not start this until [Part 1](portfolio-part-1.md) is complete. Do not read [Part 4](portfolio-part-4.md) or later before this checklist is done.

## Cheat Sheet

- **Done means this:** the P-101 bearing-temperature trend shows new points, a high-temperature alarm can be made active, acknowledged with a comment, cleared, and later found in the alarm journal.
- **Historian** stores tag values over time. A **trend** reads that history.
- **Alarm lifecycle:** normal → active unacknowledged → active acknowledged → cleared acknowledged.
- Use **Postgres**, not the sample SQLite database, as the historian and alarm journal.
- Keep the expression tag. Do not add OPC UA, a second pump, or .NET yet.

```text
Changing tag -> Postgres history -> trend
Changing tag -> alarm -> ack -> clear -> journal
```

## What

Part 2 adds the two SCADA behaviors interviewers expect after a live value: **history** and **alarms**.

The same tag from Part 1, `[default]Station1/P101/BearingTemperature`, is stored over time and can raise a high-temperature alarm.

## Why

A live number without history cannot answer “what happened.” A live number without an alarm cannot demand operator attention. Energy SCADA roles are judged on both.

## When

Use this document for the second build arc. If Postgres takes the whole session, stop after Ignition can read and write the database. Continue the trend and alarm next time.

Move to [Part 3](portfolio-part-3.md) only after the checklist at the bottom is complete.

## How

### Session Plan

| Block | Stop when |
|---|---|
| 1. Local Postgres | A Postgres container is running and reachable from this machine |
| 2. Ignition database connection | Gateway status for the connection is valid |
| 3. Tag history and trend | New temperature points appear on a trend on `PumpDetail` |
| 4. High-temperature alarm | The alarm becomes active when the value is high |
| 5. Alarm views and journal | Ack, clear, and journal review all work |

### Block 1: Local Postgres

Create `docker-compose.yml` at the repo root if it does not exist:

```yaml
services:
  postgres:
    image: postgres:18
    environment:
      POSTGRES_USER: ignition
      POSTGRES_PASSWORD: ignition
      POSTGRES_DB: pipelineops
    ports:
      - "5432:5432"
    volumes:
      # Postgres 18+ expects the mount at /var/lib/postgresql (not .../data).
      - pipelineops-pgdata:/var/lib/postgresql

volumes:
  pipelineops-pgdata:
```

In PowerShell, from the repo root:

```powershell
docker compose up -d
```

Confirm the port is `5432`. Do not use a production password. This is a local lab.

These credentials will be in Git. That is acceptable for a local Maker lab. Do not reuse them anywhere real.

If Docker Desktop is installed but `docker` is not recognized, close and reopen the terminal, or start Docker Desktop first.

**Port 5432 already in use:** a native Windows Postgres service (`postgresql-x64-18`) will steal `localhost:5432`. Ignition then authenticates against the wrong server (`password authentication failed for user "ignition"`). Uninstall the Windows install if you do not need it, or temporarily map Docker to `"5433:5432"` and use that port in the JDBC URL.

### Block 2: Ignition database connection

In the **Gateway webpage** (`http://localhost:8088`), not Designer, not the Perspective client.

Ignition **8.3** has no classic **Config** gear on Home. Use the far-left icon rail.

1. Open `http://localhost:8088`.
2. Click **Log In** if you are not already signed in. Use the Gateway administrator you created in Part 1 (same user as Designer).
3. Click **Connections** on the far-left icon rail (plug icon).  
   *(8.1 wording: **Config → Databases → Connections**.)*
4. Create a PostgreSQL connection:
   - Name: `PipelineOps`
   - Driver: PostgreSQL
   - Connect URL: `jdbc:postgresql://localhost:5432/pipelineops`
   - Username: `ignition`
   - Password: `ignition` (the Docker DB password, not your Gateway login)
5. Save and confirm status is **Valid**.

In **Designer**:

1. Click **Project** in the menu bar (between **View** and **Component**).
2. Click **Properties**.
3. In the left tree of the dialog, click **General**.
4. Set **Default Database** to `PipelineOps` if it still says Sample SQLite.
5. Click **OK**, then **Ctrl+S**.

A Valid JDBC connection is **not** a historian yet. Continue Block 3.

### Block 3: History provider, tag history, and trend

A database connection does not appear in the tag **Storage Provider** list. Create a **SQL Historian** that uses `PipelineOps`.

In the **Gateway webpage** (`http://localhost:8088`):

1. Click **Services** on the far-left icon rail (stacked bars).  
   *(You are not on Home / Perspective Launch.)*
2. In the secondary menu, click **Historians**.
3. Click **Historians** if a nested list appears.
4. Click **Create Historian**.
5. Select **SQL Historian** (stores in your SQL database). Do not pick Core Historian / QuestDB for this lab. Do not pick DB Table Historian.
6. Click **Next**.
7. Name: `PipelineOps` (or `PipelineOpsHistory` if the name must be unique).
8. Set the database / datasource to connection **PipelineOps**.
9. Click **Create Historian**.
10. Confirm the new historian is listed and enabled.

In **Designer**, Tag Browser (if hidden: **View → Panels → Tag Browser**):

1. Expand **default** → **Station1** → **P101**.
2. Double-click **BearingTemperature** (or right-click → **Edit Tag**).
3. On the left of the Tag Editor, click **History**.
4. Set **History Enabled** to `true`.
5. Open **Storage Provider**. You should see the SQL Historian you created. Select it. Do **not** leave `<None>` or `Sample_SQLite_Database`.
6. Leave sample defaults if the dialog is simple (`On Change`, min time 1 second is fine).
7. Click **OK**, then **Ctrl+S**.

Wait about 30 seconds so rows exist.

On view **PumpDetail**:

1. Open **Perspective Components**.
2. Search **Power Chart** (or **Chart** / **Easy Chart** if Power Chart is missing).
3. Drag it onto the Flex root under the LED.
4. Bind or configure the pen to `[default]Station1/P101/BearingTemperature`.
5. **Ctrl+S**.
6. Launch `/pump-p101` and confirm new points appear without refreshing by hand.

If the chart is empty: history was enabled after you opened the session, Storage Provider is still Sample SQLite, the database connection is invalid, or the pen path is wrong.

If **Ctrl+S** fails with `Failed to commit changes to view '.../PumpDetail'` and the console says `root view not found`: Designer lost the view root in memory. Close the **PumpDetail** tab **without** saving, **File → Update Project**, reopen the view, confirm the Flex root and LED are still there, then add the chart onto the root (do not replace the root).

### Block 4: High-temperature alarm

The expression peaks near **160°F**. Set a **High** alarm that the sine can actually cross.

In the tag editor, **Alarms**:

1. Add alarm name: `HighBearingTemp`.
2. Mode / type: **Above setpoint** (or equivalent).
3. Setpoint: **155**.
4. Priority: **High** (or Medium if High is not offered).
5. Display path / message: `P-101 bearing temperature high`.
6. Deadband: **2** so it does not chatter at 155.0.
7. Require acknowledgment.
8. **OK**, **Ctrl+S**.

Watch Tag Browser. When the value is above 155, the alarm should be active. When it falls below about 153, it should clear.

To force a long active period for the demo, temporarily change the expression to `162` (a constant), save, observe the alarm, then restore the sine expression.

### Block 5: Alarm views and journal

**Alarm journal** (Gateway or Designer, depending on version):

1. Create an alarm journal named `PipelineOpsJournal`.
2. Store it in database `PipelineOps`.
3. Enable it for this project.

**Active alarms view:**

1. New view `AlarmStatus`, Flex, page URL `/alarms-status` (or replace the template Alarms page primary view if that is simpler).
2. Drag **Alarm Status Table**.
3. Save.

**Journal view:**

1. New view `AlarmJournal`, Flex, page URL `/alarms-journal`.
2. Drag **Alarm Journal Table**.
3. Point it at `PipelineOpsJournal`.
4. Save.

**Lifecycle demonstration:**

1. Drive temperature above 155 (wait for the sine, or set the tag to `162`).
2. On `/alarms-status`, confirm the alarm is active and unacknowledged.
3. Acknowledge it with comment `Part 2 high bearing temp check`.
4. Return the value below the clear point (restore the sine, or set `148`).
5. Confirm the alarm clears.
6. On `/alarms-journal`, confirm the activate, ack, and clear events exist.

## If Something Breaks

| Symptom | Likely cause | What to do |
|---|---|---|
| Database status Faulted | Postgres not running, wrong port, or JDBC URL | `docker compose ps`. Check `localhost:5432`. |
| `password authentication failed for user "ignition"` | JDBC hit a different Postgres on 5432 (native Windows install) | Uninstall Windows Postgres, or use Docker on another host port. Username/password are the Docker lab creds, not Gateway admin. |
| Storage Provider only shows Sample SQLite | JDBC connection exists; no SQL Historian | Gateway **Services → Historians → Create Historian → SQL Historian** on `PipelineOps`. |
| Trend is empty | History not enabled, Sample SQLite still selected, or too new | Enable history, pick the SQL Historian, wait 30s, check the pen path. |
| Failed to commit PumpDetail / `root view not found` | Designer canvas lost the view root | Close tab without save, **File → Update Project**, reopen, do not delete the Flex root. |
| Alarm never activates | Setpoint above 160, or alarm eval disabled | Setpoint 155. Confirm Alarm Eval Enabled. |
| Cannot acknowledge | Journal or permissions | Use the Gateway operator user. Confirm journal is enabled. |
| Journal table empty | Events not stored | Confirm journal database is `PipelineOps`, then re-run the lifecycle. |

## Do Not Build in Part 2

- Second pump or UDT
- OPC UA simulator
- .NET API
- Alarm flood or communication-loss scenarios
- Pretty overview graphics

## Concepts to Write After It Works

Create or fill [interview-concepts-part-2.md](interview-concepts-part-2.md) for **historian**, **trend**, and **alarm lifecycle** using:

```text
What is it?
Why does this project need it?
How did I use it?
How can it fail?
```

## Checklist

- [x] Start a local Postgres instance.
- [x] Create and verify the Ignition database connection.
- [x] Create a SQL Historian on `PipelineOps` and enable history on the bearing-temperature tag (Storage Provider is not Sample SQLite).
- [ ] Add a historical trend to the pump-detail view.
- [ ] Confirm that new values appear in the trend.
- [ ] Configure one high-temperature alarm with an understandable threshold.
- [ ] Configure an alarm journal so the event can be reviewed later.
- [ ] Create an alarm view with an Alarm Status Table for active alarms.
- [ ] Add an Alarm Journal Table for historical alarm events.
- [ ] Drive the simulated temperature above the threshold and observe the alarm becoming active.
- [ ] Acknowledge the alarm with a short comment.
- [ ] Return the value below the threshold and confirm that the alarm clears.
- [ ] Confirm that the alarm lifecycle appears in history.

When every box is checked, stop. Save Designer. Next is [Part 3: evidence](portfolio-part-3.md).
