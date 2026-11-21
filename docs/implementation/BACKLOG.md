# Karkain Project Manager — First Implementation Backlog

## First vertical slice

**Slice goal.** Create a software project and one task, persist them in the embedded `db2` store within a transaction, and list them back as JSON — a real planning workflow (create → commit → query), no GUI.

**Karkain modules/files expected.** `src/main.kark` (CLI entry), `src/store.kark` (`db` open + `db_begin`/`db_commit`/`db_rollback`), `src/project.kark`, `src/task.kark`, `src/model.kark`, `karkain.toml`.

**Exact input.** Commands: `karkain-pm project create --name "Atlas"` then `karkain-pm task create --project Atlas --title "Design schema"`, then `karkain-pm task list --project Atlas`.

**Exact expected observable output.** After the first two commands, stdout confirms `created project Atlas` and `created task 1`; the third command prints a JSON array exactly `[{"id":1,"project":"Atlas","title":"Design schema","status":"todo"}]`. Data survives process exit because it is committed to the `db2` file. No GUI window.

**Verification command pattern (Karkain v1.2.0, Go front-end engine, C-free native Windows target).**

```
$env:KARKAIN_ENGINE="go"
karkain build src/main.kark --target native-x86_64-windows -o karkain-pm
.\karkain-pm project create --name "Atlas"
.\karkain-pm task create --project Atlas --title "Design schema"
.\karkain-pm task list --project Atlas
```

Acceptance: the JSON list matches the expected array and the transaction commits successfully (exit code 0).

## Backlog items

| ID | Feature | Files/modules | Dependencies | Acceptance criteria | Tests | Complexity | Blocking Karkain capability | Definition of done |
|---|---|---|---|---|---|---|---|---|
| PM-001 | Store init & schema | `store.kark` | `db` PRESENT/PARTIAL | Creates tables within SQL subset | Integration | M | `db` PARTIAL (no joins) | Schema smoke test passes |
| PM-002 | Project create/list | `project.kark` | PM-001 | Create + list works after restart | E2E | S | None | Round-trip verified |
| PM-003 | Task create/list | `task.kark` | PM-001, PM-002 | Task CRUD with status | E2E | M | None | Vertical slice passes |
| PM-004 | Transaction safety | `store.kark` | PM-001 | Rollback on failure keeps state | Integration | M | `db_begin/commit/rollback` PRESENT | Fault-injection test passes |
| PM-005 | Milestones | `milestone.kark` | PM-002 | Create milestone, compute completion | Unit + E2E | M | `time` PARTIAL (probe) | Milestone test passes |
| PM-006 | Dependency graph & cycles | `dep.kark` | PM-003 | Adds edges, rejects cycles | Unit | L | None | Cycle fixtures pass |
| PM-007 | TUI table/kanban views | `views.kark` | PM-003 | Keyboard nav of tasks by status | E2E | L | GUI BLOCKED (TUI path allowed) | Manual TUI run passes |
| PM-008 | JSON import/export | `xport.kark` | PM-002/003 | Export then import round-trips | Integration | M | `json` PARTIAL | Restricted-shape test passes |
| PM-009 | Telemetry link | `telemetry.kark` | PM-003; `test`/`bench` PRESENT | Attaches result JSON to task | Integration | M | Toolchain subcommands unexecuted | Attach test passes |
| PM-010 | CLI/JSON automation API | `api.kark` | PM-002..006 | Scripted create/query via JSON | E2E | M | None | Agent script passes |

## Dependency order and gates

1. PM-001 → 2. PM-002 → 3. PM-003 (**MVP gate:** vertical slice passes on compiled executable) → 4. PM-004 → 5. PM-005/PM-006 → 6. PM-007 → 7. PM-008/PM-009 → 8. PM-010. No item proceeds until the prior gate passes actual compile → executable → run verification.

## Status

All items are **NOT STARTED**. Implementation must not begin until all designs and repository initialization are complete. No tests have been run and no application binary exists for this product.
