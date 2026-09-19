# Interview Concepts: Part 2

## Official Ignition 8.3 manual

Use these references to review the features in this part. These are study references; adding them does not record that you have read them or reverified the running lab.

| Manual topic | What to review for this project |
|---|---|
| [Database Connections](https://www.docs.inductiveautomation.com/docs/8.3/platform/database-connections) | The Gateway connection to Postgres through JDBC. |
| [History Providers](https://www.docs.inductiveautomation.com/docs/8.3/ignition-modules/tag-historian/tag-history-providers) | SQL Historian configuration, separate from the database connection. |
| [Perspective Power Chart](https://www.docs.inductiveautomation.com/docs/8.3/appendix/components/perspective-components/perspective-chart-palette/perspective-power-chart) | Historical pens and their data sources. |
| [Configuring Alarms](https://www.docs.inductiveautomation.com/docs/8.3/platform/alarming/configuring-alarms) | Alarm conditions, deadband, and acknowledgment settings. |
| [Alarming](https://www.docs.inductiveautomation.com/docs/8.3/platform/alarming) | Alarm states and the distinction between status and history. |
| [Alarm Journal](https://www.docs.inductiveautomation.com/docs/8.3/platform/alarming/alarm-journal) | Storing alarm events using a database journal. |


Memorize these as spoken answers. Each card is **what it is**, **why this project needs it**, **how you used it**, and **how it can fail**. Claim **Parts 1–2** for the live slice; Part 3 evidence is [interview-concepts-part-3.md](interview-concepts-part-3.md). Do not claim UDTs, OPC UA, or .NET until those exist.

---

## Interview prep after Part 2

Lead with SCADA words, not Docker. If they ask how you store history, say: **Ignition historian → SQL Historian → JDBC → Postgres**. Docker is how you ran Postgres locally.

**Two stories to memorize**

1. Part 1: one simulated bearing temperature, one tag, one page `/pump-p101`. Monitor only. See [interview-concepts-part-1.md](interview-concepts-part-1.md).
2. Part 2: history in Postgres, Power Chart, high alarm at 155, Manual ack with a comment, clear, journal. Still no pump control. Use the 30-second story below.

**Likely follow-ups**

- JDBC connection vs SQL Historian vs Storage Provider vs Sample SQLite
- LED tag binding vs Power Chart history
- Active / acknowledged / cleared; Ack Mode Manual; status table vs journal
- View vs page (`PumpDetail` vs `/pump-p101`) — that card lives in Part 1
- “Did you ack from a script?” — No. AI must never ack or suppress.

**Do not say**

- “I built a Postgres historian”
- “The screen turning red is the alarm”
- “I have a plant PI / OPC UA / .NET API”
- “I control the pump”

**Next after Part 2**  
Part 3 packages evidence: screenshots, Gateway backup, written problems, Git commit. When that checklist is done, use [interview-concepts-part-3.md](interview-concepts-part-3.md).

---

## Why Part 2 exists

**Speak this:**  
A live number is not SCADA yet. Part 2 adds history so I can see what the temperature did, and one high-temperature alarm so an operator can be required to act. That is the rest of the first vertical slice.

---

## 1. Historian

**Speak this:**  
A historian stores tag values over time in a database so a trend can show the past, not only the current sample.

| Prompt | Answer |
|---|---|
| What is it? | Time-series storage for tags. This lab uses Postgres through Ignition’s history, not a screenshot of one number. |
| Why does this project need it? | Operators and interviewers ask what happened before the alarm. Without history I can only guess. |
| How did I use it? | Created a SQL Historian on connection `PipelineOps`, set `BearingTemperature` History Enabled and Storage Provider to that historian (not Sample SQLite). Power Chart on `PumpDetail` reads that history. |
| How can it fail? | Database down, history not enabled, Storage Provider still Sample SQLite, no SQL Historian (only a JDBC connection), wrong pen path, or a session that started before any rows existed. |

---

## 2. Alarm lifecycle

**Speak this:**  
An alarm is not a red color. It has states: active unacknowledged, active acknowledged, and cleared, and those events are kept in a journal.

| Prompt | Answer |
|---|---|
| What is it? | A configured condition on a tag that demands operator attention, with ack and history. |
| Why does this project need it? | Bearing temperature exists to detect overheating. If it never alarms, it is only a demo metric. |
| How did I use it? | High alarm `HighBearingTemp`, Above Setpoint 155, deadband 2, Ack Mode Manual, Ack Notes Required. Operator acked from `/alarms-status` with comment `Part 2 high bearing temp check`. Restored the sine so it cleared. `/alarms-journal` on `PipelineOpsJournal` showed Active, Ack, and Clear. |
| How can it fail? | Setpoint the sine never crosses, no journal, ack without a comment trail, or treating a communication failure as a high-temperature alarm. |

**If they ask “did you acknowledge from a script?”**  
No. The operator acknowledges in the HMI. Later, AI must never ack or suppress alarms.

**If they ask “what is Ack Mode?”**  
It is how the alarm becomes acknowledged. **Manual** means a person must ack. **Auto** acks when the condition clears. **Unused** means there is no real ack step. This lab uses Manual plus Ack Notes Required.

**If they ask “status table vs journal?”**  
Status is live: what is active now. The journal is history: activate, ack, and clear stay after the row leaves the status table.

---

## 3. Postgres

**Speak this:**  
I needed an external database for tag history and the alarm journal, not Ignition’s sample SQLite. Postgres is a real client–server database Ignition supports, so trends and alarm events survive Gateway restarts. “Why Postgres” means “why not the toy DB,” not “Postgres is the only SCADA historian.”

| Prompt | Answer |
|---|---|
| What is it? | A relational database server. In this lab it holds Ignition tag history rows and alarm journal events. |
| Why does this project need it? | A live tag cannot answer what temperature did before the alarm. History and journal need durable storage outside the sample SQLite database. |
| How did I use it? | Local instance from `docker-compose.yml`: database `pipelineops`, user `ignition`, port `5432`. Ignition connection name `PipelineOps`. |
| How can it fail? | Container or service down, wrong port, a native Windows Postgres stealing 5432, bad credentials, or Ignition still pointed at Sample SQLite. |

**If they ask “why not PI, Influx, or MSSQL?”**  
Plants use all of those. I chose Postgres because Ignition talks to it cleanly over JDBC, it runs locally in Docker, and I already know SQL on it. The skill is wiring Ignition history and journals to an external database.

**If they ask “is Postgres the historian?”**  
No. Ignition’s historian and alarm journal are the product features. Postgres is the storage behind them.

**If they ask how you store history:**  
Ignition historian → SQL Historian → JDBC → Postgres. Do not lead with Docker.

---

## 4. JDBC

**Speak this:**  
JDBC is how Ignition’s Java Gateway talks to Postgres with a connection URL, username, and password. It is the wire, not the historian.

| Prompt | Answer |
|---|---|
| What is it? | Java Database Connectivity — a standard API so Java applications use a driver to reach a database. |
| Why does this project need it? | The Gateway is Java. Without a JDBC connection, Ignition cannot write history or journal events to Postgres. |
| How did I use it? | Gateway webpage → database connection `PipelineOps`, PostgreSQL driver, URL `jdbc:postgresql://localhost:5432/pipelineops`, lab credentials. Status must be Valid. |
| How can it fail? | Wrong host or port, Postgres down, password for the wrong Postgres on 5432, or driver mismatch → connection Faulted, empty trend, empty journal. |

**If they ask “did you write JDBC code?”**  
No. I configured the Gateway connection. Ignition uses the driver; I do not hand-write JDBC in this lab.

---

## 5. Docker

**Speak this:**  
Docker runs Postgres locally as a container so I get a repeatable lab database without installing a full Windows Postgres server. It is plumbing for the database, not SCADA.

| Prompt | Answer |
|---|---|
| What is it? | A way to run an application in an isolated container from a fixed image, with env vars, ports, and a volume. |
| Why does this project need it? | Part 2 needs Postgres on this machine. Compose starts the same lab DB every time from one file. |
| How did I use it? | Repo-root `docker-compose.yml`, then `docker compose up -d`. Postgres listens on `localhost:5432`. |
| How can it fail? | Docker Desktop not running, WSL missing, port 5432 in use, or container stopped while Ignition still expects the DB. |

**If they ask “why Docker for SCADA?”**  
Not because plants run the operator application in Docker Desktop. Because local lab setup should be repeatable. Production might be managed Postgres or a plant SQL server; Ignition still connects the same way — JDBC to a database.

**If they ask “why Docker instead of installing Postgres locally?”**  
Either works. I used Docker so the lab database is defined in the repo — same image, user, password, port, and volume every time — without a Windows Postgres install to maintain. Ignition only cares that something answers on `localhost:5432` over JDBC. A local install is fine for this lab if you prefer; Part 2’s real requirement is external Postgres, not Docker. Honest tradeoff: Docker adds Desktop and WSL on Windows.

**One sentence that ties them:**  
Docker runs Postgres; JDBC is how Ignition connects to it; a SQL Historian writes tag samples through that connection; the trend and alarm journal are why that stack matters.

---

## 6. SQL Historian / Storage Provider

**Speak this:**  
A Valid JDBC connection is not history. I still had to create a SQL Historian on the Gateway and set the tag’s Storage Provider to it. Until that exists, the tag can only offer Sample SQLite.

| Prompt | Answer |
|---|---|
| What is it? | Ignition’s historian product feature: a named provider that samples tags and writes rows into a database. Storage Provider is the tag field that picks which historian. |
| Why does this project need it? | Operators ask what temperature did before the alarm. The JDBC connection only proves Ignition can reach Postgres. The historian is what stores samples. |
| How did I use it? | Gateway 8.3: **Services → Historians → Create Historian → SQL Historian** on connection `PipelineOps`. Designer Tag Editor → History → History Enabled true → Storage Provider = that historian. |
| How can it fail? | Only a database connection exists, provider left at Sample SQLite, historian disabled, or Core/QuestDB chosen when the lab story is Postgres. |

**If they ask “why not leave Sample SQLite?”**  
That is Ignition’s toy store. The portfolio requires history in the same Postgres as the lab, not the sample file.

**If they ask “is this a plant PI historian?”**  
No. This is Ignition Tag Historian writing to SQL. Plants may use PI, Influx, or a vendor historian. The skill is wiring Ignition history to an external database.

---

## 7. Trend / Power Chart

**Speak this:**  
A trend is a chart that reads historian samples over time. The live LED is the current value. The Power Chart is what happened.

| Prompt | Answer |
|---|---|
| What is it? | An HMI component whose pen is bound to stored tag history, not a one-shot screenshot. |
| Why does this project need it? | After a high-temperature event, the first question is what the bearing did before the alarm. The LED cannot answer that. |
| How did I use it? | Power Chart on `PumpDetail`. Pen name `BearingTemperature`. `pens.0.data.source` = `[default]Station1/P101/BearingTemperature`. Page `/pump-p101` shows new points without a manual refresh. |
| How can it fail? | Empty source, Storage Provider still Sample SQLite, Gateway connection Faulted, or a session opened before any history rows existed. |

**If they ask “is this a live binding like the LED?”**  
The LED is a tag binding to the current value. The chart queries history. Same tag, different path: live vs stored.

---

## 8. Proving it in Postgres (SQL)

**Speak this:**  
Ignition owns the historian and journal. Postgres holds the rows. I can prove that with SQL, not only with the HMI.

| Prompt | Answer |
|---|---|
| What is it? | A read-only check of the tables Ignition created: `sqlt_data_*` for tag samples, `alarm_events` / `alarm_event_data` for the journal. |
| Why does this project need it? | Interviewers may not trust a chart. SQL shows the same Postgres the Gateway writes to. |
| How did I use it? | `docker exec` into the lab container, `\dt`, `COUNT(*)` on `sqlt_data_1_2026_09`, then `alarm_events` and `ackNotes` in `alarm_event_data`. |
| How can it fail? | Container down, querying the wrong month partition, or editing Ignition tables by hand and breaking the historian. |

**If they ask “did you design those tables?”**  
No. Ignition’s SQL Historian and alarm journal create and write them. I query them to verify.

---

## 30-second story

> After the live tag, I stored bearing temperature in Postgres and showed a trend. I configured a high-temperature alarm, acknowledged it with a comment, cleared it, and confirmed the lifecycle in the alarm journal. Still supervisory. Still no pump control.

---

## Words to keep precise

| Say | Do not say |
|---|---|
| Historian / tag history | “I have a database dashboard” |
| Active, acknowledged, cleared | “The screen turned red” |
| Journal | “Alarms disappear when they clear” |
| Postgres for history | “I still only have SQLite sample” |
| Ignition stores history in Postgres | “I built a Postgres historian” / “Postgres replaces Ignition” |
| JDBC connection / JDBC URL | “I wrote SQL over JDBC to build SCADA” |
| Container runs Postgres | “Docker is my historian” / “Docker stores my tags” |
| Docker for a repeatable local DB | “Docker is required for Ignition historians” |
| SQL Historian / Storage Provider | “The Valid JDBC connection is the historian” |
| History in Postgres through Ignition | “I left Sample SQLite as the historian” |
| Trend / Power Chart of history | “The LED is the historian” / “I screenshot the number over time” |
| Query Ignition-owned tables | “I built a custom historian schema” / “I update those rows by hand” |
| Ignition historian → SQL Historian → JDBC → Postgres | Lead with “I used Docker for SCADA” |
| Still supervisory / no pump control | “I control the pump” / “I have PI, OPC UA, or a .NET API” |
