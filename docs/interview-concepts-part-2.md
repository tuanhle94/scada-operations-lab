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
| How did I use it? | *(Fill after Part 2.)* Enable history on `BearingTemperature`, store in `PipelineOps` Postgres, show a Power Chart / trend on `PumpDetail`. |
| How can it fail? | Database down, history not enabled, wrong pen path, or looking at a session that started before any rows existed. |

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
