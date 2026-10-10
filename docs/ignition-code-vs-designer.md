# Ignition: Coding Workflow vs Designer Workflow

This guide answers a practical question for a software developer learning Ignition 8.3: **when should I write code, and when must I use Designer or the Gateway webpage?**

Ignition is configurable, but it is not organized around a single CDK-style source file. Configuration is divided between Gateway resources, project resources, and runtime behavior. The graphical tools are the supported primary editors for several of those resources.

The goal is not to avoid code. The goal is to use code where it improves repeatability without hiding how the running SCADA system is configured or creating an unsupported deployment path.

## The three surfaces

| Surface | Owns or edits | PipelineOps examples |
|---|---|---|
| **Designer** | Project views, components, bindings, page configuration, project scripts, and tags exposed through Tag Browser | `PumpDetail`, `pumpId`, indirect LED binding, Power Chart, Pump UDT |
| **Gateway webpage** | Gateway-wide services, connections, runtime status, security, backup and restore | Postgres connection, SQL Historian, project launch, `.gwbk` backup |
| **Perspective session** | The operator-facing runtime | `/pump-p101`, alarm status, alarm journal |

A successful change often crosses all three: configure in Designer or Gateway, save it, and verify the result in a Perspective session.

## The most important distinction

| Kind of work | Meaning | Preferred approach |
|---|---|---|
| **Persistent configuration** | Defines what exists after restart: tags, UDTs, views, pages, bindings, connections | Designer, Gateway webpage, supported Gateway API, or a controlled file-based deployment |
| **Runtime logic** | Responds while the application runs: events, transformations, validation, calculations | Ignition Python scripts and expressions |
| **External application code** | Services outside Ignition | C#/.NET, Python, SQL, tests, containers, normal CI/CD |

A script that changes a component during one session is not automatically a persistent Designer configuration change. Likewise, editing a project view does not configure a Gateway database connection.

## What is a good fit for Python or code

### 1. Tag and UDT automation

Ignition provides supported scripting functions for programmatic tag configuration:

- `system.tag.configure` creates or updates tags from Python dictionaries or JSON.
- `system.tag.getConfiguration` reads existing tag configuration.
- `system.tag.browse`, `exportTags`, `importTags`, `move`, `rename`, and `deleteTags` support other tag-management operations.

Official manual: [`system.tag` functions](https://www.docs.inductiveautomation.com/docs/8.3/appendix/scripting-functions/system-tag) and [`system.tag.getConfiguration`](https://www.docs.inductiveautomation.com/docs/8.3/appendix/scripting-functions/system-tag/system-tag-getConfiguration).

This is useful when creating many regular instances after the first instance has been designed and verified manually. It is not necessary for the two-pump Part 4 exercise, where understanding UDT inheritance in Designer is part of the learning objective.

Conceptual example:

```python
pump_instance = {
    "name": "P102",
    "tagType": "UdtInstance",
    "typeId": "Pump"
}

system.tag.configure(
    "[default]Station1",
    [pump_instance],
    "m"
)
```

Treat configuration scripts like migrations: make them reviewable and repeatable, define collision behavior deliberately, and test them on a lab Gateway before production use.

### 2. Application behavior

Python is appropriate for bounded behavior such as:

- Component event handlers.
- Gateway and Perspective session event scripts.
- Binding transforms when an expression is insufficient.
- Input validation and data shaping.
- Calling a permitted external service.
- Logging and diagnostic utilities.

Prefer expressions and ordinary bindings for simple dataflow. A hidden script is harder for the next technician to discover than a visible binding.

### 3. External services and tests

Normal software engineering remains appropriate outside Ignition:

- The Part 6 maintenance API can use C#/.NET with automated tests.
- Postgres application tables can use migrations and SQL tests.
- Docker configuration can remain in Git.
- Health checks and integration tests can be scripted.
- Documentation and evidence checks can run in CI.

Do not write migrations for Ignition-owned historian or alarm-journal tables. Ignition owns those schemas.

### 4. Source control and controlled deployment

Ignition 8.3 stores Gateway configuration in file-based resources and documents Git-oriented workflows. It also exposes a Gateway API for supported configuration automation. This makes auditing, environment-specific configuration, and CI/CD more practical than in older versions.

Official manual: [Version and Source Control Guide](https://docs.inductiveautomation.com/docs/8.3/tutorials/version-control-guide).

For this portfolio, a `.gwbk`, project export, screenshots, and Markdown evidence remain the recovery and review path. Do not start editing the live Gateway data directory merely because the files are human-readable.

## What should still be done in Designer

The following tasks are technically represented by configuration data, but Designer is the supported and practical authoring tool for this learning project:

| Task | Why Designer is appropriate |
|---|---|
| Create and arrange Perspective components | Visual position, flex behavior, responsive layout, and component hierarchy require visual inspection. |
| Configure component properties | Designer exposes the component schema and valid property types. |
| Create bindings | The binding dialog validates tag, property, expression, query, and history binding structures. |
| Add view parameters | Direction and value type are part of the view resource and must be tested with the view. |
| Configure Perspective pages | A Page URL must be mapped to a Primary View and tested in a browser session. |
| Configure a Power Chart | Pen source strings, historian paths, axes, and runtime behavior require both configuration and visual verification. |
| Configure alarm-display components | Filters and source paths must be verified against live alarm state and journal history. |
| Design responsive layouts | A syntactically valid resource can still be unusable at the target screen size. |

Official manual: [Designer](https://docs.inductiveautomation.com/docs/8.3/platform/designer), [Perspective views](https://www.docs.inductiveautomation.com/docs/8.3/appendix/components/perspective-components/perspective-view-object), and [Pages in Perspective](https://www.docs.inductiveautomation.com/docs/8.3/ignition-modules/perspective/pages-in-perspective).

There is no first-party high-level Python construct equivalent to this imagined API:

```python
PumpDetail(
    pump_id=StringParameter(default="P101"),
    temperature=IndirectTag(
        "[default]Station1/{pumpId}/BearingTemperature"
    )
)
```

Raw project resources may be file-based, but hand-generating or editing them is not the same as using a stable CDK. It requires version-specific schema knowledge, safe Gateway scanning or deployment, resource validation, rollback, and visual QA.

## What belongs on the Gateway webpage

Use the Gateway webpage for Gateway-scoped configuration and operational checks:

- Create and test database connections.
- Create the SQL Historian and select its database connection.
- Inspect module, project, connection, and service status.
- Configure authentication and security resources.
- Launch and inspect Perspective sessions.
- Create and restore Gateway backups.

Project exports include project resources such as Perspective views, but not Gateway resources such as database connections, Tag Providers, or tags. A Gateway backup contains Gateway configuration but not the Postgres historian or alarm-journal rows.

Official manual: [Projects](https://docs.inductiveautomation.com/docs/8.3/platform/projects), [Project Export and Import](https://www.docs.inductiveautomation.com/docs/8.3/platform/projects/project-export-and-import), and [Gateway Backup and Restore](https://www.docs.inductiveautomation.com/docs/8.3/platform/gateway/gateway-backup-and-restore).

## Technically possible, but avoid for now

| Approach | Why not use it yet |
|---|---|
| Generate raw Perspective resource files | No friendly construct model; easy to create valid-looking but broken views or bindings. |
| Modify the live Gateway data directory by hand | Deployment, scan behavior, local overrides, secrets, and rollback require a deliberate 8.3 operations workflow. |
| Let Perspective session scripts reconfigure tags | Runtime users should not silently mutate persistent system configuration. |
| Build every simple binding as Python | It hides dataflow that Designer can display directly and increases maintenance cost. |
| Manipulate Ignition-owned historian tables | The schema belongs to Ignition and may change across versions. |
| Automate alarm acknowledgment, suppression, or shelving | PipelineOps is monitoring-only; alarm lifecycle actions remain explicit operator actions. |
| Generate six pumps before two are verified | Automation scales mistakes as efficiently as correct configuration. |

## Decision table for PipelineOps

| Work item | Code? | Designer? | Gateway webpage? | Choice for this project |
|---|---:|---:|---:|---|
| Define the first `Pump` UDT | Possible | Yes | No | Designer, to learn and verify inheritance |
| Create P-101 and P-102 instances | Possible | Yes | No | Designer for Part 4 |
| Create dozens of later instances | Yes | Possible | No | Consider `system.tag.configure` after a tested standard exists |
| Parameterize `PumpDetail` | Raw-resource manipulation only | Yes | No | Designer |
| Map `/pump/:pumpId` | Raw-resource manipulation only | Yes | No | Designer |
| Arrange and style the overview | Not meaningfully with ordinary Python | Yes | No | Designer plus browser QA |
| Create the Postgres connection | API/file automation is possible in a mature deployment workflow | No | Yes | Gateway webpage for this lab |
| Create the SQL Historian | API/file automation is possible in a mature deployment workflow | No | Yes | Gateway webpage for this lab |
| Query application data | Yes | Named Queries/scripts | Connection status only | Named Query or external service, depending on scope |
| Back up Gateway configuration | Can be automated later | No | Yes | Gateway webpage and `.gwbk` evidence |
| Verify live UI behavior | Browser automation can assist | Designer preview helps | Session launch/status | Perspective session remains the source of runtime evidence |

## Recommended hybrid workflow

```text
Design one instance in supported UI
    -> verify behavior in the running session
    -> export or inspect configuration
    -> automate only the repetitive, stable portion
    -> review the diff
    -> deploy through a supported path
    -> verify tags, history, alarms, and UI again
```

For PipelineOps:

1. Use Designer to finish the two-pump vertical slice.
2. Keep exact click instructions because they demonstrate knowledge of the supported commissioning tools.
3. Use Python when repetition appears, not merely to escape an unfamiliar dialog.
4. Keep external application code and tests in the repository.
5. Preserve Gateway backups and Postgres separately.
6. Never let automation turn this monitoring application into equipment control.

## Interview answer

**Speak this:**

Ignition is low-code, but it is not no-code. I use Designer for visual resources, bindings, page configuration, and live commissioning because those are the supported and observable workflows. I use Python for runtime logic and repeatable tag configuration, and I use normal source control and tests for external services. In Ignition 8.3 I can also version and deploy file-based Gateway resources, but I would introduce that only with schema validation, environment separation, backups, and a tested rollback path.

## Words to keep precise

| Say | Do not say |
|---|---|
| Designer is the primary supported visual authoring tool | “Ignition cannot be configured with code” |
| `system.tag.configure` can create or update tag configuration | “Python can replace every Designer operation” |
| Runtime script versus persistent configuration | “Changing a session property changes the project” |
| Gateway resource versus project resource | “Everything is stored in the Perspective project” |
| File-based configuration enables source-control workflows | “Ignition 8.3 is a complete CDK” |
| Visual QA remains necessary | “Valid JSON proves the HMI works” |
