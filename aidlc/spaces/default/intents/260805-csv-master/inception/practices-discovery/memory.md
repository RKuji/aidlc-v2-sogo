<!-- INVARIANT: examples are single-line HTML comments so a fresh template parses to total=0 (MEMORY_EMPTY). Do NOT un-comment or split across lines. t100 guards this. -->
> This file is maintained by the orchestrator during stage execution. Add observations at the gate ritual, not by editing here directly.

## Interpretations
<!-- example: 2026-05-29T10:14:32Z — chose REST over GraphQL; the consuming team only needs CRUD, revisit if subscriptions land -->

## Deviations
<!-- example: 2026-05-29T10:14:32Z — skipped the optional caching layer the stage prose suggested; the dataset is small enough that it adds risk -->
- 2026-08-07T04:41:19Z — `aidlc-state.ts practices-promote` corrupted `project.md`'s `## Mandated`/`## Forbidden` sections: `discovered-rules.md`'s bullets were soft-wrapped across multiple lines (both the original English draft and the Japanese-translated revision used this style), and the promote tool appears to parse Mandated/Forbidden line-by-line rather than by markdown list-item boundary — each wrapped continuation line got its own duplicated `(affirmed <date>)` suffix and the list structure broke. Manually repaired `project.md` (and a missing blank line in `team.md`) directly after promotion, collapsing each bullet to a single unwrapped line. Did not re-run `practices-promote` (would have duplicated entries) or re-run the §13 ritual (already completed once for this stage). Future practices-discovery runs — in this project or others — should keep `discovered-rules.md` Mandated/Forbidden bullets on one physical line each to avoid re-triggering this tool bug.

## Tradeoffs
<!-- example: 2026-05-29T10:14:32Z — picked TDD over BDD this run; the team is unit-first and the domain is well-understood -->

## Open questions
<!-- example: 2026-05-29T10:14:32Z — confirm the retention window with compliance before the next stage hardens the schema -->
