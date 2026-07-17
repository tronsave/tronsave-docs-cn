# CLAUDE.md

Guidance for Claude Code when working in this repository.

## Project memory (.claude/memories/)

Durable project knowledge lives in `.claude/memories/` so it travels with the repo (synced from Claude's machine-level memory on 2026-07-17). Read a file when its topic becomes relevant — do not load all of them by default. When you learn something durable about this project, add or update a file there and keep this index in sync.


Shared machine/build context (from the original dev Mac):
- [Build environment](.claude/memories/build-environment.md) — toolchain, package managers, env vars used to build the ~/ projects
- [Keep Mac awake](.claude/memories/keep-mac-awake.md) — LaunchAgent that keeps builds/servers running while the Mac is locked
