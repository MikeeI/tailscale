# GitHub Issue, Comment, and Pull Request Format

## Authority

This file is the single source of truth for preparing Tailscale issues, comments, and pull requests.
`ISSUES-PRS.md` is the sole source of truth for finding IDs, delivery mode, lifecycle status, evidence, and locations.

- Investigate first, then let the user choose Issue or Pull request for each finding.
- Default to proposal-only mode until the user explicitly selects a delivery mode and exact target.
- Issue mode reports the finding without implementing it unless the user separately requests a fix.
- Pull request mode authorizes scoped implementation, verification, commit, push, and pull-request preparation.
- Show every external issue, comment, review, discussion, and pull-request draft to the user before publication.
- Publish externally only after the user explicitly approves the exact draft and target.
- Write GitHub issues and comments in friendly, concise English.
- Communicate with the user in the language of the surrounding conversation.
- Apply `skill-maintainer-communication` before drafting or publishing external communication.
- Verify source claims against current `upstream/main`, not only the fork branch.
- Follow `.github/CONTRIBUTING.md` by filing a bug first and discussing the design for new or changed functionality before investing heavily in implementation.
- Follow `docs/commit-messages.md` and use `git commit --signoff` for public-fork and upstream-contribution commits.
- Send potential security vulnerabilities only through the private contact in `SECURITY.md` and do not draft them for public publication.

## Finding IDs and Duplicate Prevention

- Every ledger entry has one permanent ID in the form `ISSUE-YYYY-NNN`.
- `YYYY` is the UTC year first recorded, and `NNN` is that year's sequence with at least three digits.
- `Next finding ID: ISSUE-YYYY-NNN` is the only allocator and MUST name the next unused ID.
- Immediately before adding a finding, re-read the full ledger from one current snapshot.
- Search titles, summaries, symbols, locations, and root causes for an existing owner.
- If the same root cause exists, update that entry and never allocate a duplicate.
- For a new root cause, assign the allocator value on its first Hold or Drafted entry.
- Increment the allocator in the same edit.
- The ledger check does not replace upstream issue, pull request, review, comment, discussion, and source-history searches.
- Never reuse, renumber, or scope IDs by subsystem, status, session, or finding family.
- IDs persist through Hold, Drafted, Implementing, Ready, Published, Closed, and Rejected states.
- External numbers and URLs belong in `Location` and never replace the internal ID.
- At the first finding of a UTC year, start `ISSUE-YYYY-001` and never alter older IDs.
- Entry headings use `### ISSUE-YYYY-NNN — <area>: <specific title>`.

## Ledger Entry Contract

Every `ISSUES-PRS.md` entry MUST contain these fields:

- `Status`: Hold, Drafted, Implementing, Ready, Published, Closed, or Rejected.
- `Delivery mode`: Issue, Pull request, or Undecided.
- `Location`: the external URL or `Not published.`.
- `Evidence class`: Observed, Source-proven, Assumed, Not measured, or a precise combination.
- `Internal priority`: High, Medium, or Low for local ordering only.
- `Confidence`: High, Medium, or Low based on evidence strength and not impact.
- `Type`: the duplicated decision, algorithm, mapping, validation, error, output, structure, networking, protocol, lifecycle, or orchestration family.
- `Publication target`: new issue, existing issue comment, new pull request, pull request comment, discussion, or Undecided.
- `Summary`: the concrete root cause and affected behavior.
- `Evidence`: exact paths, lines, symbols, commands, outputs, history, or API contracts.
- `Shared change pressure`: why the copies have one reason to change.
- `Impact`: observed impact, source-proven invariant, or an explicit not-measured statement.
- `Proposed direction`: the smallest consolidation that could remove repeated decision ownership.
- `Risks and boundaries`: behavior that must remain distinct and abstraction costs to avoid.
- `Verification`: the narrowest checks that would prove the proposed contract.
- `Missing publication evidence`: prior-art, reproduction, benchmark, operator, or maintainer context still required.

Internal priority and confidence MUST NOT be copied into upstream communication.
A ledger entry may be detailed while an upstream report remains concise.

## Lifecycle States

### Hold

Use Hold when any required publication evidence is missing.
Hold is mandatory when the finding is source-only, prior-art research is incomplete, or consolidation cost is not yet justified.

### Drafted

Use Drafted only after research is complete and an exact issue or pull-request plan and target exist.
A Drafted entry remains unpublished until the user approves its delivery mode, exact draft, and target.

### Implementing

Use Implementing only for user-selected Pull request mode while the scoped fix is in progress.
Record the branch and verification state in the entry without replacing `Location`.

### Ready

Use Ready when a selected pull-request change is implemented, verified, committed, pushed, and ready to publish.

### Published

Use Published only after successful external publication.
Record the exact issue, comment, review, discussion, or pull-request URL in `Location` immediately.

### Closed

Use Closed when the external report or underlying problem is resolved, declined, superseded, or confirmed duplicate.
Record the disposition without rewriting the historical finding ID.

### Rejected

Use Rejected when investigation disproves the root cause or shows that consolidation is worse than the duplication.
Keep the evidence explaining the rejection so the same false positive is not rediscovered.

## Required Research

Before drafting anything:

1. Read `ISSUES-PRS.md` and use its current finding ID, lifecycle status, target, and location.
2. Confirm that no ledger entry already owns the same symptom or root cause.
3. Read current upstream contribution guidance, security guidance when applicable, and the applicable issue template.
4. Search open and closed Tailscale issues for the symptom, root cause, subsystem, relevant platform, and relevant symbols.
5. Search open and merged Tailscale pull requests for changes that own or introduced the relevant code.
6. Search relevant discussions, documentation, and support context when they may contain reproduction or design context.
7. Read every plausible issue, comment, pull request, review, discussion, and current diff in full.
8. Verify source claims against current `upstream/main`, relevant build tags, and pinned dependency contracts.
9. Reproduce user-visible behavior when the report would claim a bug rather than a source invariant.
10. Test the relevant operating-system, privilege, network, and control-plane boundary when the finding depends on one.
11. Benchmark performance, allocation, network, or resource impact before claiming meaningful performance cost.
12. Record which effects are Observed, Source-proven, Assumed, or Not measured.
13. Decide whether a new issue, issue comment, pull-request comment, discussion, or Hold is the correct target.

A search result is only a candidate target.
A matching symbol, subsystem, or symptom does not prove ownership of the same root cause.
A similar implementation in another repository, platform, build tag, or product is a research lead and never proves Tailscale behavior.

## DRY Finding Contract

A DRY finding requires all of the following:

- At least two live copies implement the same decision, rule, algorithm, mapping, or workflow.
- The copies share one realistic reason to change.
- A plausible maintenance path can change one copy without the others.
- Consolidation is simpler than keeping the copies synchronized.
- The proposed owner has one clear responsibility and preserves required contextual differences.

Record these candidate-strength factors:

- Copy count and exact locations.
- Identical or changed-token distance.
- Shared owner and bounded context.
- Caller or consumer overlap.
- Change-history evidence.
- Current divergence, if any.
- Correctness or maintenance risk.
- Abstraction cost and coupling risk.
- Focused verification path.

Clone detectors, text search, AST matches, and similar names only produce candidates.
They never prove shared change pressure or justify an abstraction by themselves.

Reject these patterns unless independent evidence establishes shared ownership:

- Build-tag, operating-system, or architecture implementations whose platform contracts evolve independently.
- Protocol, local API, control-plane, or cloud-provider adapters whose external contracts evolve independently.
- Framework wiring, imports, logging, generated structure, test fixtures, mocks, migrations, vendored code, and archived code.
- DTO or wire-format mirrors required by a transport, persistence, or protocol boundary.
- Small obvious idioms with no realistic divergence cost.
- Similar error handling with different recovery or diagnostic ownership.
- Abstractions that require broad mode flags, conditional feature hooks, or pass-through wrappers.

Prefer deleting weaker copies or extracting a pure shared decision over adding inheritance or a generic framework.

## Issue, Comment, or Pull Request Decision

Use this decision order for every finding, then follow the user's selected delivery mode.

### Comment on an existing open issue

Comment when all of the following are true:

- The issue describes the same observable problem or root cause.
- The new information materially advances diagnosis, evidence, reproduction, or resolution.
- The comment will not redirect the issue to an unrelated maintenance or architecture topic.

Do not comment merely because the same feature, error string, package, or API appears.
Do not hijack an issue whose actual cause differs.

### Comment on an existing open pull request

Comment when all of the following are true:

- The pull request changes the exact lifecycle, function, invariant, or owner involved.
- The finding identifies an actionable correctness, ownership, or regression gap in the current diff.
- The comment fits the current scope or is explicitly marked as an optional follow-up.

Do not ask the author to absorb unrelated cleanup.
State explicitly when no scope expansion is requested.
Do not create a competing pull request unless the user explicitly selects Pull request mode for this finding.

### Open a new issue

Open a new issue when any of the following is true:

- No open issue or pull request owns the same root cause.
- Existing matches are closed, historical, tangential, or based on a different cause.
- The finding needs durable tracking beyond a temporary pull-request discussion.
- A merged pull request provides relevant history but no active discussion target.

Link relevant historical issues and pull requests without reopening or hijacking them.
Use one issue per independent root cause.

### Prepare a new pull request

Prepare a pull request when all of the following are true:

- The user explicitly selects Pull request mode for this finding.
- The root cause, callers, failure modes, and required behavior are verified against current `upstream/main`.
- The fix is smaller and easier to review than leaving the duplicated decision in place.
- Existing issues and pull requests do not already contain an active competing implementation.
- A focused local check can prove every changed observable contract.

Base the contribution branch on current `upstream/main`.
Keep fork-only documentation, ledgers, configuration, and personal commits out of the upstream pull request.
Use one coherent root cause per pull request.
Record implementation, verification, commit, push, and final pull-request location in the same ledger entry.

### Hold the finding without reporting it

Do not publish when any of the following is true:

- Reachability, root cause, currentness, or consolidation value is unverified.
- The claim is only a clone or static pattern without realistic change pressure.
- The proposed target is merely similar rather than directly relevant.
- Existing discussion already contains the same evidence.
- The only support is speculative severity or unmeasured production impact.
- The suggested abstraction may cost more than synchronized explicit code.

Keep the detailed ledger entry and state exactly which evidence is missing.

### Ask the user

Use the interactive ask mechanism when:

- Two external targets are materially plausible and choosing one risks thread hijacking.
- Issue, comment, discussion, or pull request has meaningful visibility, ownership, or implementation trade-offs.
- The user has not selected Issue or Pull request delivery mode.
- Required reproduction data, disclosure text, implementation scope, or publication scope is missing.
- Publication is requested and the exact draft has not been approved.

Do not ask about the external target within an already selected mode when repository and upstream research make it clear.
Recommend the safest target when presenting a choice within that selected mode.

## Evidence Contract

Every report must label its evidence honestly.

- Observed: reproduced behavior with command, version, environment, and output.
- Source-proven: current control flow, API ownership, or deterministic data flow proves the invariant.
- Assumed: a premise required by the claim has not been verified.
- Not measured: latency, throughput, allocation, resource growth, network cost, or user impact lacks measurement.

Rules:

- Never convert a source-proven invariant into observed user impact.
- Never claim a current functional divergence when only maintenance risk is established.
- Never call a repeated block a DRY violation without proving shared change pressure.
- Never claim performance value without representative measurement.
- Never use internal priority or confidence labels in upstream communication.
- Use exact `path:line`, symbol, field, error, API, protocol, build-tag, and commit names where they disambiguate the claim.
- Link issues and pull requests when they establish design intent or historical ownership.
- State when persisted state, daemon lifecycle, client behavior, local IPC, control-plane protocol, network data plane, and public API effects differ.

## Duplicate-Search Statement

Every proposed report must include the applicable exact statement after its question and before involvement text.

For a new issue:

```text
I checked all relevant issues, comments, pull requests, and discussions; this report is not a duplicate.
```

For an existing issue or pull-request comment:

```text
I checked all relevant issues, comments, pull requests, and discussions; this evidence is not already reported.
```

Make this confirmation only after completing the required research and reading every plausible candidate in full.
If a plausible candidate is unavailable or unread, keep the finding on Hold.

## Tone Contract

- Start with appreciation when commenting on another contributor's work.
- Use neutral phrases such as `I noticed`, `I may be missing context`, and `Would it make sense`.
- Describe code behavior and observed operations instead of author intent or competence.
- Ask one concrete question when maintainer input is needed.
- Avoid blame, demands, alarmism, sarcasm, and rhetorical severity.
- Keep one root cause and one requested decision per report.
- Do not tag maintainers or previous authors unless they already participate or the user explicitly approves it.
- State the selected involvement clearly as report-only for Issue mode or scoped implementation for Pull request mode.

## New Issue Format

Use the official GitHub issue form when it requires named fields.
Map the content below into the closest fields instead of fighting the form.

### Title

```text
<area>: <specific observed or source-proven problem>
```

Title rules:

- Name the affected area, such as `ipn/ipnlocal`, `wgengine`, `control`, or `cmd/tailscale`.
- State the concrete problem rather than the proposed implementation.
- Avoid severity words, speculation, and generic titles such as `code duplication`.

### Body

```markdown
## Summary

<One concise paragraph describing the observed or source-proven problem.>

## Evidence

- `<path:line>`: <specific control-flow, protocol, mapping, lifecycle, or ownership evidence>.
- <Relevant reproduction, command output, commit, issue, or pull request link>.

## Duplication topology

- Copies: <count and exact owners>.
- Shared change pressure: <one reason these copies change together>.
- Current divergence: <observed or source-proven difference, or none established>.

## Impact

<Observed impact with measurement, or an explicit statement that user impact is not measured.>

## Proposed direction

<Smallest ownership change that removes repeated decision work without hiding contextual differences.>

## Risks and boundaries

<Behavior that must remain distinct and abstraction costs to avoid.>

## Verification

- <Narrow existing test, reproduction, review, or search proving the changed contract>.

## Question

<One concrete question about ownership, expected behavior, or preferred direction.>

I checked all relevant issues, comments, pull requests, and discussions; this report is not a duplicate.

## Involvement

I am reporting this finding only and am not currently proposing a pull request.
```

### Bug form additions

When using the bug form, include every applicable required field:

- The observed issue and expected behavior.
- Exact and deterministic reproduction steps.
- Recent changes that may have introduced the behavior.
- Operating system and version.
- Tailscale version.
- Relevant networking, security, or other software.
- The `tailscale bugreport` identifier when available and appropriate.

Do not use a bug form for a source-only maintainability finding without reproduced behavior.

### Feature form additions

When using the feature-request form, explain the user problem, candidate solution, impact of no change, alternatives, and relevant context.
Do not present a source-only maintainability finding as a user feature request without a source-proven user need.

## New Pull Request Format

Use this format only after the user selects Pull request mode and the implementation is complete.

### Title

```text
<area>: <specific user-visible or maintainability fix>
```

### Body

```markdown
## Summary

<One concise paragraph describing the root cause and the scoped fix.>

## Evidence

- `<path:line>`: <specific source or reproduced behavior that established the problem>.
- <Relevant issue, prior pull request, command output, benchmark, or API contract>.

## Changes

- <Concrete ownership or behavior change>.
- <Important behavior intentionally left unchanged>.

## Risks and boundaries

- <Operating-system, network, protocol, persistence, lifecycle, or API behavior that remains distinct>.
- <Why this change avoids a broader abstraction or unrelated cleanup>.

## Verification

- `<focused command>` — <observed result>.

I checked all relevant issues, comments, pull requests, and discussions; this pull request is not a duplicate.
```

Do not include `FORMAT.md`, `ISSUES-PRS.md`, or fork-only `AGENTS.md` changes in an upstream pull request.
Use the current upstream commit style, DCO signoff, and `Fixes` or `Updates` footer requirements for each contributing commit.

## Existing Issue Comment Format

```markdown
Hi, thanks for documenting this.

I noticed one additional detail in the current implementation:

- `<path:line>`: <specific evidence>.
- <Observed or source-proven consequence>.

This appears to share the issue's root cause because <precise ownership link>.

Would it make sense to <one scoped question or direction>?

I checked all relevant issues, comments, pull requests, and discussions; this evidence is not already reported.

I am reporting this finding only and am not currently proposing a pull request.
```

## Existing Pull Request Comment Format

```markdown
Hi, thanks for working on this.

I noticed one edge in the current diff:

- `<path:line or diff hunk>`: <specific evidence>.
- <Observed or source-proven consequence>.

This does not require expanding the current scope unless you consider it part of the same invariant.

Would it make sense to <one concrete question>?

I checked all relevant issues, comments, pull requests, and discussions; this evidence is not already reported.

I am reporting this finding only and am not currently proposing a pull request.
```

## Condensed Output Contract

When presenting candidate reports to the user, use this stable order:

```text
ID: ISSUE-YYYY-NNN
Status: Hold | Drafted | Implementing | Ready | Published | Closed | Rejected
Mode: Issue | Pull request | Undecided
Target: new issue | issue comment | new pull request | pull request comment | discussion | undecided
Evidence: Observed | Source-proven | Assumed | Not measured
Title: <draft title>
Root cause: <one sentence>
Impact: <observed, source-proven, or not measured>
Missing: <remaining evidence or none>
Location: <URL or Not published.>
```

Do not dump raw search output or duplicate the full ledger entry unless requested.

## Publication Gate

Before publishing, verify every item:

- The exact finding ID exists in `ISSUES-PRS.md`.
- The ledger status and allocator are current.
- The user selected Issue or Pull request mode.
- Current `upstream/main` still contains the relevant behavior.
- Every plausible prior-art candidate was read fully.
- The selected target owns the same root cause.
- The evidence class matches the actual proof.
- Claims distinguish current behavior, maintenance risk, assumptions, and unmeasured impact.
- The proposed direction is smaller than the duplication it removes.
- Issue mode contains one root cause and one requested decision.
- Pull request mode contains one coherent, verified implementation.
- The user approved the exact draft and target.
- The duplicate-search statement is truthful.
- The ledger is updated to Published immediately after successful publication.

## Prohibited Actions

- Never publish without explicit approval of the exact draft and target.
- Never choose Issue or Pull request mode on the user's behalf.
- Never implement a finding while its delivery mode is Issue or Undecided.
- Never open a pull request before its entry is Ready and the exact pull-request draft is approved.
- Never report clone-detector output as proof.
- Never report boilerplate, generated code, tests, migrations, or intentional boundary mirrors as DRY defects.
- Never inflate source-proven maintenance risk into observed user harm.
- Never split one root cause across multiple IDs.
- Never combine independent root causes into one report.
- Never publish internal priority, confidence, adversarial review notes, or severity labels upstream.
