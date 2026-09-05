# Interview Concepts: Part 2

**Do not use these answers in interviews until the [Part 2 checklist](portfolio-part-2.md) is complete.** Part 1 only proved a live value.

Memorize these as spoken answers after history and the alarm lifecycle work. Each card is **what it is**, **why this project needs it**, **how you used it**, and **how it can fail**. Do not claim OPC UA, UDTs, or .NET until those exist.

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
| How did I use it? | Created a SQL Historian on connection `PipelineOps`, set `BearingTemperature` History Enabled and Storage Provider to that historian (not Sample SQLite). Trend on `PumpDetail` after the chart is added. |
| How can it fail? | Database down, history not enabled, Storage Provider still Sample SQLite, no SQL Historian (only a JDBC connection), wrong pen path, or a session that started before any rows existed. |

---

## 2. Alarm lifecycle

**Speak this:**  
An alarm is not a red color. It has states: active unacknowledged, active acknowledged, and cleared, and those events are kept in a journal.

| Prompt | Answer |
|---|---|
| What is it? | A configured condition on a tag that demands operator attention, with ack and history. |
| Why does this project need it? | Bearing temperature exists to detect overheating. If it never alarms, it is only a demo metric. |
| How did I use it? | *(Fill after Part 2.)* High alarm at 155°F on P-101, deadband 2, ack comment, journal in Postgres. |
| How can it fail? | Setpoint the sine never crosses, no journal, ack without a comment trail, or treating a communication failure as a high-temperature alarm. |

**If they ask “did you acknowledge from a script?”**  
No. The operator acknowledges in the HMI. Later, AI must never ack or suppress alarms.

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

## 30-second story (only after Part 2 works)

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
