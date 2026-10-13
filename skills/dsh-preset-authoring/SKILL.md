---
name: dsh-preset-authoring
description: Author or edit a DeepSeek Harness agent preset (agent.cordis.yml + optional preset.yml) with verified row structure, id rules, install location, and validation against a real harness install.
whenToUse: Use when a user asks to create, extend, or fix a DSH preset, agent composition, or "configure my agent like X" — referencing @deepseek-ai/dsh-agent-presets@0.1.1-rc.2 mechanics.
metadata: dsh-ecosystem
---

# DSH Preset Authoring

1. A preset is one directory: `agent.cordis.yml` (required composition) plus optional `preset.yml` (display metadata: `name`, `description`, numeric `order`; `order` sorts the roster).
2. Name the directory (the preset id) `[a-z0-9][a-z0-9-]*` kebab-case — discovery skips any other id outright, and authoring APIs reject it.
3. `agent.cordis.yml` is a top-level YAML list of rows `{id, name, config?}`; every `name` is an `@deepseek-ai/*` package (with optional `config`) or `cordis:group` whose `config` is a recursive row list.
4. Take each row and its config fields verbatim from an official shipped preset or an install you validated — never invent field names; unverified rows fail the package-existence gate or break the boot.
5. Install under the harness-home user root `~/.dsh/.agent-presets/<id>/` (files are the only composition editor; `copy()` is the API alternative and never overwrites).
6. Validate against a real install: `node tools/validate-presets.mjs --harness <dsh node_modules root>` from awesome-dsh-presets — YAML (incl. `!!js` dialect) parses, rows carry `id`+`name`, `cordis:group` recurses, every referenced `@deepseek-ai/*` package exists.
7. Expect live discovery: `list()`/`resolve()` re-read roots per call, and each session tracks its composition file's mtime/size stamp — a new session picks up your edit, an already-joined session keeps the generation it started on.
8. A directory that is broken (missing/unreadable `agent.cordis.yml`) is listed with its reason, occupying the id: fix the file or delete the directory; do not shadow it with a duplicate.
9. Switching presets applies only to a blank session (nothing produced yet); a produced session answers `agent-preset-locked` — do not claim a switch happened.
10. Deliver the preset directory, the validation output against the harness version you used, and one blank-session verification that it boots with the intended tools.
