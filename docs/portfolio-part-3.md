# Portfolio Part 3: Preserve Evidence

Do not start this until [Part 2](portfolio-part-2.md) is complete.

## Cheat Sheet

- **Done means this:** a reviewer can see screenshots of the live value, trend, and alarm lifecycle, restore or inspect a Gateway backup, read one real problem you solved, and find a Git commit for the vertical slice.
- Ignition configuration still lives on the Gateway. The backup file is what goes in Git (or a documented export folder).
- Do not start a UDT, second pump, or .NET in this part.

## What

Part 3 packages Milestone 0 evidence: value, history, and alarm lifecycle.

## Why

Interviewers cannot log into your laptop. Screenshots, a backup, and a written failure are the portfolio. A working Designer project that exists only on one Gateway is not inspectable.

## How

Create folder `docs/evidence/part-3/` and save images there.

Required screenshots:

1. Tag Browser: `BearingTemperature` with Quality **Good** and a live value.
2. Perspective `/pump-p101`: changing LED.
3. The same page with the **trend** showing a curve, not an empty chart.
4. Alarm Status: alarm **active**, unacknowledged.
5. Alarm Status: alarm **acknowledged** (comment visible if the table shows it).
6. Alarm Journal: activate, ack, and clear rows.

Gateway backup:

1. In the Gateway webpage, export or back up the `PipelineOps` project (and Gateway backup if the version offers a full `.gwbk`).
2. Store the file under `backups/` (create the folder). If the file is large, store it locally and put a short `backups/README.md` that says where it lives and how to restore.
3. Do not commit secrets you did not intend to share. Local lab Postgres passwords in docker-compose are expected.

Write `docs/evidence/part-3/problem-solved.md` with one real problem from Part 1 or 2. Use a problem you actually hit, for example:

- `Error_Configuration` on the expression tag
- Binding **Enabled** left off
- View with no page URL
- Engineering limits 0–100 on a 148°F tag

Format:

```text
What broke:
What I thought it was:
What it actually was:
How I confirmed the fix:
```

Update the repo [README.md](../README.md) **Current Status** to state that the first vertical slice (value, trend, alarm lifecycle) works.

Commit the docs, screenshots, compose file, and backup or backup instructions.

## Checklist

- [ ] Save screenshots of the changing value, trend, and alarm views.
- [ ] Export or back up the Ignition project.
- [ ] Add a short README section describing what works.
- [ ] Record one problem encountered and how it was solved.
- [ ] Commit the complete vertical slice.

When every box is checked, the first vertical slice is packaged. Next is [Part 4](portfolio-part-4.md).
