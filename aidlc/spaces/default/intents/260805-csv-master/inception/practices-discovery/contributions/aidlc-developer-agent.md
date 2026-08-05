**Collaborator:** aidlc-developer-agent

## Contribution

Assessed the lead draft against the confirmed stack (Next.js/React/
TypeScript, Hono API layer, Prisma/Aurora PostgreSQL, Zod, React Hook
Form, SWR/Zustand/nuqs) and the confirmed domain load: 19-column
単位数表マスタ CSV validation (all 19 columns except undefined 予備項目,
per `project.md` DECIDED) feeding an import→store→search/display
pipeline. The draft (org.md defaults only) is silent on all five areas
below; these are stack-specific and must be resolved in the interview,
not left to ad hoc implementation choice.

1. **Naming.** The CSV's canonical column labels are Japanese/half-width
   katakana (証記載保険者番号, ｻｰﾋﾞｽ種類ｺｰﾄﾞ, ｻｰﾋﾞｽ項目ｺｰﾄﾞ, etc.). The
   interview must fix: (a) the romanization/English mapping convention
   for TypeScript field names, Zod schema keys, and Prisma model/column
   names — ideally one canonical mapping table so it isn't re-derived per
   layer; (b) whether Prisma column names stay literal Japanese
   (`@map`) or use English identifiers with `@map` back to source
   headers; (c) half-width katakana normalization policy before any
   name/value comparison.

2. **Layer boundaries.** Need an explicit chain: Hono route handler
   (transport/HTTP concerns only) → CSV parse step → per-row Zod
   validation (the 19-column business rules) → domain/service layer
   (aggregates row-level results, applies the 予備項目 exclusion) →
   Prisma repository (persistence only). Also: is the Zod schema shared
   between server-side row validation and any client-side (React Hook
   Form resolver) validation, to avoid two rule sets drifting apart? The
   draft's generic "defer to project config" Code Style note doesn't
   address this at all.

3. **Error handling.** Given the success metric is 100% rejection
   compliance, the team must decide whether CSV validation is fail-fast
   (stop at first invalid row/column) or collect-all (report every
   row/column violation in one pass) — this is a behavior decision, not
   style, and drives the result-type shape. Recommend a typed
   `Result<T, ValidationError[]>` (or discriminated union) at the
   service boundary rather than throwing on expected validation failures,
   with Hono's `onError`/`HTTPException` reserved for actual system
   errors (parse failure, DB failure), not business-rule rejections.

4. **File organization.** With 19 columns each carrying independent
   rules, recommend one validation module/rule-set per column (or per
   column group) traceable back to the CSV定義表, not a single
   monolithic Zod object — testability and future column changes both
   need this. Also unresolved: feature-folder (`import/`, `search/`) vs
   layer-folder (`app/`, `services/`, `repositories/`) organization
   under Next.js app router, and where the Hono app mounts relative to
   Next.js route handlers.

5. **Code style.** Org.md's Prettier/ESLint default is fine as a floor,
   but doesn't cover TypeScript-specific choices this stack needs:
   strict mode on, `.safeParse` (not `.parse` + try/catch) for row-level
   Zod validation given the collect-all requirement above, and a
   convention for nuqs/SWR/Zustand state ownership boundaries in the
   search/display UI (query-string state vs client cache vs local UI
   state) so search filters don't get duplicated across the three.

None of this is currently captured in `team-practices.md` or
`discovered-rules.md`; all five points should be added as explicit
interview questions before Step 5 integration.

## Positions

- AGREE: The draft correctly treats every org.md value as a pending
  proposal rather than an affirmed fact, appropriate for a true
  greenfield project with no code yet.
- AGREE: `evidence.md`'s conclusion that no team practices exist yet and
  everything routes through the interview is accurate.
- OBJECT: The `## Code Style` section as drafted is purely the generic
  org.md formatter/linter/naming boilerplate and surfaces none of the
  Hono/Prisma/Zod/React-Hook-Form/SWR-Zustand-nuqs-specific naming,
  layering, error-handling, or file-organization questions above —
  without adding them, the interview will leave load-bearing structural
  decisions for this specific CSV-validation-heavy feature unasked.
