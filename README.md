# llm-agent-security-principles

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/shimo4228/llm-agent-security-principles) [![GitMCP](https://img.shields.io/endpoint?url=https://gitmcp.io/badge/shimo4228/llm-agent-security-principles)](https://gitmcp.io/shimo4228/llm-agent-security-principles) [![View Code Wiki](https://assets.codewiki.google/readme-badge/static.svg)](https://codewiki.google/github.com/shimo4228/llm-agent-security-principles)

An [Agent Skill](https://agentskills.io/specification) holding the **structural security playbook for autonomous LLM agents**. Web-app security (OWASP, auth, input validation) does not cover the threats that matter most here: prompt injection, capability escalation through memory, and blast-radius accumulation. The core insight: you cannot reliably restrict an LLM's behavior at runtime — you must shape the system so dangerous behaviors are structurally impossible.

## Install

### Claude Code

```bash
# Copy into your global skills directory
cp -r skills/llm-agent-security-principles ~/.claude/skills/llm-agent-security-principles
```

### SkillsMP

```bash
/skills add shimo4228/llm-agent-security-principles
```

## The Three Principles

| Principle | One line | Audit test |
|-----------|----------|-----------|
| Security by Absence | Dangerous capabilities are never implemented, not restricted | `grep` for `subprocess`/`eval`/`urlopen` returns zero hits outside a reviewed adapter |
| Untrusted Content Boundary | All accumulated state — including the agent's own distilled output — is untrusted on read-back | Every read goes through validate-and-wrap; validation fails closed, never filters silently |
| One External Adapter | One agent process gets at most one pinned external side-effect surface | A generic HTTP client is not "one adapter" — it is arbitrary HTTP in a costume |

The first two principles plus deterministic scaffolding-layer prohibition form the **prohibition-strength hierarchy**: absence > scaffolding enforcement > untrusted boundary. Walk it top-down; drop a layer only when the current one genuinely cannot hold the capability.

## What Else Is Inside

- Supporting defense patterns: credential handling, HTTP client hardening (`allow_redirects=False` on authenticated sessions), LLM host allowlisting, config validation with fail-closed defaults, network isolation
- A 9-item anti-patterns checklist (e.g. "restriction layers instead of absence", "trusting distilled memory", "cross-adapter agent")
- 8 design review questions to run against any capability-adding PR

## When It Triggers

- Designing a new agent or adding a capability to an existing one
- Reviewing agent architecture for prompt injection resistance
- Auditing a self-improving harness whose memory feeds back into prompts

## Curation model

This repository is **manually curated**, not script-synced: it is the generalized publication of principles enforced operationally inside the [Contemplative Agent](https://github.com/shimo4228/contemplative-agent) project. Improvements flow here editorially, the same way the paired ADRs were extracted.

## About this skill

This skill is a design-pattern skill from the [Agent Attribution Practice (AAP)](https://github.com/shimo4228/agent-attribution-practice) research line — harness-neutral ADRs on accountability distribution for agent systems ([DOI 10.5281/zenodo.19652013](https://doi.org/10.5281/zenodo.19652013)). It is the "how" counterpart to [AAP ADR-0001..0004](https://github.com/shimo4228/agent-attribution-practice/tree/main/docs/adr) (Security by Absence, Deterministic Prohibition at the Scaffolding Layer, Untrusted Content Boundary, Single External Adapter). AAP is one of three research lines by [@shimo4228](https://github.com/shimo4228), alongside the [Agent Knowledge Cycle (AKC)](https://github.com/shimo4228/agent-knowledge-cycle) ([DOI 10.5281/zenodo.19200726](https://doi.org/10.5281/zenodo.19200726)) — a six-phase bidirectional growth loop for agent-operator intent alignment — and [Contemplative Agent](https://github.com/shimo4228/contemplative-agent) ([DOI 10.5281/zenodo.19212118](https://doi.org/10.5281/zenodo.19212118)) — autonomous agents grounded in four contemplative axioms.

## License

MIT
