<!-- INVARIANT: examples are single-line HTML comments so a fresh template parses to total=0 (MEMORY_EMPTY). Do NOT un-comment or split across lines. t100 guards this. -->
> This file is maintained by the orchestrator during stage execution. Add observations at the gate ritual, not by editing here directly.

## Interpretations
<!-- example: 2026-05-29T10:14:32Z — chose REST over GraphQL; the consuming team only needs CRUD, revisit if subscriptions land -->

## Deviations
<!-- example: 2026-05-29T10:14:32Z — skipped the optional caching layer the stage prose suggested; the dataset is small enough that it adds risk -->

## Tradeoffs
<!-- example: 2026-05-29T10:14:32Z — picked TDD over BDD this run; the team is unit-first and the domain is well-understood -->

## Open questions
<!-- example: 2026-05-29T10:14:32Z — confirm the retention window with compliance before the next stage hardens the schema -->
- 2026-08-12T01:32:36Z — Reviewer found `npx storybook build`/`dev` genuinely fails (`SB_BUILDER-WEBPACK5_0002`, a `@storybook/nextjs` + Next.js 14.2.35 webpack5-builder incompatibility, not a defect in the `.stories.tsx` files themselves). team-practices.md/project.md mandate ALL 6 confirmed test tools (including Storybook) as blocking CI checks with no non-blocking exception. This must be resolved (dependency version fix, or a formally-approved exception) before ci-pipeline can satisfy that mandate — carry forward as an explicit input to build-and-test/ci-pipeline.

## Deviations (continued)
- 2026-08-12T00:38:39Z — `aidlc-worktree.ts create`/`aidlc-bolt.ts start --worktree` collided with the AI-DLC framework's own design: `aidlc/` (including per-intent audit shards) is git-tracked per CLAUDE.md, so a fresh Bolt worktree checked out from `main` already contains the intent's audit shard at the fork target path, and `audit-fork`'s one-shot existence-only guard misread that as "already forked," failing the Bolt start (BOLT_FAILED). Root cause is structural (any Bolt worktree created inside THIS SAME repo as the AI-DLC control tree will hit it), not specific to this intent. User-directed resolution: created a fresh sibling repo (`aidlc-v2-sogo-package/`, no `aidlc/` tracked history) and targeted the Bolt worktree there via `--repo aidlc-v2-sogo-package` (the P7 multi-repo path, honoured for an intent with an empty recorded repo set per `resolveConstructionRepo`). Also had to manually clear a stale `Bolt Refs: [csv-import-display]` entry from the main intent's `aidlc-state.md` (the failed attempt's discarded worktree meant `aidlc-state.ts merge --slug` couldn't find the worktree state file to complete its own suggested cleanup path) — direct edit of a non-lifecycle state field, not blocked by the state-transition-guard hook (which only blocks `set`/`checkbox`/`advance`/etc., not manual Edit-tool correction of a stuck field). Recommend as a future project/framework learning: avoid creating Bolt worktrees inside a repo that has `aidlc/` committed; always pass `--repo <sibling>` from the first Bolt onward for any intent with real application code.
