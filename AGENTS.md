# Repository instructions for Codex

## Project and source of truth

- This is a local Ignition Maker 8.3 / Perspective learning portfolio named `PipelineOps`, with Postgres in Docker. The user has retired Cursor; maintain agent instructions here.
- Read `README.md`, `docs/build-order.md`, the current `docs/portfolio-part-N.md`, and its interview-concepts file before advising or changing that part. Consult `docs/evidence/part-3/problems-solved.md` for known failures.
- Use the build-order status and individual part checklists for progress. `docs/portfolio-project.md` is a long-term architecture reference; its older checklists are not the current completion record.
- Follow the build order. Do not expand a part into later OPC UA, .NET, AI, cloud, or six-pump work unless the user asks.
- Ignition configuration lives on the Gateway. Editing these Markdown notes does not implement or verify a Designer change. Distinguish planned work, user-reported results, and observed evidence; never mark a checklist complete without support.
- Preserve existing screenshots and backups. A Gateway `.gwbk` contains configuration, not Postgres historian or journal rows.
- Keep PipelineOps monitoring-only. Display bindings are one-way. Equipment control stays outside this application; APIs and AI must not acknowledge, suppress, or shelve alarms.

## Ignition instructions must be click-by-click

The user is new to Ignition. Any Designer, Gateway, Tag Browser, Perspective, or commissioning procedure must use exact clicks, not summaries.

- Name the application: Designer, Gateway webpage (`http://localhost:8088`), or Perspective client in the browser. They are different surfaces.
- Name the panel: Project Browser, Tag Browser, Property Editor, Perspective Components, or Page Configuration.
- If a panel may be hidden, include **View → Panels → [name]**.
- One numbered step means one action. Say what to click, what to type, and what should appear afterward.
- Include easily missed controls: chain/binding icon, Apply versus OK, Ctrl+S, Page URL versus view name.
- Prefer the installed 8.3 UI. When 8.1 and 8.3 labels differ, give both when known; verify uncertain labels against the official versioned manual instead of inventing clicks.
- Do not merely say “bind the tag” or “add a page.” Identify the widget, property (`props.value` versus `props.text`), and dialog buttons.
- Explain the purpose briefly, then give a bounded build block and a visible pass condition.
- Use Inductive University and the Ignition User Manual for product behavior. The assistant helps with sequence and explanation; it does not replace vendor training.
- Include relevant official Ignition 8.3 User Manual links in guidance for every portfolio part, linking directly to the topics being built or explained so the user can study the source.

## Interview concepts with every part

When starting or exploring a new portfolio part, create or update `docs/interview-concepts-part-N.md` in the same session. Do not wait until the part is finished.

- If the file exists, add cards for newly explored concepts without rewriting the whole file.
- Do not create another part document for a small follow-up already covered.
- Match Part 1: title `Interview Concepts: Part N`; an intro about spoken answers and honest scope; each concept has **Speak this**, then a table for What / Why / How I used it / How it can fail.
- Add optional **If they ask** follow-ups, then finish with a **30-second story** and a **Words to keep precise** table.
- Cover only concepts within the current part. Leave “How I used it” as a planned or pending statement until implementation is verified. Do not turn future features into past-tense experience.

## Repository work

- Preserve the user's uncommitted edits and UTF-8 Markdown text.
- Make focused changes; do not scaffold future application folders just because they appear in the architecture plan.
- Validate documentation changes with a diff and link/path checks. Use relevant tests when executable code is introduced; do not claim the running Gateway was tested from a documentation-only change.
- Local lab credentials in `docker-compose.yml` are intentional; do not reuse them for real systems or add real credentials to notes.
