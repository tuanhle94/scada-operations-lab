# Problems solved (Part 3 evidence)

Real failures from Parts 1–2. Use any of these in interviews. Lead with the one that matches the question (historian, JDBC, Designer, alarms).

Each card:

```text
What broke:
What I thought it was:
What it actually was:
How I confirmed the fix:
```

---

## 1. Valid JDBC, but history still Sample SQLite

**What broke:**  
Gateway connection `PipelineOps` was **Valid**. On `BearingTemperature`, **History Enabled** was true, but **Storage Provider** only offered `<None>` and `Sample_SQLite_Database`. No Postgres option.

**What I thought it was:**  
That a Valid database connection was the historian — enabling history would automatically use `PipelineOps`.

**What it actually was:**  
JDBC is only the wire. Tag history needs a separate **SQL Historian** that uses that connection. Until **Services → Historians → Create Historian → SQL Historian** existed, Storage Provider could only show Sample SQLite.

**How I confirmed the fix:**  
Created the SQL Historian on `PipelineOps` (showed up as `PipelineOpsHistory` in Tag Browser), set Storage Provider to it, saved, added a Power Chart, saw a live curve on `/pump-p101`, and `SELECT COUNT(*)` on `sqlt_data_1_*` in Docker Postgres returned growing rows.

**Speak this:** Valid JDBC is not history. The SQL Historian writes the samples.

---

## 2. Password authentication failed for user "ignition"

**What broke:**  
Ignition database connection test failed with `FATAL: password authentication failed for user "ignition"` even though `docker-compose.yml` had user/password `ignition`.

**What I thought it was:**  
Wrong password typed in the Gateway form, or Docker Postgres not initialized.

**What it actually was:**  
A native Windows Postgres service (`postgresql-x64-18`) already owned `localhost:5432`. Ignition was authenticating against that server, which had no `ignition`/`ignition` user. Inside the container, `psql -U ignition` worked.

**How I confirmed the fix:**  
Uninstalled Windows Postgres, remapped Docker back to host `5432`, set JDBC URL to `jdbc:postgresql://localhost:5432/pipelineops`, saved, status **Valid**. (Temporary workaround during diagnosis: Docker on `5433` also worked.)

**Speak this:** Same port, different Postgres. Always check what is listening on 5432.

---

## 3. Designer failed to commit PumpDetail (`root view not found`)

**What broke:**  
**Ctrl+S** failed with `Failed to commit changes to view '.../PumpDetail'` and console `Attempted to collect View Configuration for save, but root view not found!` / `expected string, got null`.

**What I thought it was:**  
Gateway down, permissions, or a conflict with Update Project.

**What it actually was:**  
The Perspective Designer canvas lost the view **root** in memory (JxBrowser / designer state). Save had nothing valid to serialize. Gateway was still up (`HTTP 200`).

**How I confirmed the fix:**  
Closed the `PumpDetail` tab **without** saving, **File → Update Project**, reopened the view, confirmed Flex root and LED were still there, saved cleanly, then re-added the Power Chart onto the root (did not replace the root).

**Speak this:** That error is a broken Designer canvas root, not a bad Postgres connection.

---

## 4. Alarm “not working” while the sine was below 155

**What broke:**  
Tag Browser showed `HighBearingTemp` with `IsActive` false, `EventValue` ~144, and old timestamps even though the chart had gone above 155 earlier. Felt like the alarm never fired.

**What I thought it was:**  
Wrong mode, Alarm Eval disabled, or the alarm never saved.

**What it actually was:**  
The alarm **was** configured (Above Setpoint 155, Enabled). The property list was showing a cleared / stale moment while the sine was in the trough. Later crossings update `ActiveTime` / `IsActive` when the live value is actually above 155. Also: do not expect the LED page to turn red — status is Tag Browser or `/alarms-status`.

**How I confirmed the fix:**  
Forced expression to `162`, refreshed Tag Browser on `HighBearingTemp`, saw **Active, Unacknowledged** with `EventValue` near 162. Later captured **Active, Unacknowledged** on `/alarms-status` for Part 3 evidence.

**Speak this:** Active means the condition is true *now*. A trough is not a failed alarm.

---

## 5. Acknowledge worked with no comment

**What broke:**  
Ack Mode was Manual, but clicking Acknowledge on `/alarms-status` did not require a note.

**What I thought it was:**  
Manual ack always forces a comment dialog.

**What it actually was:**  
**Ack Notes Required** was still off. Manual only means a person must ack; the note is a separate property.

**How I confirmed the fix:**  
Set **Ack Notes Required** true on `HighBearingTemp`, saved, drove the alarm active again, Acknowledge blocked until comment `Part 2 high bearing temp check` / `Part 3 evidence check`. Journal showed an **Ack** event; `alarm_event_data` had `ackNotes`.

**Speak this:** Manual ack ≠ required notes. Turn on Ack Notes Required for an audit trail.

---

## 6. Alarm Status Table empty while I expected a row

**What broke:**  
Opened `/alarms-status` and saw no active row even after working on the alarm.

**What I thought it was:**  
Broken table binding or wrong page URL.

**What it actually was:**  
Alarm Status is **live only**. If the sine is below 155, the active row is gone. That is correct. Journal still has history. Filters on the table can also hide states.

**How I confirmed the fix:**  
Set expression to `162`, confirmed Tag Browser **Active, Unacknowledged**, refreshed `/alarms-status`, row appeared. After clear, status emptied and `/alarms-journal` still showed Active / Ack / Clear.

**Speak this:** Status is now. Journal is history.

---

## Which to lead with

| If they ask about… | Tell story |
|---|---|
| Historian / Sample SQLite | **1** |
| JDBC / Docker / ports | **2** |
| Designer save failures | **3** |
| Alarm not activating | **4** |
| Operator ack / comments | **5** |
| Status vs journal | **6** |

Primary portfolio write-up for “one problem” checklists: **story 1** (SQL Historian). Keep the others ready as follow-ups.
