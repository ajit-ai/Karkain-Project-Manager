# Competitor Analysis - Karkain Project Manager

Source: consolidated portfolio research _portfolio-foundation/research/domain1-developer.md, section for Karkain Project Manager. All changing facts accessed 2026-11-21. Evidence labels: VERIFIED FACT / RESEARCH-BASED INTERPRETATION / PROPOSAL / UNVERIFIED ASSUMPTION. Competitor capabilities and prices are reproduced as researched, never invented.

## 3. Karkain Project Manager â€” benchmarks: Jira, Linear (+ GitHub Projects)

**Positioning and intended users. [VF]** Jira (Atlassian) is a work-tracking platform with Scrum/Kanban boards, backlogs, agile reporting and configurable workflows across Free/Standard/Premium/Enterprise plans, scaling to 100,000-user enterprises. Linear is a cloud-native issue/project/cycle tracker for software teams with four plans. GitHub Projects is GitHub-native planning (table, board, roadmap) over issues and pull requests. **[RI]** Jira targets process-heavy organisations, Linear fast product teams, GitHub Projects GitHub-centred developers.

**Major capabilities/modules. [VF]** Jira: boards, backlogs, roadmaps, dependency/capacity planning, tiered automation limits, workflow/field/screen schemes, apps. Linear: issues, projects, cycles, initiatives, triage, releases, Insights/Dashboards, agent platform, MCP, SLAs. GitHub Projects: custom fields/views and issue/PR automation.

**Platform support. [VF]** Jira: Cloud primary; Data Center self-hosted is being retired â€” official pages state support ends 2029-03-28 and new sales ended March 2026; Server already ended. Linear: SaaS with web and desktop clients, Linear MCP server. GitHub Projects: web/GitHub-native. **[UA]** Native offline modes not verified.

**Deployment/packaging.** Jira Cloud/Data Center [VF]; Linear SaaS [VF]; GitHub Projects SaaS/self-hosted GitHub [VF].

**Architecture & extensibility. [VF]** Jira has permission/issue-security/workflow schemes, automation and a large Marketplace; Linear exposes API, webhooks, MCP and tiered rate limits; GitHub Projects uses the GitHub Issues/PR model and API. **[RI]** Jira is most configurable; Linear most opinionated.

**Integration ecosystem. [VF]** Jira docs enumerate GitHub, GitLab, Bitbucket, Bamboo, Jenkins, Azure DevOps, Figma, Snyk and deployment tools. Linear lists Zendesk/Intercom plus Slack/email intake; GitHub Projects is GitHub-integrated by definition.

**Security/admin. [VF]** Jira: domain verification, session-duration management, admin insights, sandbox, release tracks, SSO/SCIM/AD Sync (Enterprise included; Guard subscription otherwise), audit logs, data residency, IP allowlisting, uptime SLAs (99.9%/99.95%). Linear: SAML+SCIM, granular admin controls, IP restrictions, domain claiming, audit log, HIPAA, uptime SLA on Enterprise; SSO (Google) on lower tiers.

**Performance/scalability. [VF]** Jira plan limits: up to 100,000 users, up to 150 sites, unlimited storage on Premium/Enterprise. Linear: unlimited members/teams on Business; API rate limits 2Ã—/5Ã— on Business/Enterprise. **[RI]** Both scale well vertically in SaaS form; neither offers a terminal-first local mode.

**Licensing/pricing. [VF]** Linear: Free $0 (2 teams, 250 issues), Basic $10/user/month billed yearly, Business $16/user/month billed yearly, Enterprise custom. Jira: official plan-comparison page verifies plan tiers/limits; **exact current per-user prices UNKNOWN â€” not verified** from an official pricing page (an official Atlassian small-teams page shows "from $10/month"). GitHub Projects: free tier available to all users [VF]; paid-feature details UNKNOWN â€” not verified.

**Strengths / weaknesses. [VF+RI]** Jira: depth/compliance/scale, but complex and Data Center retirement forces cloud migration. Linear: speed/focus, but free-plan caps, guests require Business [VF], less process depth. GitHub Projects: zero-friction for code work, but lightest tracker [RI].

**Underserved needs / gaps. [RI]** Local/self-hosted file-backed issue tracking tied to builds/code; offline CLI/TUI workflows; no per-seat SaaS lock-in; work-item â†” build/test telemetry in one toolchain.

**Opportunities for Karkain differentiation (PROPOSAL). [RI/PROPOSAL]** Build a **CLI/TUI + local service** project manager backed by Karkain's embedded `db` module (`db_begin/commit/rollback` PRESENT [VF]) and workspace manifests, with JSON output. **GUI is blocked in v1.2.0**, so skip GUI boards; offer TUI kanban/table views and a CLI/JSON API CI and agents can drive. Differentiate from Jira's cloud direction via offline, self-contained operation; from Linear by coupling issues to `test`/`bench`/`build` results.

**Feature comparison matrix.**

| Capability | Karkain (proposed) | Jira | Linear | GitHub Projects |
|---|---|---|---|---|
| GUI boards | **BLOCKED**; TUI views proposed | Scrum/Kanban boards | Board/list views | Board/table/roadmap |
| Local/offline store | Embedded `db` (PRESENT) | Cloud; DC retiring | SaaS | GitHub-dependent |
| CLI/JSON automation | Native CLI + JSON [VF/proposed] | REST/API + automation | API, webhooks, MCP | GitHub API |
| Security/admin | UNKNOWN â€” not verified | SSO/SCIM, audit, residency | SAML/SCIM, IP limits | GitHub permissions |
| Scale limits | UNKNOWN â€” not verified | Up to 100,000 users | Unlimited teams (Business) | GitHub plan limits |
| Price | UNKNOWN â€” not verified | Tiers; price UNKNOWN | $0/$10/$16/custom | Free tier; price UNKNOWN |

**Sources (accessed 2026-11-21):** https://support.atlassian.com/jira-cloud-administration/docs/explore-jira-cloud-plans/ ; https://www.atlassian.com/software/jira/guides/more/jira-editions ; https://atlassian.com/software/jira/download/data-center ; https://linear.app/pricing ; https://linear.app/docs/mcp.md ; https://docs.github.com/en/issues/planning-and-tracking-with-projects ; https://github.com/features/project-management

---

