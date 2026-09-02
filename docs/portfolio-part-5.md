# Portfolio Part 5: Failure Scenarios

Do not start this until [Part 4](portfolio-part-4.md) is complete.

## Cheat Sheet

- **Done means this:** you can run **stale value**, **communication loss**, and **alarm flood** on demand, each with the same alarms, displays, and history on a second run, plus recovery to normal.
- This is the first part that may add an **OPC UA** simulator. Expression tags cannot honestly show communication loss.
- **Stale** means the number looks fine but the timestamp is old. **Bad quality** is not the same as a high temperature.
- Communication alarms must look different from process alarms.

```text
Normal -> fault scenario -> operator display + alarms + history -> recovery
```

## What

Part 5 makes the lab testable. Interviewers care that you tested failures, not only the happy path.

## Why

A sine wave that always works does not prove you can tell a hot bearing from a dead radio. Energy operations distinguish **process** problems from **data** problems.

## How

Required scenarios (save evidence under `docs/evidence/part-5/`):

| Scenario | Operator should see | Alarm |
|---|---|---|
| Stale / frozen value | Temperature stops moving; age or stale indication | Stale-data alarm, not a new high-temp if the last value was normal |
| Communication loss | Quality bad or comms lost; not a fake 148°F | Communication-lost alarm, distinct from high temperature |
| Alarm flood | Many alarms; highest priority still visible; UI still usable | Flood of process or quality alarms without a frozen Designer |
| Recovery | Values live again; process alarms clear correctly; comms alarm clears | Journal shows the episode |

Also keep the Part 2 high-temperature path working so you have five stories: **normal**, **high temp**, **stale**, **comms loss**, **flood**, then **recovery**.

Implementation notes:

1. Add the Ignition Programmable Device Simulator or an OPC UA source for P-101/P-102 if you need quality and comms. Map bearing temperature off the expression tag when the device is in place.
2. Stale: stop updates or freeze the simulator while leaving the last good number on screen. Show timestamp or stale overlay.
3. Comms loss: disable the device or break the OPC UA connection. Do not leave a green 148°F with Good quality.
4. Flood: drive many alarms (both pumps, extra UDT alarms, or a chatter scenario). Confirm the Alarm Status Table remains usable.
5. Write `docs/evidence/part-5/scenarios.md` with starting state, steps, expected alarms, and recovery for each required scenario. Screenshot each.

## Do Not Build in Part 5

- .NET API
- AI assistant
- Cloud

## Checklist

- [ ] High-temperature scenario still works (from Part 2).
- [ ] Stale or frozen-value scenario with evidence.
- [ ] Communication-loss scenario with evidence.
- [ ] Alarm-flood scenario with evidence.
- [ ] Recovery to normal with evidence.
- [ ] Manual checklist stored in the repo.
- [ ] Fill [interview-concepts-part-5.md](interview-concepts-part-5.md) after it works (quality, stale vs process alarm, containment).

Next is [Part 6](portfolio-part-6.md).
