---
name: csv-master-import-display
depth: Standard
keywords: []
description: CSV master-data import, validation, and selection-based display for the long-term-care insurance fee master (単位数表マスタ)
skeleton: on
---

# csv-master-import-display scope

Standard depth, composed for a greenfield feature: import the
介護予防・日常生活支援総合事業費の単位数表マスタ (long-term-care insurance
fee master data) from CSV, validate it, and expose selection-based display
on a Next.js/React/TypeScript/Hono/Prisma/Aurora Postgres stack. Composed via
the adaptive workflow composer (ARS composite 46) rather than matched to a
stock scope — no stock grid fit within tolerance.

## Why these stages, why skip those

This is a self-contained feature on a fresh codebase, so the walking-skeleton
spine runs: intent-capture and scope-definition frame the CSV import +
validation + display intent before the approval-handoff gate into inception;
practices-discovery and requirements-analysis pin down conventions and the
engineering-grade spec (field mapping, validation rules, error handling) for
a domain the team has not yet built; application-design settles the
architecture (Hono API surface, Prisma schema, import pipeline) directly,
folding units-generation/delivery-planning/functional-design since the
decomposition is small and inline-expressible; code-generation,
build-and-test, and ci-pipeline build, verify, and gate the implementation.

Market-research, feasibility, team-formation, and rough/refined-mockups are
SKIP — the target data and UI shape are already known from the master-data
spec, there is no market to research, no novel technical viability question,
and no multi-team coordination. Reverse-engineering is SKIP because the
project is greenfield with nothing yet to map. User-stories folds into
requirements-analysis (a single persona: the operator selecting master
records). NFR stages, infrastructure-design, and the full operation phase
are SKIP for this iteration — no explicit NFR beyond correctness, no new
infrastructure surface, and no deployment/observability work scoped for this
pass.

## Membership

Initialization (workspace-scaffold, workspace-detection, state-init),
intent-capture, scope-definition, approval-handoff, practices-discovery,
requirements-analysis, application-design, code-generation, build-and-test,
and ci-pipeline execute (12 of 32 stages); the rest is SKIP.
