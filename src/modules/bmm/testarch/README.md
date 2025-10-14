---
last-redoc-date: 2025-10-14
---

# Test Architect (TEA) Agent Guide

## Overview

- **Persona:** Murat, Master Test Architect and Quality Advisor focused on risk-based testing, fixture architecture, ATDD, and CI/CD governance.
- **Mission:** Deliver actionable quality strategies, automation coverage, and gate decisions that scale with project level and compliance demands.
- **Use When:** Project level ≥2, integration risk is non-trivial, brownfield regression risk exists, or compliance/NFR evidence is required.

## TEA Workflow Lifecycle

TEA integrates across the entire BMad development lifecycle, providing quality assurance at every phase:

```
┌──────────────────────────────────────────────────────────┐
│             BMM Phase 2: PLANNING                        │
│                                                          │
│  PM: *plan-project                                       │
│       ↓                                                  │
│  TEA: *framework ──→ *ci ──→ *test-design                │
│       └─────────┬─────────────┘                          │
│                 │ (Setup once per project)               │
└─────────────────┼──────────────────────────────────────────┘
                  ↓
┌──────────────────────────────────────────────────────────┐
│            BMM Phase 4: IMPLEMENTATION                   │
│                  (Per Story Cycle)                       │
│                                                          │
│  ┌─→ SM: *create-story                                  │
│  │        ↓                                              │
│  │   TEA: *atdd (optional, before dev)                  │
│  │        ↓                                              │
│  │   DEV: implements story                               │
│  │        ↓                                              │
│  │   TEA: *automate ──→ *test-review (optional)         │
│  │        ↓                                              │
│  │   TEA: *trace (refresh coverage)                     │
│  │        ↓                                              │
│  └───[next story]                                        │
└─────────────────┼──────────────────────────────────────────┘
                  ↓
┌──────────────────────────────────────────────────────────┐
│                EPIC/RELEASE GATE                         │
│                                                          │
│  TEA: *nfr-assess (if not done earlier)                 │
│       ↓                                                  │
│  TEA: *test-review (final audit, optional)              │
│       ↓                                                  │
│  TEA: *gate ──→ PASS | CONCERNS | FAIL | WAIVED         │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### TEA Integration with BMad v6 Workflow

TEA operates **across all four BMad phases**, unlike other agents that are phase-specific:

<details>
<summary><strong>Cross-Phase Integration & Workflow Complexity</strong></summary>

### Phase-Specific Agents (Standard Pattern)

- **Phase 1 (Analysis)**: Analyst agent
- **Phase 2 (Planning)**: PM agent
- **Phase 3 (Solutioning)**: Architect agent
- **Phase 4 (Implementation)**: SM, DEV agents

### TEA: Cross-Phase Quality Agent (Unique Pattern)

TEA is **the only agent that spans all phases**:

```
Phase 1 (Analysis) → [TEA not typically used]
    ↓
Phase 2 (Planning) → TEA: *framework, *ci, *test-design (setup)
    ↓
Phase 3 (Solutioning) → [TEA validates architecture testability]
    ↓
Phase 4 (Implementation) → TEA: *atdd, *automate, *test-review, *trace (per story)
    ↓
Epic/Release Gate → TEA: *nfr-assess, *gate (release decision)
```

### Why TEA Needs 9 Workflows

**Standard agents**: 1-3 workflows per phase
**TEA**: 9 workflows across 3+ phases

| Phase       | TEA Workflows                          | Frequency        | Purpose                          |
| ----------- | -------------------------------------- | ---------------- | -------------------------------- |
| **Phase 2** | *framework, *ci, \*test-design         | Once per project | Establish quality infrastructure |
| **Phase 4** | *atdd, *automate, *test-review, *trace | Per story/sprint | Continuous quality validation    |
| **Release** | *nfr-assess, *gate                     | Per epic/release | Go/no-go decision                |

This complexity **requires specialized documentation** (this guide), **extensive knowledge base** (19+ fragments), and **unique architecture** (`testarch/` directory).

</details>

## Prerequisites and Setup

1. Run the core planning workflows first:
   - Analyst `*product-brief`
   - Product Manager `*plan-project`
   - Architect `*solution-architecture`
2. Confirm `bmad/bmm/config.yaml` defines `project_name`, `output_folder`, `dev_story_location`, and language settings.
3. Ensure a test test framework setup exists; if not, use `*framework` command to create a test framework setup, prior to development.
4. Skim supporting references (knowledge under `testarch/`, command workflows under `workflows/testarch/`).
   - `tea-index.csv` + `knowledge/*.md`

## High-Level Cheat Sheets

### Greenfield Feature Launch (Level 2)

| Phase              | Test Architect                                                            | Dev / Team                                                                       | Outputs                                                                               |
| ------------------ | ------------------------------------------------------------------------- | -------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| Setup              | -                                                                         | Analyst `*product-brief`, PM `*plan-project`, Architect `*solution-architecture` | `{output_folder}/product-brief*.md`, `PRD.md`, `epics.md`, `solution-architecture.md` |
| Pre-Implementation | Run `*framework` (if harness missing), `*ci`, and `*test-design`          | Review risk/design/CI guidance, align backlog                                    | Test scaffold, CI pipeline, risk and coverage strategy                                |
| Story Prep         | -                                                                         | Scrum Master `*create-story`, `*story-context`                                   | Story markdown + context XML                                                          |
| Implementation     | (Optional) Trigger `*atdd` before dev to supply failing tests + checklist | Implement story guided by ATDD checklist                                         | Failing acceptance tests + implementation checklist                                   |
| Post-Dev           | Execute `*automate`, (Optional) `*test-review`, re-run `*trace`           | Address recommendations, update code/tests                                       | Regression specs, quality report, refreshed coverage matrix                           |
| Release            | (Optional) `*test-review` for final audit, Run `*gate`                    | Confirm Definition of Done, share release notes                                  | Quality audit, Gate YAML + release summary (owners, waivers)                          |

<details>
<summary>Execution Notes</summary>

- Run `*framework` only once per repo or when modern harness support is missing.
- `*framework` followed by `*ci` establishes install + pipeline; `*test-design` then handles risk scoring, mitigations, and scenario planning in one pass.
- Use `*atdd` before coding when the team can adopt ATDD; share its checklist with the dev agent.
- Post-implementation, keep `*trace` current, expand coverage with `*automate`, optionally review test quality with `*test-review`, and finish with `*gate`.
- Use `*test-review` after `*atdd` to validate generated tests, after `*automate` to ensure regression quality, or before `*gate` for final audit.

</details>

<details>
<summary>Worked Example – “Nova CRM” Greenfield Feature</summary>

1. **Planning:** Analyst runs `*product-brief`; PM executes `*plan-project` to produce PRD and epics; Architect completes `*solution-architecture` for the new module.
2. **Setup:** TEA checks harness via `*framework`, configures `*ci`, and runs `*test-design` to capture risk/coverage plans.
3. **Story Prep:** Scrum Master generates the story via `*create-story`; PO validates using `*assess-project-ready`.
4. **Implementation:** TEA optionally runs `*atdd`; Dev implements with guidance from failing tests and the plan.
5. **Post-Dev and Release:** TEA runs `*automate`, optionally `*test-review` to audit test quality, re-runs `*trace`, and finishes with `*gate` to document the decision.

</details>

### Brownfield Feature Enhancement (Level 3–4)

| Phase             | Test Architect                                                                         | Dev / Team                                                 | Outputs                                                                 |
| ----------------- | -------------------------------------------------------------------------------------- | ---------------------------------------------------------- | ----------------------------------------------------------------------- |
| Refresh Context   | -                                                                                      | Analyst/PM/Architect rerun planning workflows              | Updated planning artifacts in `{output_folder}`                         |
| Baseline Coverage | Run `*trace` to inventory existing tests                                               | Review matrix, flag hotspots                               | Coverage matrix + initial gate snippet                                  |
| Risk Targeting    | Run `*test-design`                                                                     | Align remediation/backlog priorities                       | Brownfield risk memo + scenario matrix                                  |
| Story Prep        | -                                                                                      | Scrum Master `*create-story`                               | Updated story markdown                                                  |
| Implementation    | (Optional) Run `*atdd` before dev                                                      | Implement story, referencing checklist/tests               | Failing acceptance tests + implementation checklist                     |
| Post-Dev          | Apply `*automate`, (Optional) `*test-review`, re-run `*trace`, `*nfr-assess` if needed | Resolve gaps, update docs/tests                            | Regression specs, quality report, refreshed coverage matrix, NFR report |
| Release           | (Optional) `*test-review` for final audit, Run `*gate`                                 | Product Owner `*assess-project-ready`, share release notes | Quality audit, Gate YAML + release summary                              |

<details>
<summary>Execution Notes</summary>

- Lead with `*trace` so remediation plans target true coverage gaps. Ensure `*framework` and `*ci` are in place early in the engagement; if the brownfield lacks them, run those setup steps immediately after refreshing context.
- `*test-design` should highlight regression hotspots, mitigations, and P0 scenarios.
- Use `*atdd` when stories benefit from ATDD; otherwise proceed to implementation and rely on post-dev automation.
- After development, expand coverage with `*automate`, optionally review test quality with `*test-review`, re-run `*trace`, and close with `*gate`. Run `*nfr-assess` now if non-functional risks weren't addressed earlier.
- Use `*test-review` to validate existing brownfield tests or audit new tests before gate.
- Product Owner `*assess-project-ready` confirms the team has artifacts before handoff or release.

</details>

<details>
<summary>Worked Example – “Atlas Payments” Brownfield Story</summary>

1. **Context Refresh:** Analyst reruns `*product-brief`; PM executes `*plan-project` to update PRD, analysis, and `epics.md`; Architect triggers `*solution-architecture` capturing legacy payment flows.
2. **Baseline Coverage:** TEA executes `*trace` to record current coverage in `docs/qa/assessments/atlas-payment-trace.md`.
3. **Risk and Design:** `*test-design` flags settlement edge cases, plans mitigations, and allocates new API/E2E scenarios with P0 priorities.
4. **Story Prep:** Scrum Master generates `stories/story-1.1.md` via `*create-story`, automatically pulling updated context.
5. **ATDD First:** TEA runs `*atdd`, producing failing Playwright specs under `tests/e2e/payments/` plus an implementation checklist.
6. **Implementation:** Dev pairs with the checklist/tests to deliver the story.
7. **Post-Implementation:** TEA applies `*automate`, optionally `*test-review` to audit test quality, re-runs `*trace`, performs `*nfr-assess` to validate SLAs, and closes with `*gate` marking PASS with follow-ups.

</details>

### Enterprise / Compliance Program (Level 4)

| Phase               | Test Architect                                                   | Dev / Team                                     | Outputs                                                    |
| ------------------- | ---------------------------------------------------------------- | ---------------------------------------------- | ---------------------------------------------------------- |
| Strategic Planning  | -                                                                | Analyst/PM/Architect standard workflows        | Enterprise-grade PRD, epics, architecture                  |
| Quality Planning    | Run `*framework`, `*test-design`, `*nfr-assess`                  | Review guidance, align compliance requirements | Harness scaffold, risk + coverage plan, NFR documentation  |
| Pipeline Enablement | Configure `*ci`                                                  | Coordinate secrets, pipeline approvals         | `.github/workflows/test.yml`, helper scripts               |
| Execution           | Enforce `*atdd`, `*automate`, `*test-review`, `*trace` per story | Implement stories, resolve TEA findings        | Tests, fixtures, quality reports, coverage matrices        |
| Release             | (Optional) `*test-review` for final audit, Run `*gate`           | Capture sign-offs, archive artifacts           | Quality audit, updated assessments, gate YAML, audit trail |

<details>
<summary>Execution Notes</summary>

- Use `*atdd` for every story when feasible so acceptance tests lead implementation in regulated environments.
- `*ci` scaffolds selective testing scripts, burn-in jobs, caching, and notifications for long-running suites.
- Enforce `*test-review` per story or sprint to maintain quality standards and ensure compliance with testing best practices.
- Prior to release, rerun coverage (`*trace`, `*automate`), perform final quality audit with `*test-review`, and formalize the decision in `*gate`; store everything for audits. Call `*nfr-assess` here if compliance/performance requirements weren't captured during planning.

</details>

<details>
<summary>Worked Example – “Helios Ledger” Enterprise Release</summary>

1. **Strategic Planning:** Analyst/PM/Architect complete PRD, epics, and architecture using the standard workflows.
2. **Quality Planning:** TEA runs `*framework`, `*test-design`, and `*nfr-assess` to establish mitigations, coverage, and NFR targets.
3. **Pipeline Setup:** TEA configures CI via `*ci` with selective execution scripts.
4. **Execution:** For each story, TEA enforces `*atdd`, `*automate`, `*test-review`, and `*trace`; Dev teams iterate on the findings.
5. **Release:** TEA re-checks coverage, performs final quality audit with `*test-review`, and logs the final gate decision via `*gate`, archiving artifacts for compliance.

</details>

## Command Catalog

| Command        | Workflow README                                   | Primary Outputs                                                     | Notes                                            |
| -------------- | ------------------------------------------------- | ------------------------------------------------------------------- | ------------------------------------------------ |
| `*framework`   | [📖](../workflows/testarch/framework/README.md)   | Playwright/Cypress scaffold, `.env.example`, `.nvmrc`, sample specs | Use when no production-ready harness exists      |
| `*ci`          | [📖](../workflows/testarch/ci/README.md)          | CI workflow, selective test scripts, secrets checklist              | Platform-aware (GitHub Actions default)          |
| `*test-design` | [📖](../workflows/testarch/test-design/README.md) | Combined risk assessment, mitigation plan, and coverage strategy    | Handles risk scoring and test design in one pass |
| `*atdd`        | [📖](../workflows/testarch/atdd/README.md)        | Failing acceptance tests + implementation checklist                 | Requires approved story + harness                |
| `*automate`    | [📖](../workflows/testarch/automate/README.md)    | Prioritized specs, fixtures, README/script updates, DoD summary     | Avoid duplicate coverage (see priority matrix)   |
| `*trace`       | [📖](../workflows/testarch/trace/README.md)       | Coverage matrix, recommendations, gate snippet                      | Requires access to story/tests repositories      |
| `*nfr-assess`  | [📖](../workflows/testarch/nfr-assess/README.md)  | NFR assessment report with actions                                  | Focus on security/performance/reliability        |
| `*gate`        | [📖](../workflows/testarch/gate/README.md)        | Gate YAML + summary (PASS/CONCERNS/FAIL/WAIVED)                     | Deterministic decision rules + rationale         |
| `*test-review` | [📖](../workflows/testarch/test-review/README.md) | Test quality review report with 0-100 score, violations, fixes      | Reviews tests against knowledge base patterns    |

**📖** = Click to view detailed workflow documentation

<details>
<summary>Command Guidance and Context Loading</summary>

- Each task now carries its own preflight/flow/deliverable guidance inline.
- `tea-index.csv` maps workflow needs to knowledge fragments; keep tags accurate as you add guidance.
- Consider future modularization into orchestrated workflows if additional automation is needed.
- Update the fragment markdown files alongside workflow edits so guidance and outputs stay in sync.

</details>

## Why TEA is Architecturally Different

TEA is the only BMM agent with its own top-level module directory (`bmm/testarch/`). This intentional design pattern reflects TEA's unique requirements:

<details>
<summary><strong>Unique Architecture Pattern & Rationale</strong></summary>

### Directory Structure

```
src/modules/bmm/
├── agents/
│   └── tea.agent.yaml          # Agent definition (standard location)
├── workflows/
│   └── testarch/               # TEA workflows (standard location)
└── testarch/                   # Knowledge base (UNIQUE!)
    ├── knowledge/              # 19+ reusable test pattern fragments
    ├── tea-index.csv           # Centralized knowledge lookup
    └── README.md               # This guide
```

### Why TEA Gets Special Treatment

TEA uniquely requires **extensive domain knowledge** (19+ fragments: test patterns, CI/CD, fixtures, quality practices), a **centralized reference system** (`tea-index.csv` for on-demand fragment loading), and **cross-cutting concerns** (domain-specific patterns vs project-specific artifacts like PRDs/stories). Other BMM agents don't require this architecture.

</details>
