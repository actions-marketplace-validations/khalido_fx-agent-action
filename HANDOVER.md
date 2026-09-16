# Handover

Work in flight, for the next agent or person who picks this repo up cold.
`AGENTS.md` is how to work on the action and does not go stale. This file is
what is half-done right now, and it goes stale the moment someone finishes
something.

## If you were pointed at this file, you own it

Read it, do the work, and **before you finish, leave it true**:

- **Delete what you finished.** Not a "Done" section, not a dated line — take
  it out. `git log` is the record and it is better than anything you would
  write here. This file is a model of what is outstanding, not a diary of what
  happened. (Same doctrine as the agent memory: edit in place, delete stale,
  earn its place.)
- **Add what the next one needs**, and only that: what you learned that is not
  in the code, what you tried that did not work, what you were about to do.
- **Correct what you found to be wrong.** Everything below is a claim by
  whoever wrote it, not a fact. If the code disagrees, the code wins — fix the
  line, do not work around it.
- **If nothing is in flight**, say so in one line and delete the rest. An empty
  handover is a good outcome. A stale one is worse than none, because the next
  agent will believe it.

---

## In flight: the v1.1.0 release

`CHANGELOG.md` has an uncommitted `## [1.1.0] - 2026-09-16` section, rolled
from `Unreleased`. Nothing is tagged, nothing is published, `v1` still points
at `da5f9a0`. `main` is pushed and CI is green.

The procedure is `.claude/skills/release/SKILL.md`. Follow it, including its
rule that the changelog section **is** the release notes, and its requirement
to present and stop rather than publish.

### Decided — do not relitigate

This release removes inputs (`shell`, `pr_model`) and `mode` values (`auto`,
`write`). By the release skill's own table that is a MAJOR and would be
`2.0.0`. **KO decided on 2026-09-16 to ship it as `1.1.0` and let the release
workflow move `v1` onto it**, because the action is days old, every pin the
repo advertises is `@main`, and no consumer is pinned to `@v1`. Do not propose
`2.0.0` or `0.x`. Make what ships accurate; the number is settled.

### What is left

A verification pass on 2026-09-15 checked the 1.1.0 notes against the code and
found the following. **Confirm each against the code before acting** — this
list is a report, not a fact, and it may already be stale.

False in the notes as written:

1. The section claims "no `@v1` anywhere". It is advertised in at least three
   places: `CHANGELOG.md:5`, the 1.0.0 "Pinning" paragraph, and
   `docs/guide.md:320`. The seven `@main` pins are right. This sentence is the
   whole justification for the version number, so it has to be exact — and the
   docs calling `@v1` a compatibility promise now say something this release
   makes untrue.
2. It claims `/fx pr add X` "now reads as `add X`". The verb stripping is
   gone, so the instruction is literally `pr add X`
   (`scripts/build-prompt.sh`, around the trigger parse and where the
   instruction is written).
3. The `mode: answer` rationale is inverted. The notes say the agent is never
   told to ship "so it does not spend a run building something that is thrown
   away". The prompt tells an answer-mode run to make the change to find out
   whether it works.
4. "Every run has a shell and edits" is stated unconditionally in the lead and
   the first Changed bullet, then contradicted three bullets later. `read`
   denies both.
5. The default-mode change is filed under `schedule` and `workflow_dispatch`.
   It applies to **every event**. In 1.0.0 an unset `mode` was `auto`, which
   resolved to read unless the `pr` verb appeared, so plain `/fx` questions and
   issue notes were read-only too. Now an unset `mode` gets a full shell, which
   can read `AI_GATEWAY_API_KEY` out of the environment. This is the most
   consequential change in the release and currently reads as a footnote.
   Decide where it belongs and how loudly.
6. The `workflow_dispatch` bullet calls it "how a change to the action is tried
   before it lands". True only of `.github/workflows/fx.yml`, which is
   `uses: ./`. `examples/fx.yml` pins `@main`, so a dispatch there runs the
   branch's checkout against main's action.

Code, all small:

7. `action.yml` says "Removed in 2.0.0" in the removed-input descriptions and
   in the guard's error messages, and the changelog says the removals go for
   good in 3.0.0. Neither matches a 1.1.0 release. Make code and notes agree.
8. "fx never holds a GitHub token" has one exception: `scripts/memory.sh` calls
   `scripts/cost.sh`, which runs `fx usage --json`, inside the Save memory
   step, which holds `GH_TOKEN`. A ledger read, no model call, no tools, so the
   risk is low — but either strip the token there or stop making the claim
   absolute. `AGENTS.md` currently asserts only two fx calls sit in
   token-holding steps and that both are stripped.
9. The `.agent-pr.md` contract is only reachable through the `open-pr` skill.
   `build-prompt.sh` names that skill in every agent-mode prompt, but the skill
   only reaches the runner when `skills: true`. A job with `skills: false` is
   told to use a skill that is not there, nothing else states the contract, and
   it can never open a pull request or find out why.

### Rules for this release

- Do not tag, publish, or create a release. Do not move `v1`, `v1.0` or `v1.1`
  by hand — `.github/workflows/release-tag.yml` does it on `release:
  published`, and by hand is explicitly forbidden.
- Do not edit the published `## [1.0.0]` section. It is history.
- Do not expand scope. Fix what is listed and what you find false in the 1.1.0
  notes. Report anything else, do not fix it.
- Live fx runs cost one to four cents and the repo dogfoods itself. Do not
  spend without asking. Local checks are free; `AGENTS.md` has the recipes.
- There is a stale **draft** release also tagged `v1.0.0`, duplicating the
  published one. Harmless; delete it when publishing.

### Consumers, as of 2026-09-15

Four repos ride `@main` and take every push on their next run. Three migrated
to the new inputs the day it merged: `everxptyltd/everx-crm` (`mode: answer`),
`syntechfibres/syntechfibres.dev`, `khalido/koevguide`. **`khalido/rd` has not
migrated and has no live session** — it still passes `shell: true`, so its next
run fails on the first step with the migration in the error. It needs a person.
`uts-qmn/uts-tmos-robot` was being wired and was told to re-copy the example.

## Not in flight, but parked and worth knowing

`#7` carries a designed experiment that nobody has run: whether the agent
declines to write a value it is told not to author, with three instruction
layers in its way, and which layer does the work. `khalido/koevguide` is set up
as the test bed and has offered to run it. It needs KO's say-so and his spend.
The method note there matters more than the result: a rule stated in two layers
cannot tell you which layer carried the run, so a probe needs a rule with
exactly one source and a failure the mechanical checks do not intercept.
