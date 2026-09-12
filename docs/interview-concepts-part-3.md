# Interview Concepts: Part 3

Memorize these as spoken answers. Each card is **what it is**, **why this project needs it**, **how you used it**, and **how it can fail**. Claim **Parts 1–3 only**. Do not claim UDTs, OPC UA, or .NET until those exist.

---

## Why Part 3 exists

**Speak this:**  
A working Designer project on my laptop is not a portfolio. Part 3 packages screenshots, a Gateway backup, written problems I solved, and a Git commit so a reviewer can see the first vertical slice without logging into my machine.

---

## 1. Evidence packaging

**Speak this:**  
Evidence is what proves the live value, trend, and alarm lifecycle to someone who cannot open my Gateway.

| Prompt | Answer |
|---|---|
| What is it? | Screenshots, a restoreable backup, written failures, and a Git commit for Milestone 0. |
| Why does this project need it? | Interviewers cannot remote into Designer. Without evidence, “it works on my Gateway” is unverifiable. |
| How did I use it? | Folder `docs/evidence/part-3/`: Tag Browser Good quality, `/pump-p101` LED + Power Chart, alarm status active then acked, journal Active/Ack/Clear. Plus `problems-solved.md` and `backups/*.gwbk`. |
| How can it fail? | Empty chart screenshot, alarm already cleared when you capture status, or screenshots that never get committed. |

**If they ask “is a screenshot the historian?”**  
No. Screenshots prove the HMI. History still lives in Postgres through Ignition.

---

## 2. Gateway backup vs project export

**Speak this:**  
A Gateway backup (`.gwbk`) is the whole Gateway: projects, tags, database connections, historians, journals. A project export (`.zip`) is only that project’s Designer resources — not tags or JDBC connections.

| Prompt | Answer |
|---|---|
| What is it? | Two different restore tools. `.gwbk` restores the Gateway. Project `.zip` imports project resources into Designer/Gateway projects. |
| Why does this project need it? | My SCADA config lives on the Gateway, not only in this Git repo. Without a backup, the slice dies with the machine. |
| How did I use it? | Ignition 8.3: **Platform → System → Backup & Restore → Download Backup**. Stored `Ignition-MHmaytinh_Ignition-backup-maker20260912-1606.gwbk` under `backups/` with a README. |
| How can it fail? | Exporting only the project and expecting tags and Postgres connections to come back. Restoring a `.gwbk` overwrites the whole Gateway. |

**If they ask “is Postgres in the `.gwbk`?”**  
No. Historian and journal **rows** stay in Postgres. The backup has Ignition configuration that **points** at the database.

---

## 3. Written problem (troubleshooting narrative)

**Speak this:**  
I keep real failures in writing: what broke, what I thought, what it was, how I confirmed the fix. That is how support and interviews judge debugging, not a perfect demo path.

| Prompt | Answer |
|---|---|
| What is it? | Postmortems of problems I actually hit in Part 1 or 2. |
| Why does this project need it? | Energy roles hire people who can isolate layer failures. A clean checklist with no scars looks fake. |
| How did I use it? | Wrote `docs/evidence/part-3/problems-solved.md` with six real Part 1–2 failures. Primary story: Valid JDBC but Storage Provider stuck on Sample SQLite until SQL Historian. Also documented port 5432 clash, Designer root-view save failure, sine-trough false alarm, Ack Notes Required, and status-vs-journal. |
| How can it fail? | Inventing a problem, blaming “Ignition is weird,” or skipping the confirmation step. |

---

## 30-second story

> After the live tag, trend, and alarm lifecycle, I packaged evidence: screenshots, a Gateway backup, written problems from the build, and a Git commit. A reviewer can inspect the first vertical slice without using my Designer.

---

## Words to keep precise

| Say | Do not say |
|---|---|
| Screenshots of live value, trend, alarm lifecycle | “Trust me, it works on my laptop” |
| Gateway backup `.gwbk` / project export `.zip` | “I committed the Designer UI” |
| Config in the backup; rows in Postgres | “The `.gwbk` contains all my history data” |
| Real problems I solved | “Everything worked first try” |
| First vertical slice packaged | “I already have UDTs / OPC UA / .NET” |
