# Team Practices — DRAFT (Step 2: Lead Draft)

> Status: **DRAFT / PROPOSED**. This is a greenfield project with no prior
> team affirmation. Every value below is copied from `org.md`'s framework
> defaults and is a **proposal pending human confirmation** at the
> practices-discovery interview step. Nothing here should be treated as
> affirmed until the human explicitly confirms, edits, or overrides it.

## Way of Working

**Proposed (org.md default):** Trunk-based development. All work merges to
`main` via short-lived feature branches (typically resolved within 1-2
days); we avoid long-lived branches. For Construction worktrees, the
worktree base branch is `main` and the merge target is `main`. If the
project requires multiple environments (staging, production), we still
keep one trunk and gate releases via tags or environment-specific
deployment configs — not long-lived release branches. We squash-merge Bolt
branches into `main`: each Bolt becomes one commit on the trunk, named by
the Bolt slug, with full Bolt commit history preserved on the source
branch until the worktree is discarded.

*Pending confirmation: does this greenfield project (Next.js/React/
TypeScript, Hono, Prisma, Aurora PostgreSQL) want to affirm trunk-based +
squash-merge as-is, or specialize it?*

## Walking Skeleton

**Proposed (org.md default):** When practices are scope-dependent, run the
walking-skeleton Bolt first only when the active scope file declares
`skeleton: on`; Bolt 1 is solo, gated, and the user explicitly approves
before remaining Bolts run. Skip the skeleton ceremony when the scope file
declares `skeleton: off` — the first Bolt then runs like any other. After
Bolt 1 ships (when it runs), the orchestrator fires the ladder prompt
("continue autonomously" vs "gate every Bolt"); the choice persists as
`Construction Autonomy Mode` in `aidlc-state.md`.

*Pending confirmation: no scope file examined yet at this step — the
`skeleton:` flag for this CSV import/display feature scope needs to be
checked/confirmed during the interview.*

## Testing Posture

**Proposed (org.md default):** Tests are a first-class deliverable in
every Bolt. Default per scope: `mvp`, `enterprise`, `feature`, `infra` →
tests written alongside code, minimum 80% line coverage, tests run in CI
before merge; `bugfix`, `security-patch` → regression test for the
specific issue, existing suite stays green; `poc`, `refactor`, `workshop`
→ existing suite stays green, no new test floor required.

*Pending confirmation: prior intent-capture/scope-definition context
mentions Vitest, Testcontainers, Storybook, MSW, Playwright, and MagicPod
as the intended tooling — this needs to be affirmed as the concrete
testing-posture specialization for this project (tool selection is not an
org.md default; it must be human-confirmed here).*

## Deployment

**Proposed (org.md default):** Deploy on merge to staging environments.
Production deploys gate on a separate manual approval — typically tech
lead + product owner sign-off in CodePipeline or a CD platform's
environment protection. Continuous deployment to production is a team
decision, not a framework default.

*Pending confirmation: target infra appears to be Aurora PostgreSQL per
prior context — deployment target/pipeline specifics need confirmation,
not assumed here.*

## Code Style

**Proposed (org.md default):** Defer to project-level configuration —
Prettier (JS/TS) as formatter, ESLint as linter, run in CI before merge
(failure blocks the PR), language-idiomatic naming (camelCase for
JS/TS). Agents read the project's linter config first; a framework
code-style suggestion only fires if the linter doesn't already cover it.

*Pending confirmation: no `.prettierrc`/ESLint config exists yet in this
repo (greenfield, no application code) — these will need to be
established, likely during/after this stage, and then affirmed here.*
