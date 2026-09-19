# Interview Concepts: Part 4

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

## 30-second story (only after Part 4 works)

> After packaging the first pump slice, I modeled a Pump UDT, created P-101 and P-102 from it, built an overview with navigation to a parameterized detail view, and changed the UDT once so both pumps inherited the update. Still monitoring only. Still no OPC UA or .NET.

---

## Words to keep precise

| Say | Do not say |
|---|---|
| UDT definition vs instance | “I duplicated the tag folder” |
| Change once on the UDT | “I edited both pumps by hand” |
| Parameterized view / tag path | “I copied PumpDetail for P-102” |
| Overview + navigation | “I only have one orphan page” |
| Still supervisory | “I control the pumps” / “I have OPC UA” |
