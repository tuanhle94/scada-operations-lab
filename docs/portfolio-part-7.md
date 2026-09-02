# Portfolio Part 7: Reliability and Containment

Do not start this until [Part 6](portfolio-part-6.md) is complete.

## Cheat Sheet

- **Done means this:** a support engineer can tell which component failed, follow a recovery step, and see that **core monitoring and alarming stayed up** when Postgres (application), the .NET API, or the simulator failed in a controlled test.
- This part is in the original project plan. Skip it and the demo looks like a happy-path lab.

## What

Instrument and test **failure containment**. Optional services must not take down the operator’s live values and alarms.

## Why

OT interviews ask “what happens when X is down?” The correct answer is demonstrated, not claimed.

## How

1. Add structured logs and correlation identifiers on the API. Add OpenTelemetry traces across API and Postgres if time allows.
2. Test: stop the .NET API. Confirm `/pump-p101` temperature and alarms still work. Maintenance panel shows unavailable.
3. Test: stop application Postgres (or block the API’s database). Same containment. Note historian impact separately if history shares that instance; if they share one Postgres, document that as a known limitation or split databases.
4. Test: simulator or OPC UA stop. Stale/bad quality and comms alarm from Part 5 still apply.
5. Write `docs/runbook.md`: how to start Docker, Gateway, API; how to restore the Ignition backup; who to blame first (tag quality vs API vs database).
6. Add role notes: viewer vs operator vs admin. Operator may ack alarms; API does not.

## Checklist

- [ ] Logs and correlation ids on the API.
- [ ] OpenTelemetry or equivalent traces for the API path.
- [ ] .NET API unavailability test with evidence.
- [ ] Database unavailability test with evidence and a written limitation if historian shares that database.
- [ ] Core SCADA survives optional-service failure.
- [ ] Backup/restore instructions.
- [ ] Support runbook.
- [ ] Fill [interview-concepts-part-7.md](interview-concepts-part-7.md) after it works (containment).

Next is [Part 8](portfolio-part-8.md).
