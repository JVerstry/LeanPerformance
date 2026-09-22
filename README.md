# LeanPerformance

A free audit prompt to accelerate Lean code build time.

## Purpose

Lean/Lake build times can balloon in ways that aren't obvious from reading
the code — a cosmetic doc-comment edit can trigger a multi-minute rebuild
cascade, a proof split for "parallelism" can end up slower, a default
tactic can silently dominate a file's compile time. [`LeanPerformanceAudit.txt`](LeanPerformanceAudit.txt)
is a single, self-contained prompt that walks an AI assistant through
auditing a Lean project for these kinds of issues and reporting back a
prioritized, plain-language list of concrete fixes — grounded in
measurements taken on the project itself, not assumptions.

## No installation package — just hand over the prompt

There is nothing to install and no package to add as a dependency.
`LeanPerformanceAudit.txt` is meant to be copied (or pointed to) and given
directly to an AI assistant running inside your own Lean project, so it has
access to your project's files and can run `lake`/`lean` commands against
them. For example, with Claude Code, Cursor, or a similar coding
assistant open in your Lean project's directory, paste the contents of
`LeanPerformanceAudit.txt` as your prompt (or ask the assistant to read the
file if you've copied it into your project) and let it run the audit.
