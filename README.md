# LeanPerformance

A free audit prompt to accelerate Lean code build time.

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

### Option 2: install it as a skill

To avoid pasting the prompt every time, hand
[`InstallationPrompt.txt`](InstallationPrompt.txt) to your AI assistant. It
creates a small skill (three text files, no software) so that you can run the
audit afterwards with `/leanperf`.

The assistant asks you two questions first:

- **Scope**: install for all your projects, or only for the current one.
- **Audit source**: where the skill gets the audit text when it runs.

For the audit source, *local* means the skill runs from a copy of the audit saved
in its own folder. It works offline and gives reproducible results.

*Remote* means the skill fetches the latest audit from this repository on every
run. It is always current but needs network access, and it falls back to the
local copy if the fetch fails.

You can switch between the two later with `/leanperf mode`.

### Updating and removing the skill

Run `/leanperf update` to refresh the local copy of the audit, and the skill
itself, from this repository. It shows what changed and asks for your
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
