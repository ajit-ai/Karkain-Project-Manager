# Karkain Project Manager

A local-first, CLI/TUI tool for planning software work: projects, tasks, milestones and dependencies — not ERP or CRM.

## Status

| Area | Status |
|---|---|
| Research | COMPLETE |
| Product design | COMPLETE |
| Repository creation | COMPLETE |
| Implementation | NOT STARTED |
| Compile/execute verification of the application | NOT STARTED (toolchain itself VERIFIED on Karkain v1.2.0) |

## What it does

Karkain Project Manager tracks the planning side of software development: define a project, break it into tasks and sub-tasks, group tasks into milestones, record dependencies, and review progress through a terminal kanban or table view. Its store is Karkain's embedded `db2` database on the local machine, so data stays with the developer and the tool works offline. It can optionally attach build/test results from the same toolchain to work items, giving a tight work-item-to-telemetry link. It is a software planning tool only — it does not manage customers, sales, inventory, accounting, payroll or any ERP/CRM function, and it is not a build scheduler (Karkain Build Manager) or an editor/IDE.

## Who it serves

- Small software teams and individual developers planning work locally without a SaaS subscription.
- CI/agent integrators who want to create, query and update work items via a scriptable CLI/JSON interface.
- Teams on modest hardware (about 4 GB RAM) wanting an offline, self-contained tracker.
- Developers who want milestones and tasks tied to the Karkain build/test toolchain.

## Planned features

- **Projects & work items (planned):** create/update projects, tasks, sub-tasks, labels and status workflows.
- **Milestones (planned):** define milestones with target dates and track completion.
- **Dependencies (planned):** task-to-task dependency edges with cycle detection.
- **Views (planned):** TUI kanban board, table and milestone roadmap.
- **Local store (planned):** embedded `db2` with `db_begin`/`db_commit`/`db_rollback` transactions.
- **CLI/JSON API (planned):** create, list, query and update work items from scripts and agents.
- **Telemetry link (planned, optional):** attach `test`/`bench`/`build` results to work items.
- **Import/export (planned):** JSON export/import of projects and tasks.

## Explicit non-goals

- No native desktop GUI in v1.2.0 (GUI toolkit BLOCKED); GUI is a planned later layer, not shipped.
- Not ERP, CRM, accounting, inventory, HR, payroll or customer/sales management.
- Not a build tool or compiler (Karkain Build Manager); not an editor/IDE.
- No multi-tenant SaaS, per-seat billing or hosted service in MVP.
- No pricing, adoption or benchmark claims.

## Karkain foundation

Exclusive foundation: Karkain Language v1.2.0. All application source will be Karkain. No other implementation language. No compiler modifications.

## Platform support status

Windows x86-64 toolchain VERIFIED (`karkain.exe` reports v1.2.0; C-free native Windows build → executable → run verified). Linux cross-target output PRESENT but execution NOT VERIFIED on this Windows host. macOS NOT VERIFIED.

## Build and run status

No application binary exists yet. Intended generic commands (generic form only; no machine-specific paths):

```
karkain build src/main.kark -o karkain-pm
karkain run src/main.kark
```

These become active when implementation starts. On this machine the verified toolchain path uses the Go front-end engine and an explicit C-free native Windows target; the default `kcc` engine is currently unusable in this environment.

## Known blockers

- Native GUI toolkit: BLOCKED — TUI board/table first; GUI planned only.
- `db` module: PRESENT/PARTIAL — embedded `db2` with SQL subset and transactions, but no external drivers or joins; schema must stay within the subset.
- Full JSON DOM: PARTIAL (`json_get_field` only) — import/export uses a restricted JSON shape.
- `system`/`env`/`process`/`time`: PARTIAL shell wrappers — date/time handling must be probed.
- Native cross-platform file metadata: PARTIAL; Linux/macOS execution NOT VERIFIED.

## Repository

GitHub: https://github.com/ajit-ai/Karkain-Project-Manager · Issues: https://github.com/ajit-ai/Karkain-Project-Manager/issues · License: Apache-2.0.
