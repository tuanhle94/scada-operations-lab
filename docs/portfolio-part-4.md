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

1. In Tag Browser, open **UDT Definitions** (next to the Tags tab).
2. Create UDT `Pump` with at least:
   - `BearingTemperature` (Float, °F, history, high alarm at 155 with deadband 2)
   - Parameters for station and pump id if the version supports them
3. Replace (or recreate) `Station1/P101` as an instance of `Pump`. Confirm `/pump-p101` still works.
4. Create instance `Station1/P102` from the same UDT.
5. Create view `PumpDetail` that can show either pump (view parameter for tag path), or a second page `/pump-p102` that reuses the same view. Prefer **one parameterized view**.
6. Create view `Overview` with page URL `/` or `/overview`: two pumps, live temperature, highest alarm indication.
7. Wire navigation: overview → pump detail → back. Replace or ignore template HOME/CHARTS if they fight this. The operator path matters, not the Web Nav sample pages.
8. Change one UDT alarm setpoint or documentation field. Confirm both instances pick it up.
9. Launch and walk: overview → P-102 → trend/alarm if bound → back.

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
