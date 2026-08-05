**Collaborator:** aidlc-devsecops-agent

## Contribution

Assessment scope: lint/format rules, SAST/DAST, secret and dependency
scanning, and supply-chain controls the human interview must resolve — for
this project's actual shape (Next.js/React/TypeScript frontend, Hono API,
Prisma ORM, Aurora PostgreSQL; deploy-on-merge to staging with a manual gate
to production per `org.md`; CSV file ingestion of 単位数表マスタ reference
data, validated against 19 columns, persisted to the DB, then
searched/displayed).

**Confirmed evidence (matches lead's Evidence.md):** no `.eslintrc*`,
`.prettierrc`, CI workflow files, dependency-scanning config, or secret-
scanning config exist anywhere in the repo. This is a genuine greenfield
security posture — nothing to inherit, everything must be decided at the
interview.

**Gaps the lead's draft does not surface, that the interview must resolve:**

1. **Lint/format — security-relevant rule sets, not just style.** The draft's
   Code Style section only proposes generic Prettier/ESLint. For a
   TypeScript/React/Hono stack handling file uploads, the interview should
   confirm whether `eslint-plugin-security` (or equivalent) and
   `typescript-eslint`'s strict/type-checked config are adopted, since this
   is a CSV-parsing surface (injection via malformed CSV content, formula
   injection if any field is later exported/opened in spreadsheet software).

2. **SAST — not mentioned in the draft at all.** No SAST tool (Semgrep,
   SonarQube, CodeGuru) is proposed anywhere in `team-practices.md`. For a
   greenfield TS/Hono/Prisma stack, Semgrep (fast, TS-friendly, low ops
   overhead) should be the concrete interview option; this must be decided
   before build-and-test, not deferred silently.

3. **DAST — no target defined.** `org.md`'s deploy-on-merge-to-staging
   posture (confirmed by the draft's Deployment section) gives a natural
   DAST trigger point (post-deploy-to-staging, pre-promotion-to-production),
   but the draft does not propose this. The interview should decide whether
   DAST (e.g. OWASP ZAP baseline scan) runs against staging as a gate before
   the manual production promotion, given this is the first opportunity in
   the pipeline to catch runtime issues in the CSV-upload endpoint and the
   search/display API.

4. **Secret scanning — entirely absent from the draft.** No pre-commit or
   CI secret-detection tool (Gitleaks, git-secrets, GitHub secret scanning)
   is proposed. Aurora PostgreSQL credentials and any DB connection strings
   are a concrete, current-project secret-exposure risk (Prisma
   `DATABASE_URL` in `.env`); this must be a named interview decision, not
   left to convention.

5. **Dependency/supply-chain scanning — no npm-ecosystem tool named.** The
   draft does not propose Dependabot/Snyk/`npm audit` for the Next.js/Hono/
   Prisma dependency tree, nor an SBOM. Given deploy-on-merge to staging,
   a dependency-scan gate belongs in CI before merge, not only at release
   time — the interview should fix the tool and the fail-threshold
   (Critical/High).

6. **File-upload-specific validation — not addressed by any section.**
   The scope is CSV file ingestion with 19-column validation. Beyond
   business-rule validation (already scoped in scope-definition), the
   interview should confirm technical input-hardening controls: max file
   size, MIME/content-type check, row-count/size ceiling to prevent
   resource-exhaustion (DoS) from a malicious or malformed CSV, and that
   validation happens server-side (Hono handler) not just client-side —
   this is a concrete STRIDE (Denial of Service, Tampering) concern for this
   feature that the draft's five org.md-derived sections don't surface
   because they're generic, not feature-specific.

7. **CI security-gate placement vs. deploy-on-merge posture.** Since staging
   deploys happen on merge to `main` (trunk-based, no long-lived branches),
   SAST/secret/dependency scans must run on the PR before merge — there is
   no later checkpoint before staging exposure. The interview should confirm
   this ordering explicitly rather than leaving it implicit.

None of these are hard project constraints yet (no ALWAYS/NEVER to add to
`discovered-rules.md` at this draft step) — they are unresolved gaps the
human interview should turn into explicit tool/gate decisions.

## Positions

- OBJECT: `team-practices.md`'s "Code Style" section frames code style as
  purely stylistic (Prettier/ESLint formatting), but for this stack/feature
  it should also surface security-relevant lint tooling (e.g.
  `eslint-plugin-security`, strict TS config) as an interview option —
  omitting it risks the interview never raising SAST-adjacent lint controls
  at all.
- OBJECT: None of the five `team-practices.md` sections (or `discovered-
  rules.md`) mention SAST, DAST, secret scanning, or dependency/supply-chain
  scanning anywhere, even as a "pending confirmation" placeholder like the
  other sections use — the interview step (Step 4) currently has no
  structured prompt to raise these, so they would silently default to
  "nothing" unless added as explicit questions.
- AGREE: The Deployment section's deploy-on-merge-to-staging /manual-gate-
  to-production framing (`org.md` default, correctly carried over) is the
  right anchor point for where DAST and dependency/secret gates should sit
  in the pipeline once those tools are chosen.
- AGREE: Evidence.md's greenfield-confirmation (no CI, lint, or scanning
  config present anywhere in the repo) is accurate and matches what I
  independently expect for this stage of the project.
