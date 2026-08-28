# Pipeline Operations Supervisory Control and Data Acquisition (SCADA) Application with .NET Integration

## Cheat Sheet

- **Start with one working pump in the first build session.** Create one simulated asset, one operator screen, one historical trend, and one alarm before expanding the architecture.
- **The primary product is a Supervisory Control and Data Acquisition (SCADA) application.** Tags, graphics, alarms, history, reports, troubleshooting, and system integration come before Artificial Intelligence (AI).
- **Use a real SCADA platform.** Build the operator application with Ignition Maker Edition instead of creating a React dashboard that only resembles SCADA.
- **Use .NET as the integration layer.** Keep maintenance records, shift notes, procedures, and external Application Programming Interfaces (APIs) outside the SCADA platform.
- **Package the core portfolio after Milestone 4.** The SCADA application, failure scenarios, .NET integration, tests, and reliability evidence are enough to support applications.
- **Treat AI as optional.** Retrieval-Augmented Generation (RAG) may be added later, but it must remain read-only and must never control equipment, change setpoints, or acknowledge alarms.
- **Test failures, not just normal operation.** Simulate communication loss, stale values, bad data quality, alarm floods, database downtime, duplicate messages, and service restarts.
- **Measure before optimizing.** Record ingestion throughput, query latency, and alarm-processing behavior before adding complexity. Track token usage and evaluation quality only if the optional assistant is built.
- **Build locally first.** Postgres, Ignition, the simulator, and the .NET service should run on one development machine before any Amazon Web Services (AWS) deployment.
- **Preserve evidence.** Save screenshots, test reports, architecture decisions, query plans, benchmarks, and before-and-after measurements for interviews.

---

## Start Here: First Build Session

Do not read this entire document before starting. The detailed architecture sections are references for later milestones.

Your first goal is a complete, narrow Supervisory Control and Data Acquisition (SCADA) path:

```text
Changing simulated value
    -> Ignition tag
    -> Human-Machine Interface screen
    -> historical trend
    -> alarm activation, acknowledgment, clearing, and review
```

### Build-First Learning Rule

Use this loop throughout the project:

```text
Build one step
    -> encounter a specific gap
    -> read the relevant documentation or watch one targeted video
    -> apply it immediately
    -> test normal and abnormal behavior
    -> record what was learned
```

Do not watch a complete Ignition course before building. Use videos when they answer the exact problem blocking the next checklist item.

### Part 1: Display One Changing Value

Follow [portfolio-part-1.md](portfolio-part-1.md). Do not read past this part until the screen shows a changing value.

- [ ] Create a separate Git repository for the project.
- [ ] Add a one-paragraph problem statement to the repository README.
- [ ] Install and activate Ignition Maker Edition for personal use.
- [ ] Create one Ignition Perspective project.
- [ ] Create a tag folder for Station 1 and Pump P-101.
- [ ] Add one simulated bearing-temperature tag whose value changes over time.
- [ ] Create one pump-detail view.
- [ ] Bind a numeric display to the bearing-temperature tag.
- [ ] Launch a Perspective session and confirm that the displayed value changes.

If the available study session ends here, stop with a working screen. Save the project and continue with Part 2 next time.

### Part 2: Add History and One Alarm

- [ ] Start a local Postgres instance.
- [ ] Create and verify the Ignition database connection.
- [ ] Enable history for the bearing-temperature tag.
- [ ] Add a historical trend to the pump-detail view.
- [ ] Confirm that new values appear in the trend.
- [ ] Configure one high-temperature alarm with an understandable threshold.
- [ ] Configure an alarm journal so the event can be reviewed later.
- [ ] Create an alarm view with an Alarm Status Table for active alarms.
- [ ] Add an Alarm Journal Table for historical alarm events.
- [ ] Drive the simulated temperature above the threshold and observe the alarm becoming active.
- [ ] Acknowledge the alarm with a short comment.
- [ ] Return the value below the threshold and confirm that the alarm clears.
- [ ] Confirm that the alarm lifecycle appears in history.

### Part 3: Preserve Evidence

- [ ] Save screenshots of the changing value, trend, and alarm views.
- [ ] Export or back up the Ignition project.
- [ ] Add a short README section describing what works.
- [ ] Record one problem encountered and how it was solved.
- [ ] Commit the complete vertical slice.

### Concepts to Learn Only When Needed

- **Tag:** what it represents, its path, data type, value, quality, and timestamp.
- **Binding:** how a Human-Machine Interface (HMI) component receives a tag value.
- **Historian:** why time-series values are stored and how a trend retrieves them.
- **Alarm lifecycle:** active, acknowledged, cleared, and historical states.
- **Data quality:** why a displayed number is unsafe to trust without knowing whether it is current and valid.

For each concept, write only:

```text
What is it?
Why does this project need it?
How did I use it?
How can it fail?
```

### Do Not Build Yet

- .NET service
- Open Platform Communications Unified Architecture (OPC UA) simulator
- Multiple stations or six pumps
- Redis or a message queue
- Amazon Web Services (AWS) deployment
- Retrieval-Augmented Generation (RAG) assistant
- Advanced performance experiments

Finish the first vertical slice before opening any of those branches.

### Milestone Order

| Order | Outcome | Advance only when |
|---|---|---|
| Milestone 0 | One complete vertical slice | Value, screen, history, and alarm lifecycle work |
| Milestone 1 | Reusable operator application | Two pump instances share a tested model and navigation |
| Milestone 2 | Repeatable failures | Five required scenarios behave consistently |
| Milestone 3 | .NET and Postgres integration | One maintenance workflow works end to end and is tested |
| Milestone 4 | Reliability and supportability | Dependency failures are diagnosable and contained |
| Milestone 5 | Interview packaging | A reviewer can understand and inspect the project |
| Optional extensions | Artificial Intelligence (AI), performance, or cloud work | The core portfolio is already packaged and applications have started |

---

## Full Note

## What

This project is a **local pipeline-operations Supervisory Control and Data Acquisition (SCADA) application** for monitoring simulated pump stations.

The system will:

- Generate realistic equipment measurements.
- Expose those measurements through Open Platform Communications Unified Architecture (OPC UA).
- Use Ignition for operator graphics, tags, alarms, historical trends, and reports.
- Use Postgres for application data and selected historical data.
- Use an ASP.NET Core Application Programming Interface (API) for maintenance records, shift notes, procedures, and integrations.
- Optionally add a read-only knowledge assistant after the core portfolio has been packaged.

The finished project should look like a small industrial application that an operator and a support engineer could actually use. It should not look like a generic chatbot attached to a dashboard.

## Why

The project targets **Supervisory Control and Data Acquisition (SCADA) application developer and Operational Technology (OT) application integration roles**.

It demonstrates the work those positions commonly perform:

- SCADA platform configuration
- Human-Machine Interface (HMI) development
- Alarm configuration and investigation
- Tag history and reporting
- Structured Query Language (SQL) queries and stored procedures
- Scripting and application logic
- Integration between operational and business applications
- Production troubleshooting
- Documentation and change control
- Optional, secure, read-only use of Artificial Intelligence (AI) around operational systems

The project also preserves the user's existing backend strengths in C#, .NET, Postgres, testing, observability, and cloud architecture.

## When

Build the project as the center of the [study plan](study-plan.md) and [career plan](README.md).

Use each project milestone as a practical exercise:

- Study Postgres indexes, then optimize a historical query.
- Study caching, then measure whether the latest-asset view needs a cache.
- Study asynchronous processing, then make document indexing or data export asynchronous.
- Study observability, then trace a request across Ignition, the .NET Application Programming Interface (API), and Postgres.
- If the optional assistant is started, study Retrieval-Augmented Generation (RAG) and create an evaluation dataset before changing chunking or retrieval.

Do not wait until every concept is understood. Start with a small vertical slice and deepen it as each concept becomes relevant.

## Main Interview Statement

> I built a simulated pipeline-operations Supervisory Control and Data Acquisition (SCADA) application using Ignition and Open Platform Communications Unified Architecture. I created reusable equipment models, operator graphics, alarm handling, historical trends, Structured Query Language (SQL) reports, and a .NET integration service for maintenance and shift data. I tested communication loss, stale readings, alarm floods, database downtime, and dependent-service failures while keeping core monitoring independent from optional business services.

Optional extension after it actually exists:

> After the operational system was reliable, I added a read-only knowledge assistant that correlated alarms, historical readings, maintenance events, and operating procedures without giving the language model control authority.

---

## Project Boundaries

### In Scope

The core scope is a Supervisory Control and Data Acquisition (SCADA) application with a supporting ASP.NET Core Application Programming Interface (API).

- Simulated pipeline pump stations
- Ignition Maker Edition
- Open Platform Communications Unified Architecture data
- Reusable asset and tag models
- Human-Machine Interface graphics
- Alarm configuration, acknowledgment, and history
- Historical trends
- Postgres queries and reports
- ASP.NET Core integration API
- Automated scenario and integration tests
- Structured logging and OpenTelemetry
- Performance and failure experiments
- Local deployment documentation

### Optional Extensions

- Read-only Retrieval-Augmented Generation assistant
- Retrieval evaluation dataset
- Advanced performance and cost comparisons
- Read-only cloud export or deployment exercise

### Out of Scope

- Real equipment control
- Production safety logic
- Safety Instrumented System (SIS) logic
- Advanced Programmable Logic Controller (PLC) programming
- Electrical or instrumentation design
- Control-loop tuning
- Real pipeline operating recommendations
- Predictive-maintenance claims
- A production-ready cybersecurity certification
- Kubernetes
- A large microservice architecture
- Cloud deployment before the local system is reliable

The simulator exists to provide realistic inputs to the SCADA application. It is not intended to prove controls-engineering expertise.

---

## Users and Core Workflows

### Operator

The operator needs to:

- See which stations and pumps are running.
- Identify abnormal conditions quickly.
- Review active alarms.
- Acknowledge an alarm with a comment.
- View recent and historical trends.
- Distinguish a process problem from a communication problem.

### Supervisory Control and Data Acquisition Application Support Engineer

The support engineer needs to:

- Diagnose missing or stale values.
- Review alarm and application logs.
- Verify database and service health.
- Run a repeatable failure scenario.
- Confirm that a configuration change did not break existing behavior.
- Restore the application from a documented backup.

### Maintenance Coordinator

The maintenance coordinator needs to:

- Review an asset's maintenance history.
- Add a maintenance event.
- Connect maintenance work to alarms and equipment history.
- Find the applicable operating or troubleshooting procedure.

### Knowledge Assistant User

The assistant user needs to:

- Ask what happened before an alarm.
- Retrieve the relevant procedure.
- See citations for every operational claim.
- Distinguish measured facts from generated interpretation.
- Receive an explicit refusal when the evidence is insufficient.

---

## System Context

The Supervisory Control and Data Acquisition (SCADA) platform receives equipment data through Open Platform Communications Unified Architecture (OPC UA). The ASP.NET Core Application Programming Interface (API) connects operational context to external application data.

```text
+---------------------+
| Equipment Simulator |
| C# process or tags  |
+----------+----------+
           |
           | Open Platform Communications Unified Architecture
           v
+---------------------+       +------------------------+
| Ignition SCADA      |<----->| Postgres               |
|                     |       |                        |
| - Tags              |       | - Alarm journal        |
| - Asset templates   |       | - Application data     |
| - Operator graphics |       | - Maintenance events   |
| - Alarms            |       | - Document metadata    |
| - History/trends    |       +-----------+------------+
| - Reports           |                   ^
+----------+----------+                   |
           |                              |
           | Hypertext Transfer Protocol  |
           v                              |
+---------------------+-------------------+---+
| ASP.NET Core Integration API                |
|                                             |
| - Assets and maintenance                    |
| - Shift notes and procedures                |
| - Alarm investigation records               |
| - Authentication and audit                  |
| - Read-only assistant orchestration         |
+-----------------------+---------------------+
                        |
                        v
              +--------------------+
              | Local or hosted    |
              | language model     |
              +--------------------+
```

## Component Responsibilities

The Supervisory Control and Data Acquisition (SCADA) application owns live operator behavior. Artificial Intelligence (AI) remains outside the control path.

| Component | Owns | Does Not Own |
|---|---|---|
| Equipment simulator | Values, modes, failures, deterministic scenarios | Operator graphics or business records |
| Ignition | Tags, alarm rules, operator graphics, trends, alarm interaction | Maintenance workflow or AI decisions |
| Postgres | Durable application data, journals, metadata, report queries | Live operator behavior |
| ASP.NET Core API | Business records, integrations, authorization, audit, assistant orchestration | Equipment control |
| Knowledge assistant | Retrieval, summarization, citations, read-only correlation | Setpoints, commands, alarm acknowledgment |

This separation makes authority explicit. A language model can explain information, but deterministic application code and authorized users control state changes.

---

## Simulated Domain

### Initial Physical Model

Create three stations:

- Station North
- Station Central
- Station South

Each station has two pumps. Only build one pump during the first vertical slice.

### Pump Tags

Each pump should eventually expose:

| Tag | Example unit | Purpose |
|---|---|---|
| Running | Boolean | Shows whether the pump is running |
| Mode | Local, Remote, Maintenance | Shows who may operate the pump |
| Suction pressure | pounds per square inch | Shows inlet pressure |
| Discharge pressure | pounds per square inch | Shows outlet pressure |
| Flow rate | barrels per hour | Shows product movement |
| Bearing temperature | degrees Fahrenheit | Detects overheating |
| Vibration | millimeters per second | Detects abnormal motion |
| Motor current | amperes | Shows motor load |
| Communication status | Good, Bad, Stale | Separates data failure from process failure |
| Last update | timestamp | Shows freshness |

The values are educational simulations. Document that they are not validated operating limits for real equipment.

### Reusable Models

After the first pump works, create a reusable User-Defined Type (UDT) for pumps.

The reusable model should contain:

- Tag definitions
- Engineering units
- Alarm limits
- History settings
- Display metadata
- Communication status
- Documentation

Use parameters for station name, pump identifier, and simulated data source. Avoid copying independent tag folders for every pump.

---

## Alarm Design

### Initial Alarms

Implement at least:

- High bearing temperature
- High-high bearing temperature
- High discharge pressure
- Low suction pressure
- High vibration
- Pump stopped unexpectedly
- Communication lost
- Stale data

### Alarm Lifecycle

Support and demonstrate:

```text
Normal
  -> Active and unacknowledged
  -> Active and acknowledged
  -> Cleared and acknowledged
```

Also test an alarm that clears before acknowledgment.

### Alarm Requirements

Each alarm needs:

- A meaningful name
- Asset and station context
- Priority
- Activation threshold
- Clear threshold or deadband
- Delay when appropriate
- Operator-facing message
- Acknowledgment state
- Event timestamp
- Source value
- Historical record

### Alarm Philosophy

- Use alarms for conditions requiring operator attention.
- Do not alarm every unusual value.
- Use deadbands and delays to prevent chattering.
- Give communication failures a different presentation from process alarms.
- Preserve alarm history after the value returns to normal.
- Never let the Artificial Intelligence (AI) assistant acknowledge or suppress an alarm.

---

## Human-Machine Interface (HMI) Design

### Required Screens

1. **System overview**
   - All stations
   - Running state
   - Highest active alarm priority
   - Communication health

2. **Station detail**
   - Both pumps
   - Current measurements
   - Navigation to asset details

3. **Pump detail**
   - Current values
   - Operating mode
   - Recent trends
   - Active alarms
   - Maintenance summary

4. **Alarm summary**
   - Active alarms
   - Priority
   - State
   - Acknowledgment
   - Operator comment

5. **Alarm history**
   - Filters by time, station, pump, priority, and state

6. **Trend viewer**
   - Select asset, tag, and time range
   - Compare related measurements

7. **System health**
   - Database connectivity
   - Integration API health
   - Communication status
   - Last successful data update

### Display Rules

- Use color primarily to communicate abnormal state.
- Do not make normal operation visually noisy.
- Display engineering units next to values.
- Display bad or stale data explicitly.
- Do not show a last-known value as if it were current.
- Keep navigation consistent.
- Require confirmation for meaningful state changes.

---

## Data Design

### Supervisory Control and Data Acquisition (SCADA)-Owned Data

Allow Ignition to manage:

- Tag configuration
- Tag history
- Alarm journal
- Alarm acknowledgment information
- SCADA user-interface configuration

Do not build a second competing historian in the ASP.NET Core application.

### Application-Owned Tables

Start with:

```text
assets
- id
- station_code
- asset_code
- display_name
- asset_type
- service_status

maintenance_events
- id
- asset_id
- event_type
- started_at
- completed_at
- summary
- performed_by

shift_notes
- id
- station_code
- asset_id
- created_at
- author
- note_text

procedures
- id
- procedure_code
- title
- revision
- effective_at
- source_path
- content_hash

alarm_investigations
- id
- alarm_event_reference
- asset_id
- opened_at
- closed_at
- summary
- conclusion

audit_events
- id
- occurred_at
- actor
- action
- target_type
- target_id
- result
```

Add document chunks and embeddings only when the Retrieval-Augmented Generation milestone begins.

### Important Query Exercises

- Recent alarms for one asset
- Maintenance events before and after an alarm
- Most frequently alarming assets
- Average acknowledgment time by priority
- Missing-data periods
- Asset trends for a selected time range
- Alarm count by station and day

Capture the query plan before and after adding an index to at least one slow query.

---

## .NET Integration Application Programming Interface (API)

### Responsibilities

The ASP.NET Core API should provide:

- Asset metadata
- Maintenance event creation and lookup
- Shift-note creation and lookup
- Procedure metadata
- Alarm-investigation records
- Audit records
- Read-only correlation endpoints
- Health checks
- Assistant orchestration in the later milestone

### Initial Endpoints

```text
GET  /api/assets
GET  /api/assets/{assetId}
GET  /api/assets/{assetId}/maintenance
POST /api/assets/{assetId}/maintenance
GET  /api/assets/{assetId}/shift-notes
POST /api/assets/{assetId}/shift-notes
GET  /api/procedures
GET  /api/health
```

Add alarm-correlation and assistant endpoints only after the base API is tested.

### API Rules

- Validate all input.
- Use cancellation tokens.
- Return structured errors.
- Add request identifiers to logs.
- Make retryable writes idempotent when appropriate.
- Use least-privilege database credentials.
- Audit meaningful changes.
- Do not expose an equipment-control endpoint.

---

## Testing Strategy

Testing covers the Supervisory Control and Data Acquisition (SCADA) application, the ASP.NET Core Application Programming Interface (API), Structured Query Language (SQL) behavior, and later Retrieval-Augmented Generation (RAG) quality.

### Deterministic Scenario Tests

Create a scenario runner that can produce:

1. Normal startup
2. High temperature
3. High temperature with chattering near the limit
4. High vibration
5. Low suction pressure
6. Unexpected stop
7. Communication loss
8. Frozen value
9. Stale timestamp
10. Bad data quality
11. Alarm flood
12. Recovery to normal operation

Each scenario should define:

- Starting state
- Sequence of input values
- Expected alarm transitions
- Expected historical records
- Expected operator display state
- Recovery behavior

### Automated Tests

Use:

- Unit tests for deterministic simulation and business rules
- ASP.NET Core integration tests for APIs
- Testcontainers with Postgres for database tests
- Contract tests for data exchanged between Ignition and the .NET API
- Scenario tests for end-to-end behavior
- Evaluation tests for RAG

### Manual Supervisory Control and Data Acquisition Verification

Some operator-interface behavior will require a short manual checklist:

- Correct navigation
- Correct color and alarm state
- Correct engineering units
- Correct data-quality display
- Correct acknowledgment behavior
- Readable trend ranges
- Graceful dependency-failure display

Store the checklist in the project repository and run it before recording a demo.

---

## Failure and Recovery Plan

The Supervisory Control and Data Acquisition (SCADA) application is the core monitoring path. The ASP.NET Core Application Programming Interface (API) and language model are optional dependencies whose failures must be contained.

| Failure | Expected behavior |
|---|---|
| Simulator stops | Values become stale or bad; communication alarm activates |
| Postgres is unavailable | Operator monitoring continues where possible; dependent views show a clear failure |
| .NET API is unavailable | Core SCADA display and alarms continue; maintenance features show unavailable |
| Language model times out | Assistant fails without affecting SCADA operation |
| Duplicate maintenance request | Idempotency prevents an accidental duplicate when configured |
| Application restarts | Configuration reloads and historical records remain available |
| Alarm flood | Interface remains usable and highest-priority conditions stay visible |
| Invalid document | Indexing rejects or quarantines it without corrupting the existing index |

The important architectural rule is **failure containment**. Optional integrations must not take down core monitoring and alarming.

---

## Security and Authority Boundaries

Security separates Supervisory Control and Data Acquisition (SCADA) monitoring authority, application-data authority, and Artificial Intelligence (AI) assistance.

### Roles

Use at least:

- Viewer
- Operator
- Maintenance
- Administrator

### Rules

- Viewers cannot acknowledge alarms or change records.
- Operators may acknowledge alarms but may not administer the system.
- Maintenance users may update maintenance records.
- Administrators manage configuration.
- Every meaningful write produces an audit record.
- The assistant is read-only regardless of the signed-in user's role.
- Secrets stay outside source control.
- Postgres, Ignition, and the API use separate credentials.

### Operational Technology (OT) Boundary

Document a conceptual boundary between Operational Technology and Information Technology:

```text
Operational side                    Information side

Simulator -> Ignition -> historian  -> read-only integration -> .NET/API
```

The local lab will not reproduce a production network. The design should still explain why direct, unrestricted cloud-to-control access would be unacceptable.

---

## Read-Only Knowledge Assistant

The knowledge assistant uses Retrieval-Augmented Generation (RAG) without receiving equipment-control authority.

### What

The assistant retrieves and summarizes:

- Procedures
- Shift notes
- Maintenance events
- Alarm history
- Selected historical measurements

It must cite the evidence used for an answer.

### Why

Operators and support engineers often need to correlate information stored in different places. The assistant demonstrates how retrieval can reduce search time without transferring control authority to a language model.

### When

Add this only after:

- The SCADA application is usable.
- Alarm history works.
- The .NET API is tested.
- Procedures and maintenance records exist.
- Failure behavior is documented.

### Initial Questions

- What procedure applies to the high discharge-pressure alarm?
- What alarms occurred before Pump P-101 stopped?
- What maintenance occurred in the previous 24 hours?
- Which measurements support the explanation?
- Is there enough evidence to identify a cause?

### Guardrails

- No control commands
- No alarm acknowledgment
- No setpoint changes
- No suppression or shelving
- No unsupported causal claims
- Citations required
- Explicit uncertainty
- Clear separation between facts and interpretation

### Evaluation Dataset

Create at least 30 questions covering:

- Correct procedure retrieval
- Correct asset filtering
- Correct time-window filtering
- Multiple documents
- Conflicting revisions
- Missing answers
- Misleading assumptions
- Insufficient evidence

Track:

- Expected source in the top retrieved results
- Citation precision
- Citation coverage
- Factual correctness
- Faithfulness to retrieved evidence
- Appropriate refusal
- Latency
- Input and output token usage

Pin the evaluation dataset and record results before changing the model, prompt, embedding model, chunk size, or retrieval settings.

---

## Observability

Instrument the ASP.NET Core Application Programming Interface (API), database operations, retrieval pipeline, and language-model requests with OpenTelemetry.

### Traces

Trace:

- Ignition-to-API requests
- API database queries
- Procedure retrieval
- Language-model requests
- End-to-end assistant requests

### Metrics

Record:

- Simulator readings per second
- Stale and bad-quality tag count
- Active alarms by priority
- Alarm acknowledgment duration
- API request count and latency
- Database query latency
- Assistant latency
- Retrieval duration
- Cache hit rate
- Evaluation score
- Token usage and estimated cost

### Logs

Use structured logs containing:

- Timestamp
- Severity
- Component
- Request or correlation identifier
- Station and asset identifier when relevant
- Operation
- Result
- Failure category

Do not log secrets, full prompts containing sensitive data, or unnecessary personal information.

---

## Performance Experiments

Run experiments only after a working baseline exists.

### Supervisory Control and Data Acquisition (SCADA) and Data Experiments

- One database write per reading versus batched writes
- Query without an index versus query with a composite index
- Raw long-range history versus hourly rollups
- Normal load versus alarm flood
- Current-value query from Postgres versus cached latest value

### Assistant Experiments

- Different chunk sizes
- Different numbers of retrieved chunks
- Metadata filtering by asset and procedure revision
- Vector search versus hybrid search
- Full conversation history versus bounded summary
- Local model versus hosted model
- No cache versus embedding and retrieval caches

### Measurements

Record:

- Hardware and software environment
- Dataset size
- Test duration
- Concurrency
- Median latency
- 95th-percentile latency
- Throughput
- Error rate
- Database connections
- Cache hit rate
- Evaluation quality
- Estimated cost

Never claim production scale from a laptop test. State what was tested, where it was tested, and what remains unknown.

---

## Architecture Decisions to Record

Create short Architecture Decision Records (ADRs) for important Supervisory Control and Data Acquisition (SCADA), backend, and Artificial Intelligence (AI) boundaries:

1. Ignition instead of a custom SCADA-like web interface
2. Local-first deployment
3. Postgres for application data
4. Ignition-managed tag history
5. ASP.NET Core as the external integration layer
6. Read-only AI authority
7. Monolith before microservices
8. No Redis until measurements justify shared caching
9. No message queue until asynchronous buffering has a demonstrated need
10. Optional cloud export rather than cloud control

Each record should state:

```text
Context:
Decision:
Alternatives:
Why:
Trade-off:
Failure risk:
Revisit when:
```

---

## Milestone Plan

Progress is milestone-based rather than calendar-based. Short sessions count.

## Milestone 0: First Vertical Slice

### Goal

Display and historize one changing value in Ignition.

### Tasks

- [ ] Complete the [First Build Session](#start-here-first-build-session) checklist.
- [ ] Confirm that every item in the definition of done below is observable.

### Definition of Done

One screen shows a changing temperature. The value is stored historically. Raising the value activates an alarm that can be acknowledged, cleared, and later reviewed. Screenshots, a backup, and a Git commit preserve the evidence.

Stop after this works. Do not begin the .NET service or assistant on the first day.

## Milestone 1: Reusable Supervisory Control and Data Acquisition Application

### Goal

Expand the vertical slice into a small, consistent Supervisory Control and Data Acquisition (SCADA) operator application.

### Tasks

- [ ] Define tag naming conventions.
- [ ] Add the initial measurement and status tags to Pump P-101.
- [ ] Add engineering units and useful display ranges.
- [ ] Configure alarm priority, deadband, delay, and acknowledgment comments.
- [ ] Display bad and stale data quality explicitly.
- [ ] Create the reusable pump User-Defined Type.
- [ ] Create Pump P-102 from the reusable model and confirm that bindings and alarms work without rebuilding them manually.
- [ ] Create or refine the system overview, pump detail, alarm, trend, and health screens.
- [ ] Add navigation between the overview and both pumps.
- [ ] Journal alarm history to Postgres.
- [ ] Document navigation, naming, color, and display conventions.
- [ ] Optional after two pumps work: expand to six pumps across three stations.

### Definition of Done

An operator can monitor at least two pumps, identify the highest-priority abnormal condition, navigate to the affected pump, inspect trends, acknowledge the alarm, and find the historical alarm record. A change to the reusable model reaches both pump instances.

## Milestone 2: Simulator and Failure Scenarios

### Goal

Make behavior repeatable and testable.

### Tasks

- [ ] Build a deterministic simulator or scenario runner.
- [ ] Add Open Platform Communications Unified Architecture communication.
- [ ] Record data quality and source timestamps.
- [ ] Implement the normal-operation scenario.
- [ ] Implement a high-temperature scenario.
- [ ] Implement a communication-loss scenario.
- [ ] Implement a frozen or stale-value scenario.
- [ ] Implement an alarm-flood scenario.
- [ ] Test alarm deadbands and delays.
- [ ] Test recovery after communication loss.
- [ ] Test application restart.
- [ ] Write a manual verification checklist.
- [ ] Save evidence for the five required scenarios.
- [ ] Optional: add the remaining scenarios from the full scenario catalog.

### Definition of Done

Each of the five required scenarios produces the same values, alarm transitions, historical records, and recovery behavior on repeated runs.

## Milestone 3: .NET and Structured Query Language Integration

### Goal

Connect Supervisory Control and Data Acquisition (SCADA) data with enterprise-style application records through an ASP.NET Core Application Programming Interface (API) and Structured Query Language (SQL) database.

### Tasks

- [ ] Create the ASP.NET Core solution.
- [ ] Create the application-owned Postgres schema.
- [ ] Implement health checks.
- [ ] Implement one asset lookup endpoint.
- [ ] Implement maintenance-event creation and lookup.
- [ ] Add validation and structured errors.
- [ ] Add audit records.
- [ ] Connect Ignition to the API.
- [ ] Display maintenance information on the pump screen.
- [ ] Add unit and integration tests.
- [ ] Add Postgres Testcontainers tests.
- [ ] Optional after the vertical integration works: add shift-note endpoints.
- [ ] Optional after the vertical integration works: add procedure endpoints.

### Definition of Done

An authorized user can create a maintenance event through the .NET API and see it from the relevant pump screen. The API and database behavior are covered by repeatable tests.

## Milestone 4: Reliability and Observability

### Goal

Make failures in the Supervisory Control and Data Acquisition (SCADA) application, ASP.NET Core Application Programming Interface (API), and supporting services diagnosable and contained.

### Tasks

- [ ] Add OpenTelemetry traces and metrics.
- [ ] Add correlation identifiers.
- [ ] Add service-level structured logs.
- [ ] Test Postgres unavailability.
- [ ] Test .NET API unavailability.
- [ ] Confirm core monitoring survives optional-service failures.
- [ ] Add backup and restore instructions.
- [ ] Add a support runbook.
- [ ] Add role-based permissions.
- [ ] Review secrets and database privileges.

### Definition of Done

A support engineer can identify which component failed, see the affected workflow, follow a recovery procedure, and confirm that core monitoring and alarming remained available where designed.

## Milestone 5: Portfolio Packaging

### Goal

Make the work easy for an interviewer to inspect.

Start this milestone after Milestone 4. Do not wait for an optional extension.

### Tasks

- [ ] Create a clear README.
- [ ] Add the final architecture diagram.
- [ ] Add local setup and reset instructions.
- [ ] Add screenshots of every major screen.
- [ ] Add a three-to-five-minute demo video.
- [ ] Publish the scenario-test results.
- [ ] Include the Architecture Decision Records.
- [ ] Document limitations and future work.
- [ ] Remove secrets and private data.
- [ ] Practice a five-minute verbal walkthrough.
- [ ] If completed, publish any optional evaluation or performance report.

### Definition of Done

A reviewer can understand the problem, run the project, inspect the major decisions, see evidence of testing, and hear a concise explanation without needing private context.

## Optional Extension A: Knowledge Assistant and Evaluations

Do not start this extension before the Milestone 5 portfolio is packaged and automation applications have begun.

### Goal

Add safe, measured Retrieval-Augmented Generation (RAG) over operational documents and records.

### Tasks

- [ ] Add procedure ingestion.
- [ ] Add document content hashes and revisions.
- [ ] Add document chunks and embeddings.
- [ ] Implement metadata-filtered retrieval.
- [ ] Add cited answers.
- [ ] Separate facts from interpretation.
- [ ] Enforce read-only tools.
- [ ] Create the 30-question evaluation dataset.
- [ ] Establish a baseline evaluation report.
- [ ] Add regression thresholds.
- [ ] Test missing and conflicting information.
- [ ] Trace assistant requests.

### Definition of Done

The assistant answers the evaluation questions with measured retrieval and citation quality, refuses unsupported questions, and has no path to equipment control or alarm acknowledgment.

## Optional Extension B: Performance and Cost

Do not start this extension before the Milestone 5 portfolio is packaged and a real measurement question exists.

### Goal

Create a measured optimization story.

### Tasks

- [ ] Define normal, burst, and failure workloads.
- [ ] Record the baseline environment and results.
- [ ] Identify the first bottleneck.
- [ ] Optimize one Postgres query.
- [ ] Evaluate batching where appropriate.
- [ ] Evaluate historical rollups.
- [ ] Evaluate caching only after measuring repeated work.
- [ ] Compare assistant context sizes.
- [ ] Compare at least two model configurations.
- [ ] Record quality, latency, and cost trade-offs.
- [ ] Publish a before-and-after report.

### Definition of Done

The project contains at least one honest performance story with a measured baseline, identified bottleneck, implemented change, improved result, and stated trade-off.

---

## Suggested Repository Structure

```text
scada-operations-lab/
|-- README.md
|-- docs/
|   |-- architecture/
|   |-- decisions/
|   |-- operations/
|   |-- performance/
|   `-- evaluations/
|-- ignition/
|   |-- backups/
|   |-- tag-exports/
|   `-- verification-checklists/
|-- simulator/
|   |-- src/
|   `-- tests/
|-- services/
|   `-- Operations.Api/
|-- tests/
|   |-- Operations.Api.Tests/
|   |-- Operations.IntegrationTests/
|   `-- Scenarios/
|-- database/
|   |-- migrations/
|   `-- sample-data/
|-- documents/
|   |-- procedures/
|   |-- maintenance/
|   `-- shift-notes/
`-- deploy/
    `-- local/
```

Do not create every empty folder immediately. Add each folder when its milestone begins.

---

## Portfolio Evidence Checklist

- [ ] Problem statement
- [ ] Architecture diagram
- [ ] Scope and non-goals
- [ ] Tag naming standard
- [ ] Reusable asset model
- [ ] Operator-interface screenshots
- [ ] Alarm lifecycle demonstration
- [ ] Failure-scenario evidence
- [ ] Structured Query Language (SQL) query plan before and after optimization
- [ ] Automated test report
- [ ] Manual verification checklist
- [ ] OpenTelemetry trace example
- [ ] Optional: retrieval evaluation report
- [ ] Optional: performance and cost report
- [ ] Architecture Decision Records
- [ ] Backup and recovery instructions
- [ ] Known limitations
- [ ] Short demo video
- [ ] Five-minute interview explanation

---

## Resume Bullet Template

Use real measurements when available:

> Built a local pipeline-operations Supervisory Control and Data Acquisition application using Ignition, Open Platform Communications Unified Architecture, .NET, and Postgres; implemented reusable asset models, alarm workflows, historical reporting, backend maintenance integration, and tested failure scenarios while preserving operator control boundaries.

Add an optional assistant or performance result only after it exists and has measured evidence.

Do not insert invented scale, latency, accuracy, or cost numbers.

---

## Official Starting Resources

- Ignition Maker Edition: <https://inductiveautomation.com/ignition/maker-edition>
- Ignition documentation: <https://docs.inductiveautomation.com/>
- Open Platform Communications Unified Architecture overview: <https://opcfoundation.org/about/opc-technologies/opc-ua/>
- ASP.NET Core documentation: <https://learn.microsoft.com/aspnet/core/>
- OpenTelemetry for .NET: <https://opentelemetry.io/docs/languages/dotnet/>
- Postgres documentation: <https://www.postgresql.org/docs/>

---

## North Star

The project is successful when it proves:

> I can build, integrate, test, support, and explain a Supervisory Control and Data Acquisition (SCADA) application that remains useful during abnormal conditions. I can connect operational data to .NET and Postgres without confusing enterprise software with equipment control. If I later add Artificial Intelligence (AI), it remains measured, read-only, and outside equipment-control authority.
