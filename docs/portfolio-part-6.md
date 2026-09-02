# Portfolio Part 6: .NET Maintenance API

Do not start this until [Part 5](portfolio-part-5.md) is complete.

## Cheat Sheet

- **Done means this:** an authorized user creates a maintenance event through an ASP.NET Core API and sees it on the relevant pump screen. API and database behavior have automated tests.
- .NET owns maintenance records. Ignition displays them. The API must not start, stop, or command pumps, and must not acknowledge alarms.
- Use Postgres (same engine as the historian is fine; keep an **application schema** separate from Ignition’s history tables).

```text
Operator / technician -> ASP.NET Core API -> Postgres -> Ignition pump screen
```

## What

Part 6 is the integration layer energy companies expect from a software engineer on an OT team: a tested API next to SCADA, not business logic stuffed into Designer scripts.

## Why

Your C# strength is the differentiator **after** the SCADA slice exists. If you build .NET before alarms and failures, it looks like a web app with an Ignition screenshot.

## How

1. Create an ASP.NET Core solution under `src/` (name it clearly, for example `PipelineOps.Api`).
2. Application schema: assets, maintenance events, audit fields. Not Ignition’s sqlth tables.
3. Endpoints: health, asset lookup, create maintenance event, get events for a pump.
4. Validation, structured errors, audit records.
5. Tests: unit tests plus Testcontainers Postgres integration tests.
6. From Ignition, HTTP binding or a named query/script that **reads** the API. Show latest maintenance event on `PumpDetail`.
7. If the API is down, the pump temperature and alarms still work. Show “maintenance unavailable,” do not blank the SCADA page.

## Checklist

- [ ] ASP.NET Core solution in the repo.
- [ ] Application-owned Postgres schema.
- [ ] Health check endpoint.
- [ ] Asset lookup endpoint.
- [ ] Maintenance-event create and lookup.
- [ ] Validation and structured errors.
- [ ] Audit records.
- [ ] Ignition displays maintenance info on the pump screen.
- [ ] Unit and integration tests, including Testcontainers.
- [ ] API-down behavior: core SCADA continues.
- [ ] Fill [interview-concepts-part-6.md](interview-concepts-part-6.md) after it works (boundary: SCADA vs application data).

Optional after the vertical integration works: shift notes, procedures.

Next is [Part 7](portfolio-part-7.md).
