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
    image: postgres:16
    environment:
      POSTGRES_USER: ignition
      POSTGRES_PASSWORD: ignition
      POSTGRES_DB: pipelineops
    ports:
      - "5432:5432"
    volumes:
      - pipelineops-pgdata:/var/lib/postgresql/data

volumes:
  pipelineops-pgdata:
```

In PowerShell, from the repo root:

```powershell
docker compose up -d
```

Confirm the port is `5432`. Do not use a production password. This is a local lab.

These credentials will be in Git. That is acceptable for a local Maker lab. Do not reuse them anywhere real.

### Block 2: Ignition database connection

In the **Gateway webpage** (`http://localhost:8088`), not Designer, not the Perspective client:

1. Log in as the Gateway administrator.
2. Open **Connections** (or **Config → Databases → Connections**, depending on version).
3. Create a connection:
   - Name: `PipelineOps`
   - Driver: PostgreSQL
   - Connect URL: `jdbc:postgresql://localhost:5432/pipelineops`
   - Username: `ignition`
   - Password: `ignition`
4. Save and confirm status is **Valid**.

In **Designer**, set the project default database to `PipelineOps` if the project still points at Sample SQLite.

### Block 3: History and trend

In **Designer**, open tag `[default]Station1/P101/BearingTemperature`.

1. Find **History**.
2. Set **History Enabled** to true.
3. Set the history provider / database to `PipelineOps` (wording varies by version).
4. Use a one-second sample that matches the tag rate, or the default if the dialog is simpler.
5. **OK**, then **Ctrl+S**.

Wait about 30 seconds so rows exist.

On view **PumpDetail**:

1. Open **Perspective Components**.
2. Search **Power Chart** (or **Chart** / **Easy Chart** if Power Chart is missing).
3. Drag it onto the Flex root under the LED.
4. Bind or configure the pen to `[default]Station1/P101/BearingTemperature`.
5. **Ctrl+S**.
6. Launch `/pump-p101` and confirm new points appear without refreshing by hand.

If the chart is empty: history was enabled after you opened the session, the database connection is invalid, or the pen path is wrong.

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
| Trend is empty | History not enabled, or too new | Enable history, wait 30s, check the pen path. |
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

- [ ] Start a local Postgres instance.
- [ ] Create and verify the Ignition database connection.
- [ ] Enable history for the bearing-temperature tag.
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
