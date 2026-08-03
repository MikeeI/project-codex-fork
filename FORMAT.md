# GitHub Issue and Comment Reporting Format

## Authority

This file is the single source of truth for reporting findings to the upstream `openai/codex` project.
`ISSUES.md` is the sole source of truth for finding IDs, lifecycle status, and published locations.

- The goal is to report findings and materially useful evidence, not to produce unsolicited code changes.
- Never create, draft, offer, or propose a pull request unless an upstream maintainer first invites one.
- Never implement a reported fix unless a later user instruction and an upstream invitation authorize it.
- Default to proposal-only mode and show every complete draft to the user before publication.
- Publish externally only after the user explicitly approves the exact draft and target.
- Explicit user direction may authorize an unmeasured source-only potential finding under the exception below.
- It never waives required research, exact-draft approval, truthful form completion, or uncertainty labels.
- Write GitHub issues and comments in friendly, concise English.
- Communicate with the user in the language of the surrounding conversation.

These rules implement the issue-first policy in `docs/contributing.md`, which says unsolicited pull requests are closed.

## Finding IDs and Duplicate Prevention

- Every ledger entry has one permanent ID in the form `ISSUE-YYYY-NNN`.
- `YYYY` is the UTC year first recorded; `NNN` is that year's sequence with at least three digits.
- `Next finding ID: ISSUE-YYYY-NNN` is the only allocator and names the next unused ID.
- Immediately before adding a finding, re-read the full ledger from one current snapshot.
- Search every title, summary, symbol, and location for the same symptom or root cause.
- If the same root cause exists, update that entry; never allocate a duplicate.
- For a new root cause, assign the allocator value on its first Hold or Drafted entry.
- Increment the allocator in the same edit.
- The ledger check does not replace upstream issue, pull request, discussion, and release searches.
- Never reuse, renumber, or scope IDs by crate, component, status, or session.
- IDs persist through every lifecycle state; external numbers belong in `Location`.
- At the first finding of a UTC year, start `ISSUE-YYYY-001`; never alter older IDs.
- Entry headings use `### ISSUE-YYYY-NNN — <area>: <specific title>`.

## Finding Lifecycle

Use exactly one lifecycle status for each `ISSUES.md` entry:

- `Hold` means reporting may still be appropriate, but research, evidence, drafting, target selection, or approval remains open.
- `Drafted` means an exact report or comment draft exists and awaits approval or publication.
- `Published` means the approved report or comment was posted; `Location` records its exact URL.
- `Duplicate` means an existing upstream issue owns the same root cause and no separate report should be opened.
- `Retired` means completed research shows the finding should not be reported because it is invalid, obsolete, intentional, or not actionable.

`Duplicate` and `Retired` are terminal reporting decisions, but their IDs and research records remain permanent.
Move a `Duplicate` back to `Hold` only when new evidence would materially advance the existing upstream issue.
Move a `Retired` finding back to `Hold` only when changed source or materially new evidence invalidates the retirement reason.
For `Duplicate`, set `Location` to `Existing upstream issue: <URL>.`
For `Retired`, use `Location: Not published.` and preserve the retirement reason in `Status`.
After exactly three summary bullets, an entry may include a non-bulleted `Research:` line with the audit date, pinned source, candidates read, search coverage, and target rationale.
Research records prevent repeated prior-art work, but changed upstream source still requires a focused currentness check.

## Required Research

Before drafting anything:

1. Read `ISSUES.md` and use its current finding ID, lifecycle status, target, and published location.
2. Confirm that no ledger entry already owns the same root cause.
3. Read the current upstream contribution guide and applicable issue form.
4. Search open and closed issues by symptom, root cause, component, error text, and relevant symbols.
5. Search open and merged pull requests for changes that own or introduced the relevant code.
6. Search Codex discussions and release notes for the same behavior or design decision.
7. Read every potentially relevant issue, comment, pull request, review, discussion, and current diff.
8. Verify source claims against current upstream code and pin links to the audited commit.
9. Record which effects are observed, source-proven, assumed, or not yet measured.
10. Confirm whether the latest released Codex CLI still exhibits a user-visible bug before using a bug form.

A search result is only a candidate target.
A matching component or symbol does not prove that an existing thread owns the same root cause.

## User-Requested Source-Only Potential Findings

An explicit user request to notify maintainers without measuring may qualify a finding for this exception.
It authorizes a source-only proposal, not publication and not a claim of observed impact.

Every qualifying report must satisfy all of these conditions:

- Current source proves the reachable root cause or control flow.
- A realistic frequency and non-trivial cost mechanism establish why the finding may matter.
- The opening states that the reporter found a potential performance concern in current source, not a confirmed bug.
- The impact section starts with `Not measured.` and identifies the latency, throughput, allocation, or load still unknown.
- The report includes commit-pinned code evidence, a realistic scenario, and one actionable maintainer question.
- Required duplicate research is complete, and the selected issue form can be answered truthfully.
- The report covers one root cause and preserves all normal publication and user-approval gates.

This exception permits a useful source-only heads-up when the user explicitly values maintainer awareness over measurement.
It does not permit generic static-analysis output, unsupported severity, or inferred production impact.

## Issue or Comment Decision

Use this decision order for every finding.

### React to an existing issue

Use only a thumbs-up reaction when the existing issue already contains the same problem and no new evidence is available.
This follows the Codex issue forms and avoids duplicate confirmation comments.

### Comment on an existing open issue

Comment only when all of the following are true:

- The issue describes the same observable problem or root cause.
- The new information materially advances reproduction, diagnosis, evidence, or resolution.
- The comment will not redirect the issue to an unrelated performance or architecture topic.

Do not comment merely because the same crate, error string, API, or feature appears.
Do not repeat evidence already present in the thread.

### Comment on an existing open pull request

Comment only when all of the following are true:

- The pull request changes the exact lifecycle, function, or invariant involved.
- The finding identifies a correctness, ownership, resource, or concurrency gap in the current diff.
- The comment is actionable within the current scope or explicitly marked as information only.

Do not ask the author to absorb unrelated work.
State explicitly when no scope expansion is requested.
Never create or offer a competing pull request.

### Open a new issue

Open a new issue when all applicable conditions hold:

- No open issue or pull request owns the same root cause.
- Existing matches are closed, historical, tangential, or based on a different cause.
- The finding has observed behavior or a source-proven invariant with a realistic, non-speculative cost mechanism.
- The finding needs durable tracking beyond a temporary pull request discussion.

Link relevant historical issues and pull requests without reviving or hijacking them.
Use one issue per independent root cause.

### Hold the finding without reporting it

Do not publish when any of the following is true:

- Reachability, root cause, currentness, or affected release is unverified.
- The claim is only a static pattern without realistic cost or behavior evidence.
- A performance claim has neither reproduction nor measurement nor deterministic proof and lacks the source-only exception.
- The proposed target is merely similar rather than directly relevant.
- The report would duplicate information already present in the target thread.
- A plausible prior-art candidate could not be fully read.
- Required issue-form information is unavailable and cannot be established from the environment.

Keep the ledger entry on Hold and state exactly which evidence is missing.
Use `Duplicate`, not `Hold`, when an existing upstream issue owns the exact root cause.
Use `Retired`, not `Hold`, when completed research establishes a durable decision not to report the finding.

### Ask the user

Use the interactive ask mechanism before proceeding when:

- Two targets are materially plausible and choosing one risks thread hijacking.
- The choice between a new issue and a comment has meaningful visibility or scope trade-offs.
- Required reproduction data, subscription details, disclosure text, or publication scope is missing.
- The requested action would publish externally and the exact draft has not been approved.

Do not ask when research makes the correct target clear.
Recommend the safest target when presenting a choice.

## Evidence Contract

Every report must label its evidence honestly:

- Observed: reproduced behavior with command, version, environment, and relevant output.
- Source-proven: current control flow, data flow, or ownership proves the invariant.
- Assumed: a necessary premise that has not been verified.
- Not measured: latency, throughput, allocation, request count, or resource growth lacks measurement.

Rules:

- Never convert a source-proven invariant into observed user impact.
- Never call something a leak without deterministic lifetime evidence or resource-growth measurement.
- Never claim a network request occurs when it only may occur for some route, cache, or protocol state.
- Never use internal severity labels such as Critical, High, Medium, or Low upstream.
- Use exact `path:line`, symbol, field, error, command, and API names where they disambiguate the claim.
- Pin source links to the audited commit when branch movement could invalidate them.
- State search coverage without claiming exhaustiveness when result caps or unavailable channels leave gaps.
- A source-only potential finding must state `Impact: Not measured.` before describing any plausible cost mechanism.
- Describe what the current code does and what it may cause; never state that users experience an unobserved effect.

## Duplicate-Search Statement

Every proposed report includes the applicable exact statement after its question and before disclosure.

For a new issue:

```text
I checked all relevant issues, comments, pull requests, discussions, and release notes; this report is not a duplicate.
```

For an existing issue or pull request comment:

```text
I checked all relevant issues, comments, pull requests, discussions, and release notes; this evidence is not already reported.
```

Use this statement only after completing the required research and reading every plausible prior-art candidate.
If a plausible candidate is unavailable or unread, hold the finding instead.

## Tone Contract

- Start with appreciation when commenting on another contributor's work.
- Use neutral phrases such as `I noticed`, `I may be missing context`, and `Would it make sense`.
- Describe code behavior, not author intent or competence.
- Ask one concrete question when maintainer input is needed.
- Avoid blame, demands, alarmism, sarcasm, and rhetorical severity.
- Keep one root cause and one requested decision per report.
- Do not tag maintainers or previous authors unless they already participate or the user approves it.
- State that no pull request is planned because Codex contributions are invitation-only.

## New Issue Format

Use the official GitHub issue form and map evidence into its named fields.
Do not replace required form fields with a free-form body.

### Title

```text
<area>: <specific observed or source-proven problem>
```

Title rules:

- Name the affected surface or crate, such as `tui`, `app-server`, `mcp-server`, `exec`, or `sdk`.
- State the concrete problem, not the proposed implementation.
- Avoid severity words, speculation, and generic titles such as `performance issue`.

### CLI bug form

Use `.github/ISSUE_TEMPLATE/3-cli.yml` for a reproduced Codex CLI bug.
Provide every required or applicable field:

- latest tested Codex CLI version from `codex --version`
- subscription type
- model, when relevant
- platform details using the issue form's requested command
- terminal and multiplexer, when relevant
- `codex doctor --json`, reviewed for sensitive data, or `not available`
- actual behavior
- deterministic reproduction steps and thread ID, when applicable
- expected behavior
- additional evidence, source links, uncertainties, and the reporting footer

Do not use the CLI bug form for a source-only performance hypothesis without reproducible behavior.

### Other bug form

Use `.github/ISSUE_TEMPLATE/4-bug-report.yml` for reproduced bugs outside the CLI-specific form.
Provide actual behavior, deterministic reproduction, expected behavior, and relevant evidence.
Do not use the other bug form for a source-only potential finding without reproducible behavior.

### Feature request form

Use `.github/ISSUE_TEMPLATE/5-feature-request.yml` only when the request is genuinely an enhancement.
A source-proven performance concern is not automatically a feature request.
Under the source-only exception, this form may request a concrete, behavior-preserving optimization.
Every required field must still be answered truthfully.
Without explicit user direction or a realistic cost mechanism, keep the finding on Hold.

### Additional information field

For a new issue, place this structure in the most suitable free-text field when the form permits it:

```markdown
## Evidence

- `<commit-pinned path:line>`: <specific control-flow, lifecycle, or ownership evidence>.
- <Relevant command, output, API contract, issue, pull request, or discussion link>.

## Impact

<Observed impact with measurement, or an explicit statement that the impact has not been measured.>

## Question

<One concrete question about ownership, expected behavior, or the preferred direction.>

I checked all relevant issues, comments, pull requests, discussions, and release notes; this report is not a duplicate.

I am reporting this finding only and am not proposing a pull request unless a maintainer invites one.

### Disclosure

Investigated thoroughly with <model display name> (<reasoning effort> reasoning effort), using [Oh My Pi](https://github.com/can1357/oh-my-pi) as the agent framework.

This report is not generic or unreviewed AI-generated output. Its claims were checked against the cited evidence, and it includes the relevant detail intended to help maintainers resolve the issue.

If reports like this are not useful to the project, please let me know and I will refrain from submitting similar ones. My intent is to help without wasting maintainer time or energy or discouraging their work.

Thank you for your work.
```

Fill the model and reasoning-effort placeholders from the active runtime before presenting the draft.
Never fabricate unavailable environment, subscription, model, reproduction, or diagnostic details.

## Existing Issue Comment Format

```markdown
Hi, thanks for documenting this.

I noticed one detail that may be relevant to the same root cause:

- `<commit-pinned path:line>`: <new evidence>.
- <Why this evidence changes or strengthens the current diagnosis>.

<One concise question or proposed next diagnostic step.>

I checked all relevant issues, comments, pull requests, discussions, and release notes; this evidence is not already reported.

I am only reporting this finding and am not proposing a pull request unless a maintainer invites one.

### Disclosure

Investigated thoroughly with <model display name> (<reasoning effort> reasoning effort), using [Oh My Pi](https://github.com/can1357/oh-my-pi) as the agent framework.

This report is not generic or unreviewed AI-generated output. Its claims were checked against the cited evidence, and it includes the relevant detail intended to help maintainers resolve the issue.

If reports like this are not useful to the project, please let me know and I will refrain from submitting similar ones. My intent is to help without wasting maintainer time or energy or discouraging their work.

Thank you for your work.
```

Use this only when the existing issue owns the same root cause.
Otherwise open a new issue or hold the finding.

## Existing Pull Request Comment Format

```markdown
Hi, thanks for working on this.

While reading the current diff, I noticed one possible <lifecycle, ownership, resource, or concurrency> gap:

- `<commit-pinned changed path:line>`: <specific behavior in the current diff>.
- `<related path or API contract>`: <why the current behavior may be incomplete>.

Would it make sense to <one focused question or diagnostic step>?
I may be missing ownership handled elsewhere.
I checked all relevant issues, comments, pull requests, discussions, and release notes; this evidence is not already reported.
I am not suggesting a broader scope change or a separate pull request.

### Disclosure

Investigated thoroughly with <model display name> (<reasoning effort> reasoning effort), using [Oh My Pi](https://github.com/can1357/oh-my-pi) as the agent framework.

This report is not generic or unreviewed AI-generated output. Its claims were checked against the cited evidence, and it includes the relevant detail intended to help maintainers resolve the issue.

If reports like this are not useful to the project, please let me know and I will refrain from submitting similar ones. My intent is to help without wasting maintainer time or energy or discouraging their work.

Thank you for your work.
```

Keep review comments scoped to the active diff.
Move independent findings to new issues only after user approval.

## Condensed Output Contract

When proposing reports to the user, output each candidate in this stable order:

```text
finding: ISSUE-YYYY-NNN
target: <new issue | issue #N | PR #N | none>
action: <open issue | comment | react | hold | none>
reason: <one sentence>
lifecycle: <Hold | Drafted | Published | Duplicate | Retired>
title: <new issue title, otherwise omit>
draft: <complete proposed text, otherwise omit>
missing: <required evidence, otherwise omit>
publication: proposal only; not published
```

For multiple findings, preserve permanent finding IDs and never combine independent root causes.

## Publication Gate

Before publishing, verify every item:

- The target still exists and its state has not changed.
- The finding ID, lifecycle status, target, and published location match `ISSUES.md`.
- The draft matches the latest source, released behavior, or current pull request diff.
- The report adds information not already present.
- Every material claim has evidence or an explicit uncertainty label.
- The selected official issue form is appropriate and every required field is complete.
- The tone is friendly, concise, and non-accusatory.
- The report contains no pull request offer or implementation commitment.
- The exact disclosure footer is the final section.
- The applicable duplicate statement is present and every plausible candidate was fully read.
- A source-only potential finding records the explicit user direction and labels its impact as not measured.
- The user approved the exact target and final text.

After publication, update `ISSUES.md` with the exact target, status, and URL before returning.
Return only the created issue or comment URLs and a concise status.

## Prohibited Actions

- Never create, draft, propose, or offer a pull request without an explicit upstream maintainer invitation.
- Never implement code as part of the issue-reporting workflow.
- Never publish without explicit user approval of the exact draft and target.
- Never cross-post the same finding to multiple threads.
- Never revive a closed issue with an unrelated root cause.
- Never use an active issue as a generic component discussion.
- Never report unverified static patterns as production bugs.
- User-requested source-only findings remain prohibited when reachability, control flow, or realistic cost is unverified.
- Never hide uncertainty or fabricate measurements, commands, logs, versions, subscriptions, or maintainer intent.
- Never alter the required disclosure footer.
