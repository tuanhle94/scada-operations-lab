# Interview Concepts: Part 1

Memorize these as spoken answers. Each card is **what it is**, **why this project needs it**, **how you used it**, and **how it can fail**. Do not claim history, alarms, OPC UA, or .NET until those exist.

Say the **Speak this** line first. Use the four answers if they ask you to go deeper.

## Why Part 1 exists

**Speak this:**  
Part 1 proves the smallest live SCADA path before I add history, alarms, or .NET. If I cannot show one trustworthy live value, a bigger architecture would only hide that I never got the operator application working.

**Why I had to do it this way:**

- Energy SCADA roles are judged on tags, bindings, and Designer, not on a custom dashboard that only looks industrial.
- One pump, one measurement, one screen isolates the path: simulated value → tag → HMI. If that path is broken, Postgres and OPC UA will not save it.
- Simulation first. An expression tag stands in for a transmitter so I can prove the HMI without a device. A plant would use OPC UA later.
- Designer is the plant tool. I configure graphics there the way an Ignition shop maintains a Gateway.
- Monitor only. Bearing temperature is a condition-monitoring reading. I do not start, stop, or command the pump.

**What I built:**  
Ignition Maker, project `PipelineOps`, tag `[default]Station1/P101/BearingTemperature`, view `PumpDetail`, page `/pump-p101`. An operator opens the browser and sees a changing °F value.

**If they ask how you learned Ignition:**  
See [how-i-learned-ignition.md](how-i-learned-ignition.md). Do not say an AI walked you through Designer. Say you built a monitoring app in Maker and used Inductive University and the manual when you did not know the click. Core certification, if you want it, is that video curriculum. An assistant does not replace it.

---

## 1. SCADA

**Speak this:**  
Supervisory Control and Data Acquisition is the system operators use to watch live plant data. This lab is supervisory only. It displays a simulated pump measurement. It does not start, stop, or command equipment.

| Prompt | Answer |
|---|---|
| What is it? | Software that acquires field data, shows it to operators, and can later alarm and historize it. Supervisory means the human watches the process. Control of motors and valves usually lives in a PLC or other controller, not in this screen. |
| Why does this project need it? | Energy companies hire people who can build and support the operator application, not only a generic website. The portfolio has to look like that application. |
| How did I use it? | I built the smallest live path: one simulated measurement, one tag, one browser HMI. |
| How can it fail? | The Gateway can be down, the screen can have no page URL, or the value can be stale or bad quality while still looking like a number. |

**If they ask “is this control?”**  
No. The project boundary is monitoring. Bearing temperature is a reading, not a setpoint or a command.

**If they ask whether Part 1 is enough for a SCADA role:**  
No. Part 1 is the live path only. The hireable portfolio is [build-order.md](build-order.md) through the five-minute demo: history and alarms, evidence, two pumps from a UDT, failure scenarios, a .NET maintenance API, and containment.

**If they ask whether I program PLCs:**  
No, and this lab does not try to. A Programmable Logic Controller (PLC) runs the fast, safety-adjacent control: start, stop, interlocks. SCADA supervises that controller. I need PLC literacy—scan cycle, I/O, why the HMI must not replace the PLC—so I can map tags and talk to controls engineers. Advanced ladder or safety logic is a different job.

---

## 2. Tag

**Speak this:**  
A tag is a named live value inside Ignition. It has a path, a data type, a current value, a quality, and a timestamp.

| Prompt | Answer |
|---|---|
| What is it? | The named point the rest of the application reads. Operators do not query a device from the screen. They look at tags. |
| Why does this project need it? | Every later feature hangs off the same point: the display, then history, then the high-temperature alarm. If the tag is wrong, the whole slice is wrong. |
| How did I use it? | Path `[default]Station1/P101/BearingTemperature`. Type Float. Units °F. Folder shape `Station1/P101` so later pumps can follow the same model. |
| How can it fail? | Bad or `Error_Configuration` quality, a stale timestamp, an empty or invalid expression, or the tag disappearing after a rename or unsaved edit. |

**If they ask “what is quality?”**  
Quality is whether the system trusts the value. `Good` means use the number. `Error_Configuration` means the tag is misconfigured and the value is not a real temperature.

**If they ask about engineering limits:**  
Engineering low and high are the expected process range, not the live value. Ignition defaults to 0–100. This tag runs near 148°F, so those defaults are wrong metadata. `No_Clamp` means the value is not forced into that range. I set limits that match a bearing-temperature scale, such as 0–250.

---

## 3. Binding

**Speak this:**  
A binding is a subscription from a screen widget to a tag. When the tag changes, the widget updates. I do not write a refresh loop.

| Prompt | Answer |
|---|---|
| What is it? | The link from Human-Machine Interface (HMI) property to tag. Direct tag binding, one direction, display only. |
| Why does this project need it? | The operator screen is useless if it is a static label. The binding is what makes the HMI live. |
| How did I use it? | Bound the numeric display `props.value` (or `props.text` on a Label) to `[default]Station1/P101/BearingTemperature`. Bidirectional off. Optional format `0.0`. |
| How can it fail? | Bound to the wrong property, wrong tag path, bidirectional left on, or the project not saved so the session still shows the old screen. |

**If they ask “how does the number update?”**
The tag evaluates on a one-second rate. The binding receives the new value. The browser widget changes. There is no client-side timer polling the tag.

**If they ask about Enabled vs Bidirectional:**
Enabled means the binding is allowed to run. If it is off, the tag can still preview in Designer, but the LED stays at 0. Bidirectional would let the screen write back to the tag. This display is monitor-only, so Enabled on, Bidirectional off.

---

## 4. Bearing temperature

**Speak this:**  
Bearing temperature is the first process measurement for Pump P-101. On a real pump it is a condition-monitoring signal for overheating. In this lab it is simulated. It does not control the pump.

| Prompt | Answer |
|---|---|
| What is it? | Shaft-bearing temperature in degrees Fahrenheit. A high reading is how an operator notices lubrication, load, or alignment problems. |
| Why does this project need it? | I needed one believable energy-industry point, not a generic counter. This is the value an operator would actually watch on a pump. |
| How did I use it? | One expression tag centered near 148°F, moving about ±12°F, so the screen change is obvious in a few seconds. |
| How can it fail? | The expression can be invalid, the quality can go bad, or later the value can go stale if the simulator or device stops. A frozen good-looking number is worse than an obvious error if nobody notices. |

**If they ask “why 148°F?”**  
It is a plausible simulated running temperature, not a copied plant setpoint. The important part is that it is engineering-unit data that later can trend and alarm.

---

## 5. Expression tag and simulation

**Speak this:**  
Part 1 does not talk to a real device. An expression tag calculates the temperature every second so I can prove the tag-to-screen path before adding OPC UA.

| Prompt | Answer |
|---|---|
| What is it? | A tag whose value comes from an Ignition expression, not from a PLC or OPC UA item. |
| Why does this project need it? | If I add a device, historian, and graphics at once, I cannot tell which layer broke. Simulation isolates the first path. |
| How did I use it? | Value Source Expression. Fixed Rate 1000 ms. Expression uses `now(0)` and `sin` so the value moves without me typing. |
| How can it fail? | Empty expression, Event Driven mode with nothing to trigger it, or a type error in `sin` / `toMillis` that yields `Error_Configuration`. |

**Working expression (what you actually used):**

```text
148 + 12 * sin(toFloat(toMillis(now(0))) / 8000.0)
```

**If they ask “is this how a plant does it?”**  
No. A plant reads instruments through a controller and a protocol such as OPC UA. The expression is a stand-in so the HMI work is real while the I/O is still fake.

**If they ask why this formula:**  
`148` is the center, `12 * sin(...)` is the visible swing. `now(0)` is the current time without its own poll; the tag’s 1-second rate drives updates. `toMillis` turns that time into a rising number. `toFloat` avoids Ignition’s `Error_Configuration` type error. `/ 8000.0` slows the sine so one cycle takes about 50 seconds.

**If they ask why it does not jump every second:**  
The tag evaluates every second. That is not the same as the temperature jumping a full degree. A sine is flattest at the top and bottom. Near 136°F or 160°F the number can look still for several seconds, then climb or fall faster through the middle. That is expected. Do not change `now(0)` to `now(1000)`. That would poll twice.

---

## 6. Perspective HMI

**Speak this:**  
Perspective is Ignition’s web Human-Machine Interface. A view is only a screen after it has a page URL. Operators open it in a browser.

| Prompt | Answer |
|---|---|
| What is it? | The operator graphics layer. Designer edits live Gateway configuration. Save is what the session sees. |
| Why does this project need it? | SCADA work is judged by whether an operator can see the live value, not by whether a tag exists in a browser tree. |
| How did I use it? | Project `PipelineOps`. View `PumpDetail`. Page `/pump-p101`. Session at the Perspective client URL. |
| How can it fail? | View with no page mapping opens a blank app. Unsaved Designer edits never reach the session. Looking for Views in the browser client also fails. Views are built in Designer. |

**If they ask about Designer vs the browser:**  
Designer is the editor. The browser session is what the operator sees. **Views** live under Project Browser → Perspective → Views. A view is not a URL until Page Configuration maps it. The Web Nav template’s Home / Charts / Alarms pages are starter chrome. They are not the pump screen.

---

## 7. Designer vs code

**Speak this:**  
Ignition is configured, not written as a React app. Tags and expressions are the data layer. Perspective views are JSON resources that Designer edits. Energy companies hire people who can work in Designer on a live Gateway.

| Prompt | Answer |
|---|---|
| What is it? | The supported way to build tags, graphics, and bindings is Ignition Designer. Under the hood, tags and Perspective components serialize to JSON. Gateway scripts can create tags with `system.tag.configure`. There is no supported first-class API to author a Perspective view the way you author a web page. |
| Why does this project need Designer? | Plant HMIs are built and changed in Designer. That is the skill the role tests. Coding the first pump screen would skip the tool operators and controls engineers actually use. |
| How did I use it? | I created the tag, view, and binding in Designer. The “code” so far is the expression on the tag, not a custom frontend. |
| How can it fail? | Hand-editing `view.json` on the Gateway filesystem can work and can also break signatures, Git diffs, and Designer validation. Unsupported scripts that write project files are a maintenance risk. |

**If they ask “could you generate screens in code?”**  
Later, reusable equipment is modeled as User-Defined Types (UDTs) and templates, still in Designer. Tags can be generated in script. Views stay Designer-first. Version control of project resources is a later milestone, not Part 1.

**If they ask how this compares to Android:**  
Android has a visual layout editor, but the source of truth is XML/Compose you type. Ignition’s source of truth for graphics is Designer on the Gateway. I still write expressions, scripts, and later tag-generation code. I do not skip Designer the way I skipped drag-and-drop layouts on Android, because that is the plant standard and the skill the role tests.

---

## 30-second story

Use this when they ask what you built so far:

> I stood up Ignition Maker Edition and a Perspective project called PipelineOps. I modeled Station 1, Pump P-101, and one tag: bearing temperature. The tag is simulated with an expression so the value changes every second. I bound that tag to a pump-detail view, mapped it to `/pump-p101`, and launched it in a browser. That is the core SCADA path: a named live value with quality, and an HMI that subscribes to it. The application monitors. It does not control the pump.

---

## Words to keep precise

| Say | Do not say |
|---|---|
| Supervisory / monitor | “I control the pipeline” |
| Simulated expression tag | “I connected to field devices” (not yet) |
| Quality Good or Error_Configuration | “The number is always right” |
| Binding / subscription | “The page polls every second” |
| Built in Designer | “I coded the HMI in React / HTML” |
| Bearing temperature in °F | “A random demo metric” |
| Part 1 is one live value | “I already have alarms, history, and .NET” |
