# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] — 2026-06-12

Initial release as a standalone skill repository.

### Added

- `skills/llm-agent-security-principles/SKILL.md` — structural security design principles for LLM agents: Security by Absence, Untrusted Content Boundary, One External Adapter, the prohibition-strength hierarchy (absence > scaffolding enforcement > untrusted boundary), supporting defense patterns (credentials, HTTP hardening, LLM host allowlisting, config validation, network isolation), an anti-patterns checklist, and design review questions.

### Notes

- Previously published inside the [Agent Attribution Practice](https://github.com/shimo4228/agent-attribution-practice) repository as a design-pattern skill under `docs/skills/`. Extracted here so the skill is installable on its own; the paired decision records [AAP ADR-0001..0004](https://github.com/shimo4228/agent-attribution-practice/tree/main/docs/adr) stay in the research repository.
- This repository is manually curated: it generalizes an operational variant that runs inside the [Contemplative Agent](https://github.com/shimo4228/contemplative-agent) project. There is intentionally no automated sync script.
