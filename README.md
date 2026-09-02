# Pipeline Operations SCADA Lab

This project demonstrates a small pipeline-operations Supervisory Control and Data Acquisition (SCADA) application for monitoring simulated pump equipment. Equipment control stays outside the application.

## Current Status

Part 1 is complete: a Perspective session at `/pump-p101` shows Pump P-101 bearing temperature changing without operator input.

**Next:** [Part 2](docs/portfolio-part-2.md) — Postgres, trend, high-temperature alarm, acknowledge, clear, journal.

## Build order

Follow [docs/build-order.md](docs/build-order.md). Do not skip ahead.

| Part | Outcome |
|---|---|
| 1 | Live bearing temperature in the browser |
| 2 | History, trend, alarm lifecycle |
| 3 | Screenshots, Gateway backup, one written problem |
| 4 | Pump UDT, P-101 and P-102, overview, navigation |
| 5 | Stale, communication loss, alarm flood |
| 6 | .NET maintenance API on the pump screen |
| 7 | Optional-service failures contained |
| 8 | Five-minute demo and interview packaging |