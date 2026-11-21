# Karkain Project Manager — Product Specification

**Document status:** Design COMPLETE. Research = COMPLETE. Repository creation = COMPLETE. Application implementation = NOT STARTED. Application compile/execute verification = NOT STARTED (toolchain path itself VERIFIED on Karkain v1.2.0).


## A. Product definition

Karkain Project Manager is a local-first tool for planning software work: projects, tasks, milestones and dependency relationships. **Problem.** [VF] Jira is a cloud-first, process-heavy work tracker (with Data Center being retired); Linear is a SaaS issue/cycle tracker; GitHub Projects is GitHub-native planning. [RI] All are hosted or account-bound, none is a small offline CLI/TUI tracker coupled to a compiler toolchain, and none is free of per-seat SaaS lock-in for local use. **Vision.** A self-contained, file-backed software planning tool that runs from a terminal, stores data locally with embedded `db2`, and exposes a JSON/CLI API that CI and agents can drive. **Intended users/segments.** Small software teams and individual developers, offline/air-gapped users, CI/agent integrators, teams on ~4 GB RAM. **Usage scenarios.** (1) Plan a project by creating tasks and milestones; (2) review a TUI kanban board; (3) script work-item creation from an agent; (4) attach a build/test result to a task. **Market positioning.** A lightweight, offline complement to hosted trackers — software project planning only. **Primary value proposition.** Local ownership of work data, scriptable CLI/JSON automation, and optional coupling of work items to build/test telemetry in one toolchain. **Explicit non-goals/boundaries.** Not ERP, CRM, accounting, inventory, HR or customer/sales management; not a hosted service; not a build tool (Karkain Build Manager); not an editor/IDE (Karkain IDE / Karkain Code Editor); no desktop GUI in v1.2.0 (BLOCKED); no pricing/adoption/benchmark claims. [VF/RI]

## B. Functional architecture

| Module | Responsibility |
|---|---|
| project-svc | Create/update projects, labels and status workflows |
| task-svc | Create/update tasks and sub-tasks, assign status |
| milestone-svc | Define milestones and compute completion |
| dep-graph | Manage task dependencies and detect cycles |
| store | `db2` persistence via `db` module + transactions |
| query-api | CLI/JSON create, list, get, update commands |
| tui-views | Kanban, table and milestone roadmap views |
| telemetry-link | Optional attach of `test`/`bench`/`build` results |
| io-xport | JSON import/export of projects and tasks |
| config | Local settings and schema version |

**Core workflows.** Create project → add tasks → group into milestone → set dependencies → review board → close tasks. **Admin workflows.** Initialize/migrate the local store, configure status workflow names, export/import data. **Error/recovery workflows.** Roll back a failed transaction with `db_rollback`; reject cyclic dependencies; recover from a corrupt config with defaults. **Roles & permissions.** Single local user/owner; no multi-tenant auth in MVP. **Configuration/customization.** Status labels and view preferences via `config`. **Import/export.** Restricted JSON format (PARTIAL `json_get_field` only). **Integration requirements.** Embedded `db` module, `io`, optional `test`/`bench`/`build` results. [VF/RI/PROPOSAL]

## C. Competitor analysis

Researched benchmarks: **Jira**, **Linear**, plus **GitHub Projects**. [VF] Jira (Atlassian) offers Scrum/Kanban boards, backlogs and configurable workflows across Free/Standard/Premium/Enterprise plans, scaling to 100,000 users; Data Center support ends 2029-03-28 with new sales ended March 2026. Linear is a cloud-native issue/project/cycle tracker with Free/Basic/Business/Enterprise plans (Free $0, Basic $10/user/month yearly, Business $16/user/month yearly). GitHub Projects provides GitHub-native table/board/roadmap views over issues and PRs. Feature comparison matrix (Karkain = **proposed**):

| Capability | Karkain (proposed) | Jira | Linear | GitHub Projects |
|---|---|---|---|---|
| GUI boards | **BLOCKED**; TUI views proposed | Scrum/Kanban boards | Board/list views | Board/table/roadmap |
| Local/offline store | Embedded `db` (PRESENT) | Cloud; DC retiring | SaaS | GitHub-dependent |
| CLI/JSON automation | Native CLI + JSON [VF/proposed] | REST/API + automation | API, webhooks, MCP | GitHub API |
| Security/admin | UNKNOWN — not verified | SSO/SCIM, audit, residency | SAML/SCIM, IP limits | GitHub permissions |
| Scale limits | UNKNOWN — not verified | Up to 100,000 users | Unlimited teams (Business) | GitHub plan limits |
| Price | UNKNOWN — not verified | Tiers; price UNKNOWN | $0/$10/$16/custom | Free tier; price UNKNOWN |

**Platform/deployment.** Jira Cloud (Data Center retiring); Linear SaaS with web/desktop clients and MCP; GitHub Projects SaaS/GitHub-native; Karkain: local C-free native binary (Windows VERIFIED). **Licensing comparison.** Linear and Atlassian plan facts above are VERIFIED; Jira exact per-user prices and GitHub Projects paid-tier details are **UNKNOWN — not verified**; Karkain licensing UNKNOWN — not verified. No prices invented. **Strengths/limitations.** [VF+RI] Jira: depth/compliance/scale but complex and cloud-migrating; Linear: fast/focused but free-plan caps and guests require Business; GitHub Projects: zero-friction for code work but lightest tracker; Karkain: offline, self-contained, scriptable but no GUI and PRESENT-not-executed capabilities. **Market gaps.** [RI] Local/self-hosted file-backed tracking tied to builds/code; offline CLI/TUI workflows; no per-seat lock-in; work-item↔build/test telemetry in one toolchain. **Differentiation.** [RI/PROPOSAL] Embedded-`db` local store, TUI views and CLI/JSON automation; couple issues to `test`/`bench`/`build` results. **Features deliberately not copied:** cloud hosting, per-seat billing, enterprise SSO/SCIM admin, GUI boards, customer-facing portals. Consistent with `docs/competitor-analysis/COMPETITORS.md`. [VF/RI/UA]

## D. Technical architecture

**Application architecture.** CLI + TUI frontends over a domain service layer and embedded store. **Component boundaries.** Each service is an independent Karkain component with a JSON command contract. **Process/service boundaries.** One local process; optional local service over stdio/`net` building blocks (no HTTP framework). **UI architecture.** CLI/TUI/service-first is explicit: Karkain v1.2.0 has no native GUI toolkit (BLOCKED), so no windows, controls or event loop; GUI is a **planned layer contingent on ecosystem capability**, supplied by an external host, never a v1.2.0 claim. **Domain model.** Project, Task, SubTask, Milestone, Dependency, Label, Status, WorkItemResult. **Data storage strategy.** Embedded `db2` with a restricted SQL subset; file lives in the local workspace. **API/interface architecture.** Stdio JSON commands (create/list/get/update) plus optional local service. **Event/message architecture.** `async` channels may serialize store writes (cooperative). **Logging/diagnostics/observability.** Structured JSON lines to stderr and an audit table. **Configuration management.** Versioned `config` file. **Plugin/extension architecture.** None in MVP; no GUI plugin API. [VF/RI]

## E. Data design

**Main entities.** Project (id, name, description), Task (id, project_id, title, status, milestone_id, parent_id), Milestone (id, project_id, name, target_date), Dependency (from_task, to_task), Label (id, name), WorkItemResult (task_id, kind, exit_code, artifact). **Relationships.** Project 1—N Task; Project 1—N Milestone; Task N—1 Milestone; Task N—N Task via Dependency; Task N—N Label. **Ownership.** Local user owns all data. **Validation.** Status values from configured set; dependency insertion rejected on cycle; target_date validated via `time` (probe first). **Lifecycle.** project draft→active→archived; task todo→in-progress→done. **Persistence.** Embedded `db2` tables; transactions via `db_begin`/`db_commit`/`db_rollback` (PRESENT). **Backup/restore.** Copy the database file; export JSON. **Migration.** Schema version table; migrations within SQL subset. **Import/export.** Restricted JSON (PARTIAL `json_get_field`; arrays/nested objects not fully supported). **Audit history.** Append-only audit table of commands. **Storage decision.** Embedded `db2` is appropriate for structured queries and transactions; it avoids relying on PARTIAL filesystem metadata and needs no external SQL driver/joins. [VF/RI]

## F. Karkain implementation architecture

**Required capabilities.** `db` PRESENT/PARTIAL (embedded `db2`; CREATE/INSERT/SELECT/UPDATE/DELETE/DROP; `db_begin`/`db_commit`/`db_rollback` and prepared statements present; no external drivers/joins); `io` PRESENT; `config` PRESENT (unverified); `time` PARTIAL (shell wrapper — probe before date use); `json` PARTIAL (only `json_get_field`); `crypto`/`encoding` PRESENT (checksums for export integrity). **Compiler/runtime.** v1.2.0 only; Go front-end engine required here (default `kcc` BLOCKED); C-free native Windows backend VERIFIED; Linux/macOS targets PRESENT but cross-execution NOT VERIFIED. **stdlib actually present.** `net` PRESENT (5 s read timeout); `http` PRESENT (building blocks only); `filesystem` PARTIAL (PowerShell shell-outs); `system`/`env`/`process`/`time` PARTIAL (`get_args()` empty array; `system()` returns exit code, not stdout — probe before use); `async`, `collections`, `generics`, `string`, `path`, `math` PRESENT; `numerics`/`gpu` not required. **Missing dependencies/blockers.** GUI toolkit BLOCKED; full JSON DOM PARTIAL; native file metadata PARTIAL; `system`/`process`/`time` PARTIAL; TLS/WebSocket/HTTP-server BLOCKED (no hosted service); crash-atomic file locking PARTIAL. **Native opportunities.** Local native binary with embedded `db2`. **FFI/OS needs.** None required; no replacement language. **Build/packaging.** `karkain build --target native-x86_64-windows -o ...` with `KARKAIN_ENGINE=go`. **Blockers recorded, never faked; no Karkain changes, no alternate language.** [VF]

## G. Non-functional requirements

- **Performance.** Workload: create 1,000 tasks and query by status; method: `bench` timing; target: p95 query < 500 ms on ~4 GB RAM (measurement plan, not achieved).
- **Memory.** Workload: 1,000 tasks loaded; method: OS task-manager sample; target: resident set < 250 MB.
- **CPU.** Workload: dependency cycle check over 1,000 edges; method: `prof`; target: < 10% sustained.
- **Scalability.** Workload: 1→10,000 tasks; method: scripted generation; target: linear query growth within SQL subset.
- **Reliability.** Workload: interrupted transactions; method: fault injection; target: `db_rollback` leaves consistent state.
- **Security.** Workload: malicious JSON import; method: fuzz; target: no injection into SQL strings (prepared statements).
- **Privacy.** All data local; no telemetry, no sign-in, no cloud sync.
- **Portability.** Windows VERIFIED; Linux/macOS NOT VERIFIED.
- **Maintainability.** Each service documented with a JSON contract.
- **Accessibility.** Fully keyboard-operable TUI.
- **Observability.** All commands and exit codes logged in an audit table.
- **Recovery.** Restore from database file copy / JSON export. [RI]

## H. Compatibility and portability

**Supported OS.** Windows (toolchain VERIFIED); Linux/macOS NOT VERIFIED. **Architectures.** x86-64 C-free native verified on Windows only. **Karkain v1.2.0 matrix.** Compiler v1.2.0 VERIFIED; `db2` subset as in F; stale install-dir `VERSION` (1.1.0) ignored. **Binary/package formats.** Native `.exe` on Windows; cross-target output PRESENT (unverified); no installer yet. **Install/uninstall.** Place binary on PATH, choose a local data directory; uninstall by removal (data retained). **Config migration.** Versioned schema; none required before v1.2.0. **Data migration.** Schema version table within SQL subset. **Backward compatibility.** None required before v1.2.0. **Upgrade/rollback.** Replace binary, keep database file. **Cross-platform differences.** `system`/`time` wrappers use PowerShell; `db2` is embedded/portable but cross-execution UNVERIFIED. **Unsupported.** GUI boards, hosted/multi-tenant mode, ERP/CRM functions, external SQL drivers. [VF/UA]

## I. Testing strategy

**Unit.** Each service and dependency-cycle logic; criterion: all functions covered. **Integration.** `db2` transactions against temp store; criterion: commit/rollback behavior correct. **E2E.** Create project → task → milestone → board view; criterion: JSON output and TUI state match. **Platform-specific.** Windows native binary; criterion: compile + run smoke test. **Security.** Malicious JSON/SQL input; criterion: prepared statements block injection. **Performance.** 1,000-task query; criterion: meets G target. **Regression.** Prior-bug corpus; criterion: zero regressions. **Compatibility.** v1.2.0 toolchain/`db` contract; criterion: SQL subset queries succeed. **Actual-compiled-executable.** Build with `KARKAIN_ENGINE=go` + native-x86_64-windows, run `.exe`; criterion: expected stdout/data. **Failure/recovery.** Inject failed transaction; criterion: consistent state after rollback. **All tests PLANNED; none run yet for this application.** [VF/RI]

## J. Implementation roadmap

**MVP.** project-svc, task-svc, store (`db2`), minimal CLI create/list. **Core functional release.** milestone-svc, dep-graph, TUI table/kanban views. **Advanced release.** telemetry-link, JSON import/export, CLI/JSON automation polish. **Production-readiness release.** security fuzzing, transaction soak, platform probes, docs. **Feature dependencies.** store before all services; task-svc before dep-graph/milestone-svc; CLI before TUI. **Milestones/gates.** M0 design complete (done); M1 MVP compiles + runs; M2 core E2E passes; M3 advanced; M4 production gates. **Risks/mitigations.** GUI blocked → TUI; `db` PARTIAL → stay within SQL subset; `time`/`system` PARTIAL → probe; JSON PARTIAL → restricted shape; cross-platform UNVERIFIED → probe. No fictional dates or completion claims. [RI]
