<!-- INVARIANT: examples are single-line HTML comments so a fresh template parses to total=0 (MEMORY_EMPTY). Do NOT un-comment or split across lines. t100 guards this. -->
> This file is maintained by the orchestrator during stage execution. Add observations at the gate ritual, not by editing here directly.

## Interpretations
<!-- example: 2026-05-29T10:14:32Z — chose REST over GraphQL; the consuming team only needs CRUD, revisit if subscriptions land -->

## Deviations
<!-- example: 2026-05-29T10:14:32Z — skipped the optional caching layer the stage prose suggested; the dataset is small enough that it adds risk -->
- 2026-08-12T02:40:00Z — team-practices.md/project.md mandate all 6 confirmed test tools as blocking CI checks with no exception. User explicitly approved a one-time deviation for Storybook only (continue-on-error in ci.yml) pending the known @storybook/nextjs+Next.js compatibility fix (project.md DECIDED, code-generation stage). This is a live, acknowledged gap against the Mandated rule, not a silent override — documented in ci-config.md/quality-gates.md.

## Open questions
<!-- example: 2026-05-29T10:14:32Z — confirm the retention window with compliance before the next stage hardens the schema -->
- 2026-08-12T02:40:00Z — MagicPod acceptance-test job is scaffolded in ci.yml but disabled (`if: false`) since no real MagicPod organization/project/API token exists yet. Needs real credentials + a real test plan before it can run; carry forward to whoever owns CI/CD secrets configuration post-workflow.

## Tradeoffs
<!-- example: 2026-05-29T10:14:32Z — picked TDD over BDD this run; the team is unit-first and the domain is well-understood -->

## Open questions
<!-- example: 2026-05-29T10:14:32Z — confirm the retention window with compliance before the next stage hardens the schema -->
