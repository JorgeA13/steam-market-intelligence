# ADR-0004: Repository zones by responsibility and code promotion

- **Date:** 2026-10-18
- **Status:** Accepted

## Context

Building a data platform requires a lot of exploratory work: probing sources, inspecting raw files, trying transformations. That code is written to learn something, not to run unattended, so it is rarely clean, tested or reusable on the first attempt.

If there is no clear boundary, exploratory code tends to become production code without ever being redesigned. A common result is a single 1,000-line stored procedure with chained temp tables that nobody can understand, test or safely change.

At the same time, this repository is a professional portfolio. The history of how decisions were reached (explorations, validations, ADRs) is as valuable as the final pipelines.

## Options considered

1. **Study repository now, clean repository at the end:** the final repository looks polished, but loses the history and the reasoning behind each decision, and the copy step risks divergence between both repositories.
2. **Single repository without separation:** simple, but exploration and production code mix, quality standards are unclear, and notebooks end up imported by production jobs.
3. **Do not version exploration at all:** keeps the repository small, but loses the evidence behind decisions (for example, why a data source was accepted or rejected).
4. **Single repository with zones and an explicit promotion path:** keeps history and evidence, and gives each kind of code its own quality standard.

## Decision

Option 4. The project uses three zones:

| Zone | Location | In Git | Minimum standard |
|---|---|---|---|
| Scratch | Personal Databricks folder (`/Workspace/Users/<user>/scratch`), outside the Git folder | No | None; disposable |
| Exploration | `notebooks/` | Yes | Numbered; markdown tells the story (question → test → finding); runs top to bottom; no secrets; committed in source format without outputs; no tests required |
| Production | `src/vemx/`, `pipelines/`, `resources/`, `tests/` | Yes | Small single-purpose functions; I/O separated from transformation logic; no hardcoded configuration; docstrings; unit tests; lint; reviewed in a pull request |

Boundary rules:

- Production code never imports or depends on exploration notebooks. Notebooks may import from `src/vemx/`.
- Code is **promoted, not copied**: when exploratory logic is going to be reused, it becomes a function in `src/vemx/` with tests, and the notebook is updated to call that function.
- Each table is built by its own module, in the same spirit as dbt models: one table, one transformation, one test suite.

## Rationale

- Option 4 is the only one that keeps both the evidence (how and why decisions were made) and a production codebase with clear quality rules.
- Different zones allow fast, imperfect exploration without lowering the standard of production code.
- The promotion path prevents exploratory code from silently becoming production code.
- A single repository with squash merges keeps `main` readable while preserving the full history.

## Consequences

- **Positive:** decisions are traceable to the exploration that supports them; production code stays modular, testable and easy to change; the repository shows how the project evolved.
- **Costs:** promoting code takes deliberate effort (refactoring and tests); every pull request must respect the zone rules.
- **Risks:** exploration notebooks can become outdated. Mitigation: when a notebook no longer reflects reality, it is updated or removed in a dedicated pull request (Git keeps the history).
- **Follow-up:** CI (Module 9) will enforce lint and tests on the production zone only.