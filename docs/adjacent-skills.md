# Adjacent Skills: PLC, MQTT, Security, Core Exam

Gemini is right that a SCADA applicant should not be Ignition-only. These skills sit **beside** Project 1. They do not replace [build-order.md](build-order.md).

Do **not** pause Part 2 to become a PLC programmer, MQTT expert, or security engineer.

| Skill | Depth to aim for | When | Do not |
|---|---|---|---|
| PLC / hardware layer | **Literacy**, not expert | Light parallel with Parts 2–4 | Advanced ladder, safety logic, loop tuning (out of scope for this repo) |
| MQTT | **Working literacy**, then depth | After Part 5 (OPC UA exists) or after Core | “Master MQTT” before history and alarms exist |
| Industrial security | **OT awareness** (Purdue, zones, least privilege) | Part 7 plus a short 62443 intro | CISSP-first, or exposing the Gateway to the internet |
| Ignition Core exam | **Pass the official exam** | IU Core path in parallel with Parts 2–4; sit the exam after IU courses + enough Designer hours | Skip IU because an assistant explained clicks |

Interview cards: [interview-concepts-adjacent-skills.md](interview-concepts-adjacent-skills.md). Do not claim these until you have actually studied or built them.

---

## 1. PLC and the hardware layer

**Goal:** Explain how a pump measurement gets to a tag, and why the HMI does not run the motor.

Learn:

- What a PLC is vs a sensor vs an RTU
- Scan cycle (read inputs → logic → write outputs)
- Digital vs analog I/O
- A tag path maps to a PLC address or OPC UA node; it is not the physical bearing
- Interlocks and start/stop live in the PLC; SCADA supervises

Enough: a short PLC fundamentals course or CODESYS/vendor starter, plus one evening of “how 4–20 mA and Ethernet/IP show up in Ignition.” Not a controls-engineer career detour.

**Interview:** *I am not a PLC programmer. I know the scan cycle and that control stays in the controller so I can integrate and troubleshoot tags.*

---

## 2. MQTT

**Goal:** Understand pub/sub vs polling (OPC UA), topics, QoS, and why Sparkplug B shows up in IIoT and Ignition.

“Master MQTT” is a later IIoT skill. For energy SCADA interviews, **literacy plus one hands-on broker lab** beats a certificate you cannot connect to a tag.

Sequence:

1. Finish Parts 2–5 so you already know tags, quality, and a device connection.
2. Then: MQTT basics, Sparkplug B overview, how Ignition’s MQTT modules differ from OPC UA.
3. Optional small lab: publish a simulated temperature to a local broker and into a tag. Keep it out of the Part 1–8 happy path until the portfolio is packaged if it would delay Part 8.

Maker Edition may not include every MQTT module. Check what your license actually has before promising Sparkplug in the pipeline demo.

**Interview until you have a lab:** *I understand MQTT as an IIoT transport. This portfolio uses simulated tags and later OPC UA. I have not claimed a production MQTT backbone.*

---

## 3. Industrial security

**Goal:** Talk like someone who will not put a plant HMI on the public internet.

Learn:

- Purdue / zone-and-conduit idea (Level 0–1 field, Level 2 control, Level 3 operations, IT above)
- Least privilege: viewer vs operator vs admin (this repo’s Part 7)
- Secrets, default passwords, and not committing plant credentials
- IEC 62443 as **awareness** (zones, risk, not a full cert)
- AI and .NET never acknowledge alarms or command equipment (already a Project 1 rule)

Part 7 of this lab **is** the security demonstration: containment, roles, runbook. A weekend of 62443/Purdue reading is enough until a job requires more.

**Interview:** *I treat OT security as architecture and access, not as a bolt-on firewall checkbox. Core monitoring stays up when optional services fail. I do not expose this Gateway as a public app.*

---

## 4. Ignition Core exam

**Goal:** Pass Inductive Automation **Core Certification**.

The curriculum is [Inductive University](https://inductiveuniversity.com/) Core, not this repository and not an assistant. See [how-i-learned-ignition.md](how-i-learned-ignition.md).

Suggested timing:

| Phase | What |
|---|---|
| Now through Part 4 | IU Core videos **for the topic you are building** (historian, alarms, UDTs) plus a weekly Core block |
| After Part 4 or during [Project 2 Design Challenge](project-2-ignition-design-challenge.md) | Finish remaining Core courses; the challenge is extra Designer reps |
| When IU Core path is complete and you can do the clicks without a script | Sit the exam |

Core includes Designer/Gateway fundamentals. Vision appears in some Core material; Maker has no Vision. Learn the concept from IU; do not fail the portfolio trying to install Vision on Maker.

After you pass, put the credential on the resume **and** keep the pipeline project as the proof of SCADA thinking (alarms, failures, .NET boundary). A cert without a project is thinner than a project plus a cert.

---

## Weekly split (once Part 2 is moving)

- **Primary:** Project 1 checklist (this repo).
- **Secondary:** IU Core videos tied to that checklist.
- **Small:** PLC literacy (one short module per week until the list above is covered).
- **Later:** MQTT lab, 62443 awareness, Core exam date, Design Challenge.

If a week has only four hours, spend them on Part 2, not on MQTT.
