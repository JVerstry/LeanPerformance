# LeanPerformance

A free audit prompt to accelerate Lean code build time.

> **Moved:** LeanPerformance now lives in
> [LeanSkills](https://github.com/JVerstry/LeanSkills), in the `LeanPerformance/`
> folder, with its manual at
> [`LeanPerformance/MANUAL.md`](https://github.com/JVerstry/LeanSkills/blob/main/LeanPerformance/MANUAL.md).
> Development and issues continue there. This repository stays online and frozen,
> because skills installed earlier fetch their first update from the files here;
> that update repoints them to LeanSkills.

## Purpose

Lean/Lake build times can balloon in ways that aren't obvious from reading the
code. A cosmetic doc-comment edit can trigger a multi-minute rebuild cascade. A
proof split for "parallelism" can end up slower. A default tactic can silently
dominate a file's compile time.

[`LeanPerformanceAudit.txt`](LeanPerformanceAudit.txt) is a single,
self-contained prompt that walks an AI assistant through auditing a Lean project
for these kinds of issues.

The assistant reports back a prioritized, plain-language list of concrete fixes,
grounded in measurements taken on your own project rather than assumptions.

A second prompt, [`LeanStructureAudit.txt`](LeanStructureAudit.txt), is a
read-only check of your code's structure and style against the conventions of
Mathlib or CSLib: naming, documentation, layout and imports. It runs nothing and
changes nothing.

## No installation package

There is nothing to install and no package to add as a dependency. The audit is
a prompt: you hand it to an AI assistant that is running inside your own Lean
project, so it can read your files and run `lake` and `lean` commands against
them.

You have two ways to do that.

### Option 1: paste the prompt

Open Claude Code, Cursor or a similar coding assistant in your Lean project's
directory. Paste the contents of `LeanPerformanceAudit.txt` as your prompt, or
copy the file into your project and ask the assistant to read it. Then let it
run the audit.

For the structure check, do the same with `LeanStructureAudit.txt` and tell the
assistant which profile to use, `mathlib` (the default) or `cslib`.

### Option 2: install it as a skill

To avoid pasting the prompt every time, hand
[`InstallationPrompt.txt`](InstallationPrompt.txt) to your AI assistant. It
creates a small skill (four text files, plus an optional fifth if you connect a
papercuts log, no software) so that you can run the audit afterwards with
`/leanperf`.

Running `/leanperf` on its own performs the full audit: it builds and profiles
your project to measure where the time goes, then reports prioritized fixes. The
other modes below are variations on it.

The assistant asks you two questions first:

- **Scope**: install for all your projects, or only for the current one.
- **Audit source**: where the skill gets the audit text when it runs.

For the audit source, *local* means the skill runs from copies of the audit texts
saved in its own folder. It works offline and gives reproducible results.

*Remote* means the skill fetches the latest audit texts from this repository on
every run. It is always current but needs network access, and it falls back to
the local copies if the fetch fails.

You can switch between the two later with `/leanperf mode`.

### Dry runs and weekly checks

A full audit builds and profiles your project, which can take a while and uses
real CPU time. `/leanperf dry` is the light alternative: it only reads your
files, never runs `lake` or `lean`, and finishes quickly.

Its findings are leads, not conclusions. Every one is marked "unmeasured", and the
report ends with the measurements a full run would take to confirm the most
promising ones.

`/leanperf schedule` sets up a recurring check, weekly and dry by default, using
your assistant's own scheduler. It asks you to confirm the interval, the kind of
run, and that reports may be saved in a `.leanperf/` folder (which it adds to your
`.gitignore`).

Each run compares itself with the previous report and tells you only what is new,
what is gone and what changed rank, or "No change" if nothing did. A scheduled run
writes only inside `.leanperf/` and never changes your code.

To keep scheduled runs cheap, each one looks at the files changed since the last
report, plus one older top-level directory in rotation, so the whole project is
covered over time. The first run covers everything.

Scheduling needs an assistant that can run prompts on a schedule, such as Claude
Code. If yours cannot, the skill says so and stops. Run `/leanperf schedule off`
to remove the schedule.

### Checking structure and style

`/leanperf structure` checks your code against a library's conventions without
running anything. Use `/leanperf structure cslib` for CSLib; the default profile
is Mathlib. Add `internal` to also check rules that apply to Mathlib itself, such
as its copyright header, which are off by default because they rarely fit a
project that merely uses it.

Many of these checks also exist as linters that run on every build. Where your
project already switches a linter on, the skill skips its own check, so you are
not told the same thing twice. Where a linter is off, the skill runs the check
itself and recommends switching the linter on, which gives you the earliest
warning.

The check is advice, graded by how objective each rule is: mechanical rules such
as line length are reported as findings, while judgement calls are reported as
"consider". Things that need real understanding, such as whether a lemma is named
well or an API is well designed, are not checked at all.

### Applying a recommendation

A report never changes your code. When you want a recommendation applied, run
`/leanperf apply`. It applies one low-risk batch at a time: it measures the
baseline twice, makes the change, requires a full build with no new warnings, and
measures again. It keeps the change only if the improvement is clearly larger
than the measurement noise; otherwise it reverts, restoring only the files it
touched.

Every batch, kept or reverted, is recorded in `.leanperf/apply-log.md`. Kept
changes are left uncommitted for you to review. It never commits and never
pushes, and it stops after each batch to ask whether to continue.

### Accepting findings

If you decide to live with a finding, run `/leanperf accept <key> <reason>` with
the key shown in the report, for example `module-system: whole project`. It is
recorded in `.leanperf/accepted.md`, kept on your machine and not in your
repository, and the finding is not reported again. Reports say how many accepted
findings they skipped, so nothing is hidden. `/leanperf accept list` shows the
list, and `/leanperf accept remove <key>` removes an entry.

### Logging findings to a papercuts log

If you keep a papercuts log, a plain text file where you note tooling friction one
line at a time (`date · symptom · fix · project`), `/leanperf papercuts <path>`
connects it to the skill. It is off until you set it, and `/leanperf papercuts off`
disconnects it.

After a full audit, the skill then proposes log lines only for findings that were
measured in your project and are general lessons about Lean or Lake, not details of
your own proofs. It shows you the exact lines and appends them only once you
confirm. Dry runs and scheduled runs never write to the log.

### Updating and removing the skill

Run `/leanperf update` to refresh the local copies of the audit texts, and the
skill itself, from this repository. It shows what changed and asks for your
confirmation before overwriting anything.

To remove the skill, run `/leanperf uninstall`, which also asks for confirmation.
You can instead delete its folder yourself: `~/.claude/skills/leanperf/` for a
user-level install, or `.claude/skills/leanperf/` in your project for a
project-level one.

Neither updating nor removing the skill touches anything in your Lean project.

## Contributing

Contributing (submitting changes to the audit prompt) is limited to this
project's contributors.

Anyone can open or report an issue, though. If you hit a problem with the prompt
or have a suggestion, please file an issue.

By contributing to this project, you agree that your contribution is
automatically licensed under this project's license (see [LICENSE](LICENSE)),
with no separate agreement required.
