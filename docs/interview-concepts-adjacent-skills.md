# Interview Concepts: Adjacent Skills

**Do not claim PLC programming, MQTT mastery, 62443 certification, or Ignition Core until you have done that work.** Project 1 Part 1 only proves a live tag.

Full plan: [adjacent-skills.md](adjacent-skills.md).

---

## Why these skills exist

**Speak this:**  
Ignition is the HMI and SCADA application layer. I also need PLC literacy so I understand where control lives, MQTT literacy for IIoT transports, OT security awareness so I do not expose a Gateway, and Core certification as the vendor exam. The pipeline project stays the portfolio. Those topics are adjacent, not a replacement.

---

## 1. PLC literacy

**Speak this:**  
A PLC runs the fast control scan. SCADA tags are a view of that world. I am not a PLC programmer.

| Prompt | Answer |
|---|---|
| What is it? | A controller that reads I/O, runs logic, writes outputs. |
| Why do I need it? | So I do not put start/stop in Perspective and so I can talk to controls engineers about tag quality vs a real fault. |
| How did I use it? | *(Fill after a fundamentals course or lab.)* |
| How can it fail? | Pretending the HMI is the PLC, or claiming ladder expertise I do not have. |

---

## 2. MQTT

**Speak this:**  
MQTT is pub/sub messaging used a lot in IIoT, often with Sparkplug B. This lab is not a production MQTT backbone.

| Prompt | Answer |
|---|---|
| What is it? | A lightweight publish/subscribe protocol. Unlike polling OPC UA, clients publish to topics. |
| Why might a plant use it? | Edge devices, bandwidth, Sparkplug for device identity and rebirth. |
| How did I use it? | *(Fill after a broker lab.)* Until then: *I have not implemented MQTT in PipelineOps.* |
| How can it fail? | Saying I “mastered MQTT” with no broker, no Sparkplug, and no tag. |

---

## 3. Industrial security

**Speak this:**  
OT security is zones, least privilege, and failure containment. It is not the same as locking down a public website.

| Prompt | Answer |
|---|---|
| What is it? | Purdue-style levels, who may ack vs administer, secrets, and keeping optional services from taking down monitoring. |
| Why does this project need it? | A plant HMI on the internet is a bad architecture. Part 7 is containment and roles. |
| How did I use it? | *(Fill after Part 7.)* Maker default users are not a security portfolio by themselves. |
| How can it fail? | Default passwords left in a demo, or AI/API that can ack alarms or command equipment. |

---

## 4. Ignition Core exam

**Speak this:**  
Core is Inductive Automation’s vendor exam. I study it on Inductive University while I build. An assistant is not the curriculum.

| Prompt | Answer |
|---|---|
| What is it? | Official Core Certification covering Gateway, Designer, tags, and platform fundamentals. |
| Why take it? | Energy shops recognize it. It does not replace a project with alarms and failure tests. |
| How did I use it? | *(Fill after you pass.)* Until then: *I am on the IU Core path; I have not passed yet.* |
| How can it fail? | Listing Core on a resume before the exam, or skipping IU because I used an assistant. |

---

## 30-second story (only as each piece becomes true)

> I am building a pipeline monitoring SCADA app in Ignition as the main evidence. In parallel I am getting PLC literacy so I respect the controller boundary, OT security awareness for zones and access, and I am studying for Ignition Core on Inductive University. MQTT is on the list as IIoT literacy after the core SCADA slice, not instead of it.

---

## Words to keep precise

| Say | Do not say |
|---|---|
| PLC literacy / I do not write plant safety logic | “I am a PLC expert” |
| MQTT literacy / Sparkplug as IIoT transport | “I mastered MQTT” (until you have a lab) |
| OT security awareness / Part 7 containment | “I am 62443 certified” (unless you are) |
| Studying IU Core / passed Core on [date] | “An AI certified me” |
