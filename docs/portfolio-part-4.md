# Portfolio Part 4: Two Pumps from a UDT, Overview, Navigation

Do not start this until [Part 3](portfolio-part-3.md) is complete.

## Cheat Sheet

- **Done means this:** an operator can open an overview, see at least P-101 and P-102, go to the affected pump, see its temperature (and alarm state), and a change to the User-Defined Type (UDT) reaches both pumps without copying tags by hand.
- A **UDT** is a reusable equipment model: tags, units, alarms, history settings.
- Do not add OPC UA, alarm flood, or .NET yet. Optional extra measurements on the UDT are fine if they stay simulated.

```text
UDT definition -> P-101 instance + P-102 instance -> overview -> pump detail
```

## What

Part 4 turns one hand-built pump into a **reusable operator application**.

## Why

Plants do not redraw every pump. Interviewers ask whether a tag and alarm change can be made once. That is the UDT. Navigation proves it is an application, not one orphan view.

## How

### Session plan

| Block | Stop when |
|---|---|
| 1. Pump UDT definition | UDT `Pump` exists with `BearingTemperature` (history + high alarm) |
| 2. Convert / create P-101 instance | `/pump-p101` still shows a live value from the instance |
| 3. Create P-102 | Second instance exists without hand-copying tags |
| 4. Parameterize PumpDetail | One view works for both pumps |
| 5. Overview + navigation | Operator path: overview → pump → back |
| 6. Prove inheritance | One UDT change appears on both instances |

If Docker was off: `docker compose up -d`, confirm Gateway `PipelineOps` is **Valid**.

### Block 1: Create UDT `Pump` (Designer)

1. Open Designer → project **PipelineOps**.
2. Open **Tag Browser** *(View → Panels → Tag Browser if hidden)*.
3. At the bottom of Tag Browser, click the **UDT Definitions** tab  
   *(next to **Tags** — not the Tags tree)*.
4. Click the **+** (Add) icon.
5. Click **New Data Type**.
6. Set **Name** to `Pump`.
7. Click **OK** / create so the UDT opens in the Tag Editor (wording varies).
8. Inside the UDT, add a member tag:
   - Click **+** / **New Standard Tag** (or equivalent inside the data type).
   - **Name:** `BearingTemperature`
   - **Data Type:** Float
   - **Value Source:** Expression (same simulation approach as Part 1)
   - **Expression** (default for the type; instances can override later):

```text
148 + 12 * sin(toFloat(toMillis(now(0))) / 8000.0)
```

   - **Execution Mode:** Fixed Rate, **1000** ms
   - **Engineering Units:** °F
   - **Engineering Low / High:** `0` / `250`
9. On that member, open **History**:
   - **History Enabled:** true
   - **Storage Provider:** your SQL Historian (`PipelineOpsHistory` or whatever you named it — not Sample SQLite)
10. On that member, open **Alarms** → add `HighBearingTemp`:
    - Mode: **Above Setpoint**
    - Setpoint: `155`
    - Deadband: `2`
    - Priority: **High**
    - Display path / message: `P-101 bearing temperature high` is fine for now; you can parameterize display path later
    - **Ack Mode:** Manual
    - **Ack Notes Required:** true
    - Confirm **Alarm Eval Enabled** on the member/tag as needed
11. Click **OK** to save the UDT definition.
12. Press **Ctrl+S**.

You should see `Pump` under **UDT Definitions**. Do not delete the live `Station1/P101` tags yet.

### Block 2: P-101 as a UDT instance

Goal: `/pump-p101` keeps working, now reading an instance member.

**Safer path (recommended):**

1. Click the **Tags** tab in Tag Browser.
2. Expand **Station1**.
3. Note the existing folder/tags for **P101** (hand-built from Part 1).
4. Right-click **Station1** → **New Tag** → **Data Type Instance** → **Pump**  
   *(or Add → Data Type Instance → Pump)*.
5. Name the instance temporarily `P101_udt` (or create it under a temp folder) **or** rename after cutover — pick one approach and stick to it:
   - **Option A:** Rename old `P101` to `P101_old`, create instance `P101`, retarget the view bindings to `[default]Station1/P101/BearingTemperature`, confirm live, then delete `P101_old`.
   - **Option B:** Create `P102` first to learn instances, then convert P-101 once you are confident.
6. After the instance exists at `Station1/P101`, open view **PumpDetail**.
7. Confirm LED / chart pens still point at `[default]Station1/P101/BearingTemperature`.
8. **Ctrl+S**, launch `/pump-p101`, confirm the number moves and Quality is Good.

If the path broke, fix the binding before deleting anything.

### Block 3: Create P-102

1. **Tags** tab → right-click **Station1**.
2. **New Tag** → **Data Type Instance** → **Pump**.
3. **Name:** `P102`.
4. Optionally override `BearingTemperature` expression so it is visibly different, for example:

```text
148 + 12 * sin(toFloat(toMillis(now(0))) / 8000.0 + 2.0)
```

5. **OK**, **Ctrl+S**.
6. Expand `Station1/P102` — you should see `BearingTemperature` inherited (history + alarm from the UDT).

### Block 4: One parameterized PumpDetail

Prefer **one view**, not a copy.

1. Open view **PumpDetail**.
2. Select the **view** root (not a component).
3. In Property Editor, find **PARAMS** (or add a view parameter).
4. Add parameter `tagPath` (String) with default  
   `[default]Station1/P101/BearingTemperature`  
   **or** parameter `pumpPath` = `[default]Station1/P101` and build member paths from it.
5. Rebind the LED `props.value` to an **Indirect Tag** binding using that parameter  
   *(chain-link → Tag → Indirect, or expression that uses `{view.params.tagPath}`)*.
6. Point the Power Chart pen `data.source` at the same parameterized path.
7. **Ctrl+S**.
8. Page Configuration: keep `/pump-p101`. Add `/pump-p102` that uses the **same** view `PumpDetail` but passes the P-102 tag path as a page/view parameter  
   *(exact UI: page params / view params — set P-102’s path on the `/pump-p102` page)*.

If page parameters feel hard in 8.3, temporary fallback: Embedded View on overview that passes `tagPath`. Still one detail view.

### Block 5: Overview + navigation

1. **Project Browser** → right-click **Views** → **New View**.
2. **Name:** `Overview`, layout **Flex**, **Create**.
3. Add two labels or numeric displays:
   - P-101 temperature → `[default]Station1/P101/BearingTemperature`
   - P-102 temperature → `[default]Station1/P102/BearingTemperature`
4. Add two **Button** components: **P-101** and **P-102**.
5. On each button, add an event → **Navigation** / `system.perspective.navigate` to `/pump-p101` or `/pump-p102`  
   *(Perspective Components: select button → Events → onClick → Navigation)*.
6. On **PumpDetail**, add a **Back** button that navigates to `/overview` (or `/` if that is the overview URL).
7. **Page Configuration**: map **Overview** to `/overview` (or `/` as primary home).
8. **Ctrl+S**.
9. Launch `/overview` → click P-102 → confirm detail → Back.

Template HOME/CHARTS/ALARMS can stay; operator path is what matters.

### Block 6: Prove the UDT

1. Open **UDT Definitions** → double-click **Pump**.
2. Edit `BearingTemperature` alarm: change Display Path message slightly, or change documentation, or change deadband from `2` to `3`.
3. **OK**, **Ctrl+S**.
4. Inspect both instances in Tag Browser — inherited members should show the change (unless an instance override blocks it).
5. Say out loud: one edit, two pumps.

## Do Not Build in Part 4

- OPC UA device
- Alarm flood / communication-loss scenarios
- .NET API
- Six pumps (optional only after two pumps work)

## Checklist

- [ ] Define tag naming: `Station{n}/P{id}/...`
- [ ] Create reusable pump UDT with temperature, units, history, and high alarm.
- [ ] Recreate or convert P-101 from the UDT and confirm the existing screen still works.
- [ ] Create P-102 from the UDT without copying tags manually.
- [ ] Parameterize or reuse PumpDetail for both pumps.
- [ ] Create an overview that shows both pumps.
- [ ] Add navigation between overview and pump detail.
- [ ] Change the UDT once and confirm both pumps update.
- [ ] Fill [interview-concepts-part-4.md](interview-concepts-part-4.md) after it works (UDT, instances, navigation).

Next is [Part 5](portfolio-part-5.md).
