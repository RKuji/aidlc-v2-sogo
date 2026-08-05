# Evidence — DRAFT (Step 2: Lead Draft)

> Status: **DRAFT**. This documents what was inspected to produce the
> Step 2 lead draft for intent `260805-csv-master`.

## Sources Inspected

1. `aidlc/spaces/default/memory/org.md` — read all five relevant sections
   in full: `## Way of Working`, `## Walking Skeleton`, `## Testing
   Posture`, `## Deployment`, `## Code Style`. These are framework-level
   defaults, not team-affirmed facts, and were copied into
   `team-practices.md` as explicit DRAFT/proposed values.
2. Repository root (`/home/mgdoc/work/src/sogo`) — listed top-level
   contents: only `.claude/` (framework shell) and `aidlc/` (workspace)
   are present. No application source tree, no `package.json`, no `.git`
   history of application commits beyond framework/workflow scaffolding.
   Confirmed this is a genuine greenfield project with:
   - No CI configuration (no `.github/workflows/`, no `.gitlab-ci.yml`,
     no equivalent pipeline definitions)
   - No test tooling installed or configured (no `vitest.config.*`, no
     `playwright.config.*`, no Testcontainers/MSW/Storybook/MagicPod
     setup files)
   - No deployment configuration (no IaC, no CD pipeline definitions,
     no environment manifests)
   - No linter/formatter configuration (no `.prettierrc`, `.eslintrc*`,
     or equivalent)

## Not Yet Inspected (deferred to later steps)

- `aidlc/spaces/default/memory/team.md` — not consulted for this draft
  per task instructions (org.md defaults are the primary source at this
  step); team.md will matter for the affirmation/interview step.
- Prior ideation-phase artifacts (scope-definition, intent-capture) — not
  re-read for this draft per task instructions, though their known
  context (Next.js/React/TypeScript, Hono, Prisma, Aurora PostgreSQL,
  Vitest/Testcontainers/Storybook/MSW/Playwright/MagicPod) is carried
  forward as background and flagged as pending confirmation in
  `team-practices.md`.

## Conclusion at This Draft Step

Since no team practices have been established for this new project, this
draft leans entirely on org.md defaults, explicitly marked as proposals.
The human interview step must confirm, adjust, or override each of the
five sections before they are considered affirmed team practice.
