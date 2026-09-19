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

### Recommended order after reviewing the repository

Parts 1–3 are marked complete in `build-order.md` and their session checklists. The large `portfolio-project.md` contains older unchecked items; use the session checklists for current progress.

**First session:** create `Pump`, then create and verify `P102` using Block 3 before changing P-101. Stop when P-102 has Good quality, changing temperature, history, and its own identifiable high alarm. Keep the existing P-101 screen working during this session.

**Second session:** take a fresh Gateway backup using Part 3 Block 2, convert P-101 using Block 2 below, and parameterize the detail view. **Third session:** build overview/navigation, prove inheritance, and save evidence. These are stopping points, not deadlines.

Keep naming consistent: tag instances `Station1/P101` and `Station1/P102`; operator labels P-101 and P-102. Use one input parameter named `pumpId` in `PumpDetail` (`P101` or `P102`). The live tag path and chart history source both depend on that identity, but are separate bindings.

### Session plan

| Block | Stop when |
|---|---|
| 1. Pump UDT definition | UDT `Pump` exists with `BearingTemperature` (history + high alarm) |
| 3. Create P-102 first | Second pump works without changing P-101 |
| 2. Convert / create P-101 instance | `/pump-p101` still shows a live value from the instance |
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
    - Display Path: leave blank initially so the alarm identifies its actual tag instance. Do not put `P-101` in the shared definition. A parameterized display label can come later.
    - **Ack Mode:** Manual
    - **Ack Notes Required:** true
    - Confirm **Alarm Eval Enabled** on the member/tag as needed
11. Click **OK** to save the UDT definition.
12. Press **Ctrl+S**.

You should see `Pump` under **UDT Definitions**. Do not delete the live `Station1/P101` tags yet.

### Block 2: P-101 as a UDT instance

Goal: `/pump-p101` keeps working, now reading an instance member.

**Do this only after P-102 passes Block 3 and you have a fresh backup.**

1. Click the **Tags** tab in Tag Browser.
2. Expand **Station1**.
3. Right-click the existing **P101** folder and choose **Rename**.
4. Enter `P101_old`. Expect the existing screen to lose its live source briefly during this lab cutover.
5. Right-click **Station1** → **New Tag** → **Data Type Instance** → **Pump**, then name the instance `P101` and confirm **OK**. *(Some Designer builds expose this through Add → Data Type Instance.)*
6. After the instance exists at `Station1/P101`, open view **PumpDetail**.
7. Confirm the LED reads `[default]Station1/P101/BearingTemperature`. Check the chart's existing historical source separately; do not replace it blindly with the live tag path.
8. **Ctrl+S**, launch `/pump-p101`, confirm the number moves and Quality is Good.

If the path broke, fix the binding before deleting anything.

Retain `P101_old` until the new instance passes live-value, trend, and alarm checks. Disable the old member's tag **Enabled** property during validation so it does not keep generating duplicate simulated alarms/history. For rollback, move the failed new instance away from `P101`, rename `P101_old` back, and re-enable the old member. Treat renaming as a history-continuity check: verify both pre-cutover and new samples; do not assume a matching live path guarantees the old trend survived unchanged.

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

Prefer **one view**, not a copy. Use `pumpId` consistently.

1. Open view **PumpDetail**.
2. Select the **view** root (not a component).
3. In Property Editor, add a String **PARAMS** property named `pumpId`, with input direction and default `P101`.
4. Click the LED on the canvas.
5. Click the chain-link binding icon beside `props.value` in Property Editor.
6. Choose **Tag** binding and **Indirect** mode.
7. Enter `[default]Station1/{1}/BearingTemperature` as the tag path.
8. Set reference `1` to property `view.params.pumpId` using the property picker.
9. Leave **Bidirectional** off.
10. Click **OK**. The preview should show P-101's temperature.

Apply the same identity to the pump heading. For the Power Chart, first record the working `props.pens[0].data.source`. Browse P-102 in the chart's **Browse Tags** panel to obtain its actual historical source. Then bind the pen source to an expression selecting the recorded P-101 or P-102 source from `{view.params.pumpId}`. Preserve the historian/driver prefix and the actual path casing. **Do not use a live Tag binding on the pen source**: that would supply a temperature value where the chart needs a history-path string. See the [Power Chart manual](https://www.docs.inductiveautomation.com/docs/8.3/appendix/components/perspective-components/perspective-chart-palette/perspective-power-chart).

For page navigation:

1. Click the **Perspective** icon at the bottom of Designer to open **Page Configuration**.
2. Click **+** under the page list.
3. Enter `/pump/:pumpId` as **Page URL**.
4. Select `PumpDetail` as **Primary View**.
5. Press **Ctrl+S**.
6. Open `/pump/P102` in the Perspective client. Confirm the heading, LED, and trend all identify P-102.
7. Open `/pump/P101`. Confirm all three switch to P-101.

Keep the original `/pump-p101` mapping to `PumpDetail`; its default `pumpId=P101` preserves the Part 3 demo link. Use `/pump/P101` and `/pump/P102` for new navigation. URL parameter names must match view input names; pass the pump ID, not a slash-containing tag path. See [Passing Parameters to Pages](https://www.docs.inductiveautomation.com/docs/8.3/ignition-modules/perspective/pages-in-perspective#passing-parameters-to-pages).

If the UI differs from these labels, check the installed 8.3 build against the linked manual before changing the design.

### Block 5: Overview + navigation

1. **Project Browser** → right-click **Views** → **New View**.
2. **Name:** `Overview`, layout **Flex**, **Create**.
3. Add two labels or numeric displays:
   - P-101 temperature → `[default]Station1/P101/BearingTemperature`
   - P-102 temperature → `[default]Station1/P102/BearingTemperature`
4. Add two **Button** components: **P-101** and **P-102**.
5. On each button, add an event → **Navigation** / `system.perspective.navigate` to `/pump/P101` or `/pump/P102`
   *(Perspective Components: select button → Events → onClick → Navigation)*.
6. On **PumpDetail**, add a **Back** button that navigates to `/overview` (or `/` if that is the overview URL).
7. **Page Configuration**: map **Overview** to `/overview` (or `/` as primary home).
8. **Ctrl+S**.
9. Launch `/overview` → click P-102 → confirm detail → Back.

Template HOME/CHARTS/ALARMS can stay; operator path is what matters.

Include an Alarm Status Table on the overview so the affected pump is identifiable by alarm source, plus navigation to the existing `/alarms-status` and `/alarms-journal` pages. Verify P-102's alarm never appears labeled P-101. On pump detail, show alarm state for the selected pump using an Alarm Status Table filtered to its source; do not infer full alarm state from `temperature > 155`. The alarm engine also tracks deadband and acknowledgment. See [Alarms in UDTs](https://www.docs.inductiveautomation.com/docs/8.3/platform/alarming/configuring-alarms/alarms-in-udts).

### Block 6: Prove the UDT

1. Open **UDT Definitions** → double-click **Pump**.
2. Edit the `BearingTemperature` **Documentation** property to `Part 4 inheritance check`. Use metadata for this proof so the tested alarm thresholds remain unchanged.
3. **OK**, **Ctrl+S**.
4. Inspect both instances in Tag Browser — inherited members should show the change (unless an instance override blocks it).
5. Say out loud: one edit, two pumps.

## Verification and evidence

Before marking this part complete, confirm both pumps have Good quality, changing live values, new history, distinct alarm identities, and working overview → detail → back navigation. Open each detail URL directly and refresh it; the pump identity must survive refresh. Check that the LED, heading, trend, and alarm source all agree.

For each pump, repeat Part 2's high-temperature → operator acknowledgment with comment → clear → journal check. Restore simulation expressions afterward and remove temporary test overrides. Confirm both instances inherit the Documentation change and that no unintended alarm/history overrides block future UDT edits.

Save screenshots of the overview, both detail pages, and inheritance proof under `docs/evidence/part-4/` when these exist. Export a new Gateway backup without overwriting Part 3. Record actual results in the interview cards; these notes alone do not complete the implementation.

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
- [ ] Verify both pumps' trend and alarm lifecycle, including correct pump identity.
- [ ] Save Part 4 screenshots and a fresh Gateway backup.
- [ ] Update [interview-concepts-part-4.md](interview-concepts-part-4.md) while learning; fill actual-use claims only after verification.

Next is [Part 5](portfolio-part-5.md).
