# Interview Concepts: Part 4

## Official Ignition 8.3 manual

Start with UDTs for the current build block. Read the remaining topics as you reach them; Part 4 implementation is still pending verification.

| Manual topic | What to review for this project |
|---|---|
| [User Defined Types (UDTs)](https://www.docs.inductiveautomation.com/docs/8.3/platform/tags/user-defined-types-udts) | Create Pump, create instances, and understand inherited settings and overrides. |
| [Alarms in UDTs](https://www.docs.inductiveautomation.com/docs/8.3/platform/alarming/configuring-alarms/alarms-in-udts) | Define the shared alarm while keeping each pump identifiable. |
| [Tag Bindings in Perspective](https://www.docs.inductiveautomation.com/docs/8.3/ignition-modules/perspective/working-with-perspective-components/bindings-in-perspective/tag-bindings-in-perspective) | Use an indirect binding driven by pumpId for the live temperature. |
| [Pages in Perspective](https://www.docs.inductiveautomation.com/docs/8.3/ignition-modules/perspective/pages-in-perspective) | Page navigation and passing pumpId through /pump/:pumpId. |
| [Perspective Power Chart](https://www.docs.inductiveautomation.com/docs/8.3/appendix/components/perspective-components/perspective-chart-palette/perspective-power-chart) | Select the matching historical source when pump identity changes. |
| [Gateway Backup and Restore](https://www.docs.inductiveautomation.com/docs/8.3/platform/gateway/gateway-backup-and-restore) | Preserve configuration before the P-101 cutover and after verification. |


**Do not use these answers in interviews until the [Part 4 checklist](portfolio-part-4.md) is complete.** Parts 1–3 are the live slice plus evidence. Part 4 adds reusable equipment and navigation.

Memorize these as spoken answers. Each card is **what it is**, **why this project needs it**, **how you used it**, and **how it can fail**. Do not claim OPC UA, alarm flood, or .NET until those exist.

---

## Why Part 4 exists

**Speak this:**  
One hand-built pump is a demo. Plants model equipment once. Part 4 turns P-101 into a Pump UDT, adds P-102 from the same definition, and gives an overview with navigation so a change to the model reaches both pumps.

---

## 1. UDT (User-Defined Type)

**Speak this:**  
A UDT is a reusable equipment template: members like bearing temperature, with units, history, and alarms defined once.

| Prompt | Answer |
|---|---|
| What is it? | A tag data type definition under **UDT Definitions**. Instances inherit members and settings from that definition. |
| Why does this project need it? | Interviewers ask whether I change an alarm once or copy tags by hand. A UDT is the plant answer. |
| How did I use it? | *(Fill after Part 4.)* Created UDT `Pump` with `BearingTemperature` (°F, history, high alarm 155 / deadband 2), then instances for P-101 and P-102. |
| How can it fail? | Editing only one instance’s override and thinking the definition changed, or breaking `/pump-p101` while converting the old folder tags. |

**If they ask “is a UDT a PLC type?”**  
No. This is an Ignition tag model for the HMI/SCADA layer. The PLC may have its own structures later over OPC UA.

**If they ask “should the definition show a changing temperature?”**

No. A definition describes configuration; its members do not execute. The live simulation runs on an instance under **Tags**. Planned first-session check: create `Pump`, then verify `Station1/P102/BearingTemperature` before converting P-101. Implementation is still pending verification.

---

## 2. UDT instance

**Speak this:**  
An instance is one piece of equipment created from the UDT — `Station1/P101` and `Station1/P102` — not a copy-paste of every tag.

| Prompt | Answer |
|---|---|
| What is it? | A live tag folder/object under **Tags** whose members come from a UDT definition. |
| Why does this project need it? | Two pumps must share the same alarm and history rules without maintaining two hand-built trees. |
| How did I use it? | *(Fill after Part 4.)* |
| How can it fail? | Wrong parent folder path, overridden member that no longer inherits, or deleting the only working P-101 tags before the instance works. |

---

## 3. Parameterized pump detail + overview navigation

**Speak this:**  
One pump-detail view should take a tag path (or pump id) as a parameter. Overview lists pumps and navigates to the detail page. That is an application, not an orphan screen.

| Prompt | Answer |
|---|---|
| What is it? | View parameters + page navigation so the same graphic serves P-101 and P-102. |
| Why does this project need it? | Six pumps later must not mean six nearly identical views. |
| How did I use it? | *(Fill after Part 4.)* |
| How can it fail? | Hard-coded `[default]Station1/P101/...` left in the detail view, or overview with no page URL. |

---

## 4. Inheritance and instance overrides

**Speak this:**  
The definition supplies shared settings. An instance override is a deliberate exception, so I check overrides when one pump does not follow a model change.

| Prompt | Answer |
|---|---|
| What is it? | Shared UDT configuration with optional per-instance exceptions. |
| Why does this project need it? | Both pumps should inherit the same history and alarm rules. |
| How did I use it? | *(Pending verification.)* Change the member Documentation once and inspect both instances; remove temporary simulation overrides after testing. |
| How can it fail? | An alarm or history override hides later definition changes; a shared hard-coded P-101 label misidentifies P-102. |

---

## 5. One pump identity across live data and history

**Speak this:**  
The selected pump must drive the heading, current value, historical trend, and alarm source together. A changing LED alone does not prove the screen switched pumps.

| Prompt | Answer |
|---|---|
| What is it? | A `pumpId` input used consistently across a reusable detail view. |
| Why does this project need it? | An operator must not see P-102's number next to P-101's trend or alarm. |
| How did I use it? | *(Pending verification.)* Use `/pump/:pumpId`, an indirect live tag binding, and the chart's actual historical source for each pump; test navigation and direct URL refresh. |
| How can it fail? | Hard-coded title, historical source, or alarm filter; a numeric live binding used where the chart expects a history-path string. |

---

## 6. Historian schema ownership and inherited history settings

**Speak this:**

Ignition manages its SQL Historian tables. I configure the connection, historian, and tag history settings instead of writing migrations for those internal tables. My own application tables would still need their own schema management.

| Prompt | Answer |
|---|---|
| What is it? | With tag history enabled, the selected Storage Provider routes samples to a configured historian. The SQL Historian creates and maintains its required tables and writes samples to Postgres. Multiple tags share historian tables and are distinguished by tag IDs; each pump does not need its own table. |
| Why does this project need it? | Stored samples let the Power Chart show what happened before an alarm. Defining history on the Pump UDT lets instances inherit the same storage settings. |
| How did I use it? | *(Part 4 pending verification.)* Plan: enable history on Pump/BearingTemperature and select the existing PipelineOpsHistory SQL Historian, then verify new samples from P102. The definition supplies configuration; running instances produce samples. |
| How can it fail? | History disabled, wrong provider, unavailable database, insufficient database permissions, full storage, or an instance override that blocks the shared settings. Manually changing Ignition-owned tables can break history. |

**If they ask “does this replace database migrations?”**

For Ignition's internal historian schema, I use the product's supported configuration and upgrade process instead of writing my own migrations. This does not manage arbitrary application tables. I remain responsible for database availability, permissions, backups, and storage capacity. A Gateway backup does not include the Postgres history rows.

**If they ask “does a 1-second execution rate mean one stored row per second?”**

No. Fixed Rate execution controls how often the expression recalculates. History sampling and deadband settings determine which samples are recorded. Selecting a Storage Provider does not by itself enable history.

Official manual: [History Providers](https://www.docs.inductiveautomation.com/docs/8.3/ignition-modules/tag-historian/tag-history-providers), [UDTs](https://www.docs.inductiveautomation.com/docs/8.3/platform/tags/user-defined-types-udts), and [Gateway Backup and Restore](https://www.docs.inductiveautomation.com/docs/8.3/platform/gateway/gateway-backup-and-restore).

---

## 30-second story (only after Part 4 works)

> After packaging the first pump slice, I modeled a Pump UDT, created P-101 and P-102 from it, built an overview with navigation to a parameterized detail view, and changed the UDT once so both pumps inherited the update. Still monitoring only. Still no OPC UA or .NET.

---

## Words to keep precise

| Say | Do not say |
|---|---|
| UDT definition vs instance | “I duplicated the tag folder” |
| Change once on the UDT | “I edited both pumps by hand” |
| Ignition manages its historian schema | “Ignition manages all my application tables” |
| Execution rate and history sampling are separate | “A 1-second expression rate guarantees one database row per second” |
| Parameterized view / tag path | “I copied PumpDetail for P-102” |
| Overview + navigation | “I only have one orphan page” |
| Still supervisory | “I control the pumps” / “I have OPC UA” |
