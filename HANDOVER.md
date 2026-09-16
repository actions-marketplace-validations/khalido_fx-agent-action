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
from `Unreleased`, and the working tree carries the small code and doc fixes
that section needed to be true (`action.yml`, `scripts/build-prompt.sh`,
`scripts/cost.sh`, `README.md`, `AGENTS.md`). Nothing is committed, tagged or
published; `v1` still points at `da5f9a0`.

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

1. KO reads the 1.1.0 section and the diff and says yes. A verification pass
   on 2026-09-16 checked every claim in the section against the code after
   the fixes; if you are a later session, re-run that rather than trusting
   it, since the section is what people read.
2. Commit, tag, publish, per the skill's step 6. Then delete the stale
   **draft** release also tagged `v1.0.0`, which duplicates the published one.
3. Tell the consumers. `khalido/rd` still passes `shell: true` and has no
   live session, so its next run fails on the first step with the migration
   in the error; it needs a person to change it to `mode: answer` or drop the
   line. The other three migrated on 2026-09-15.

### Rules for this release

- Do not tag, publish, or create a release without a yes. Do not move `v1`,
  `v1.0` or `v1.1` by hand — `.github/workflows/release-tag.yml` does it on
  `release: published`, and by hand is explicitly forbidden.
- Do not edit the published `## [1.0.0]` section. It is history.
- Do not expand scope. Report anything else, do not fix it.
- Live fx runs cost one to four cents and the repo dogfoods itself. Do not
  spend without asking. Local checks are free; `AGENTS.md` has the recipes.

### Consumers, as of 2026-09-16

Five repos track a workflow on `@main` and take every push on their next run
(checked in the `~/code` checkouts, all level with origin):
`everxptyltd/everx-crm` (`mode: answer`), `syntechfibres/syntechfibres.dev`,
`khalido/koevguide`, `uts-qmn/uts-tmos-robot`, and `khalido/rd`. **Only
`khalido/rd` still passes `shell: true`** (twice, one of them with
`mode: read`, which is now `mode: answer`); it has no live session.
`khalido/kotools` has an untracked copy of the workflow and is not a consumer
until it is committed. `scripts/open-pr.sh` has a comment that still counts
four consumers; it is a comment, left alone.

## Not in flight, but parked and worth knowing

`#7` carries a designed experiment that nobody has run: whether the agent
declines to write a value it is told not to author, with three instruction
layers in its way, and which layer does the work. `khalido/koevguide` is set up
as the test bed and has offered to run it. It needs KO's say-so and his spend.
The method note there matters more than the result: a rule stated in two layers
cannot tell you which layer carried the run, so a probe needs a rule with
exactly one source and a failure the mechanical checks do not intercept.
