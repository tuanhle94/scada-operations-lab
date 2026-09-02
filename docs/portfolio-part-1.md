# Portfolio Part 1: Display One Changing Value

This is the document for the first build session. After the checklist, follow [build-order.md](build-order.md). Do not read the full [portfolio project](portfolio-project.md) before Part 1 works.

## Cheat Sheet

- **Done means this:** a browser session shows Pump P-101 bearing temperature, and the number changes without you typing anything.
- **Create a new Git repository** named `scada-operations-lab`. Do not put Ignition work inside `Tu-Notes`.
- **Install Ignition Maker Edition** for personal, non-commercial use. Skip Quick Start.
- **Simulate with an Expression tag.** Do not add an Open Platform Communications Unified Architecture (OPC UA) device yet.
- **A view is not a screen until it has a page URL.** Create the view, map it to a page, then launch.
- **Stop when the number moves.** History, alarms, Postgres, and .NET are Part 2 and later.

```text
Changing simulated value -> Ignition tag -> Human-Machine Interface (HMI) screen
```

---

## Full Note

## What

Part 1 builds the smallest live Supervisory Control and Data Acquisition (SCADA) path: one simulated measurement, one tag, and one operator screen.

A **tag** is a named live value inside Ignition. It has a path, a data type, a current value, a quality, and a timestamp.

A **binding** is the link from a screen widget to that tag. When the tag changes, the widget updates. You do not write a refresh loop.

**Perspective** is Ignition’s web-based Human-Machine Interface (HMI). Operators open it in a browser.

The first pump is **P-101** at **Station 1**. The first measurement is **bearing temperature** in degrees Fahrenheit.

## Why

The later architecture is a map, not tonight’s work. Six pumps, OPC UA, Postgres, and .NET hide the only thing that matters first: can you see a live value?

This part also teaches two words you will reuse everywhere: tag and binding.

## When

Use this document for the first study session. If install takes the whole evening, stop after Ignition opens. Continue the screen on the next session.

Move to Part 2 only after the checklist at the bottom is complete.

## How

Use this loop if you get stuck:

```text
Build one step
    -> encounter a specific gap
    -> read the relevant documentation or watch one targeted video
    -> apply it immediately
    -> confirm the value is changing
    -> record what was learned
```

Do not watch a complete Ignition course first.

### Session Plan

| Block | Time | Stop when |
|---|---|---|
| 1. Git repository and README | 15 min | The repo exists and the problem statement is committed |
| 2. Install and activate Ignition | 30–60 min | The Gateway opens at `http://localhost:8088` and Maker is licensed |
| 3. Project, tag, and screen | 45–90 min | The displayed temperature is changing |

---

## Block 1: Git Repository and Problem Statement

Create a separate repository. Suggested path:

```text
C:\Users\Hang Minh Nguyen\source\repos\scada-operations-lab
```

In PowerShell:

```powershell
cd "C:\Users\Hang Minh Nguyen\source\repos"
mkdir scada-operations-lab
cd scada-operations-lab
git init
```

Create `README.md` with one paragraph:

```markdown
# Pipeline Operations SCADA Lab

This project is a local pipeline-operations Supervisory Control and Data Acquisition (SCADA) application built with Ignition Maker Edition. It monitors simulated pump stations so an operator can see live values, historical trends, and alarms, with a later .NET service for maintenance records. The first working slice is one pump, Pump P-101, showing a live bearing temperature.
```

Do not create the full project folder tree yet. Add folders when a later part needs them.

Commit:

```powershell
git add README.md
git commit -m "Add problem statement for the first SCADA vertical slice."
```

A local repository is enough. GitHub can wait.

Ignition configuration lives on the Gateway, not in this Git repository yet. Exporting a backup into the repo happens in Part 3.

---

## Block 2: Install and Activate Ignition Maker Edition

### Create a free Inductive Automation account

1. Open [https://account.inductiveautomation.com/](https://account.inductiveautomation.com/).
2. Create an account.
3. Open license management and create a Maker license.
4. Copy two values:
   - **License key:** 8 characters
   - **Activation token:** a long string

Maker Edition is free for personal and educational use. It is not for work, sales demos, or a company plant. The limit is about 10,000 tags and 10 concurrent sessions. That is enough.

Official page: [Ignition Maker Edition](https://inductiveautomation.com/ignition/maker-edition)

### Download and install

1. Open [https://inductiveautomation.com/downloads/](https://inductiveautomation.com/downloads/).
2. Download the Windows installer.
3. Run it as Administrator.
4. Keep the default install path and Gateway name unless port 8088 is already used.
5. Choose **Typical** modules.
6. Finish and let the browser open the commissioning page.

Official install guide: [Downloading and Installing Ignition](https://www.docs.inductiveautomation.com/docs/8.3/getting-started/startup-guide/downloading-and-installing-ignition)

### Commission as Maker

1. Choose **Maker Edition**, not Standard, not Edge.
2. Accept the license.
3. Create the first Gateway user. This account is the administrator. Write the username and password down.
4. Keep default ports unless something already uses them:
   - HTTP: `8088`
   - HTTPS: `8043`
5. Enter the license key and activation token.
6. The Gateway needs internet access to activate.
7. When it asks about **Quick Start**, choose **No** / start from scratch.

Quick Start adds a sample plant, historian, and simulator. That is useful for a generic tutorial. It is noise for this portfolio.

### Confirm the Gateway is up

Open [http://localhost:8088](http://localhost:8088) and log in.

If the page does not load:

- Wait one minute. The Windows service may still be starting.
- Check Windows Services for `Ignition`.
- If another app took 8088, commissioning will have chosen a different HTTP port. Use that port instead.

### Install the Designer Launcher

Configure tags and screens in **Designer**, not in the Gateway webpage.

1. On the Gateway home page, download **Designer Launcher**.
2. Install it.
3. Open it and add `http://localhost:8088` if it is not already listed.
4. Launch Designer and log in with the Gateway admin user.

You do not need Perspective Workstation for Part 1. A browser is enough.

---

## Block 3: One Project, One Tag, One Screen

### Create one Perspective project

In Designer:

1. Create a new project.
2. Name it `PipelineOps`.
3. Confirm it is a **Perspective** project. Maker does not include Vision, the older desktop Human-Machine Interface (HMI).

Save immediately with **File → Save** or `Ctrl+S`.

Designer edits live configuration on the Gateway. Saving is what makes the operator session see your work.

### Create the tag folders

In the **Tag Browser**:

1. Find the default tag provider. It is often named `default`.
2. Right-click it → **New Folder** → `Station1`.
3. Right-click `Station1` → **New Folder** → `P101`.

Target path:

```text
[default]Station1/P101/BearingTemperature
```

That folder shape matches later pumps. Do not create those pumps yet.

### Add one simulated bearing-temperature tag

Right-click `P101` → **New Tag** → **Expression Tag**.

Set:

| Property | Value |
|---|---|
| Name | `BearingTemperature` |
| Data Type | Float |
| Value Source | Expression |
| Engineering Units | `°F` |
| Execution Mode | Fixed Rate |
| Execution Rate | `1000` (1 second) |

Expression:

```text
148 + 12 * sin(toMillis(now()) / 8000.0)
```

What that does:

- Centers around **148°F**, a plausible simulated bearing temperature.
- Oscillates about **±12°F**, so the number clearly moves.
- Completes a cycle in several seconds, so you do not wait a minute to see change.

Click **Apply** / **OK**.

In Tag Browser, watch **Value**. It should tick every second. **Quality** should be **Good**.

If the value is stuck:

- Confirm Value Source is **Expression**, not Memory.
- Confirm Execution Mode is **Fixed Rate**, not Event Driven. Event Driven waits for another tag to change. This tag has no other tag.
- Confirm you applied the edit.

Do not add the Programmable Device Simulator or an OPC UA connection in Part 1.

### Create one pump-detail view

In **Project Browser**:

1. Open **Perspective → Views**.
2. Right-click **Views** → **New View**.
3. Name: `PumpDetail`.
4. Root container: **Flex**. Flex is easier than Coordinate when you are new.
5. Check **Page URL** if the dialog offers it, and set `/pump-p101`. If it does not, add the page in the next step.

On the view, keep it readable and ugly:

1. Drag a **Label**. Set text to `Pump P-101`.
2. Drag another **Label**. Set text to `Bearing Temperature`.
3. Drag an **LED Display**, or a Label if you cannot find LED Display. This is the numeric display.
4. Drag a small **Label** next to it. Set text to `°F`.

You are not designing a plant graphic yet. You are proving the binding.

### Bind the numeric display to the tag

1. Select the LED Display or the value Label.
2. In Property Editor, find the value property:
   - LED Display: usually `props.value`
   - Label: `props.text`
3. Click the binding icon next to that property.
4. Choose **Tag**.
5. Choose **Direct**.
6. Browse to `[default]Station1/P101/BearingTemperature`.
7. Leave bidirectional **off**. This is a display, not a setpoint.
8. Optional: add a **Format** transform with `0.0` so you see one decimal.
9. Click OK, then save the project.

A binding is a subscription. You do not poll in a script.

### Give the view a page URL

If launching opens a blank app, the view exists but no page points at it.

1. Open **Perspective → Page Configuration**.
2. Add a page:
   - URL: `/pump-p101`
   - View: `PumpDetail`
3. Set `/pump-p101` as the homepage or default page if Designer lets you.
4. Save.

### Launch a Perspective session

From Designer, use **Launch Perspective** / preview, or open a browser:

```text
http://localhost:8088/data/perspective/client/PipelineOps
```

The exact URL can vary by Ignition version. If that address fails, use the Launch button in Designer and copy the URL it opens.

Log in with the Gateway user if asked.

**Pass condition:** the bearing-temperature number changes by itself within a few seconds.

Watch it for 15–20 seconds. Confirm it is not a frozen screenshot of one value.

---

## If Something Breaks

| Symptom | Likely cause | What to do |
|---|---|---|
| `localhost:8088` will not load | Gateway service not running, or a different port | Start the Ignition Windows service. Check the port from commissioning. |
| Maker activation fails | No internet, or a network that intercepts HTTPS | Activate on a normal home network if a locked-down proxy blocks licensing. |
| Tag value never changes | Memory tag, or Expression tag in Event Driven mode | Use Expression + Fixed Rate + the `sin(now())` expression. |
| Tag quality is Bad | Expression error | Open the tag, check the expression preview, apply again. |
| Screen shows `---` or blank | No binding, or bound to the wrong property | Bind `props.value` (LED) or `props.text` (Label) to the tag. |
| Browser opens an empty app | View has no page URL | Add `/pump-p101` in Page Configuration. |
| You changed the tag but the session did not | Project not saved | Save in Designer, then refresh the browser. |

If one Ignition click blocks you, watch one targeted Inductive University video for that click. Do not start a full course.

---

## Concepts to Write Down After It Works

Write four short answers for **tag** and four for **binding**:

```text
What is it?
Why does this project need it?
How did I use it?
How can it fail?
```

A tag can fail by going stale, showing bad quality, or disappearing. Part 1 should only show Good quality and a moving number. Part 2 is when you make it fail on purpose.

Do not write historian, alarm, or data-quality notes until those features exist.

---

## Do Not Build in Part 1

- Postgres
- Tag history or a trend
- Alarms
- Open Platform Communications Unified Architecture (OPC UA) simulator
- Second pump
- .NET Application Programming Interface (API)
- Amazon Web Services (AWS)
- Pretty graphics, colors, navigation, or an overview screen

---

## Checklist

- [x] Create a separate Git repository for the project.
- [x] Add a one-paragraph problem statement to the repository README.
- [x] Install and activate Ignition Maker Edition for personal use.
- [x] Create one Ignition Perspective project.
- [x] Create a tag folder for Station 1 and Pump P-101.
- [x] Add one simulated bearing-temperature tag whose value changes over time.
- [x] Create one pump-detail view.
- [x] Bind a numeric display to the bearing-temperature tag.
- [x] Launch a Perspective session and confirm that the displayed value changes.

When every box is checked, stop. Save the Designer project. Next is [Part 2](portfolio-part-2.md): Postgres, history, and one high-temperature alarm.
