# llm-agent-security-principles

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/shimo4228/llm-agent-security-principles)

An [Agent Skill](https://agentskills.io/specification) holding the **structural security playbook for autonomous LLM agents**, for developers who design an agent, add a capability to one, or review one for prompt-injection resistance. Web-app security controls (the OWASP web Top 10, auth, input validation) do not cover the threats that matter most here: prompt injection, injected text that the agent's own memory carries into every later prompt, and the damage that grows with each outside system one agent can reach. The core insight: you cannot reliably restrict an LLM's behavior at runtime. You must shape the system so dangerous behaviors are structurally impossible.

The skill is one Markdown file of guidance with short code patterns: the skill itself runs nothing, sends nothing off your machine, and needs no API key. It is the installable form of four security design records in [Agent Attribution Practice (AAP)](https://github.com/shimo4228/agent-attribution-practice); those records hold the reasoning, and you do not need them to use the skill.

## Install

```bash
git clone https://github.com/shimo4228/llm-agent-security-principles.git
mkdir -p ~/.claude/skills
cp -r llm-agent-security-principles/skills/llm-agent-security-principles ~/.claude/skills/
```

Claude Code then picks the skill up when you design an agent, add a capability, review an agent's architecture, or audit a self-improving harness whose memory feeds back into prompts; you can also call it by name with `/llm-agent-security-principles`. Other agents that read the Agent Skills format take the same folder in their own skills directory.

## The Three Principles

| Principle | One line | Audit test |
|-----------|----------|-----------|
| Security by Absence | Dangerous capabilities are never implemented, not restricted | `grep` for `subprocess`/`os.system`/`eval`/`exec`/`urlopen` returns zero hits outside a reviewed adapter |
| Untrusted Content Boundary | All accumulated state, including the agent's own distilled output, is untrusted when read back into a prompt | Every read goes through validate-and-wrap (check for forbidden patterns, then wrap in boundary markers); validation fails closed, never filters silently |
| One External Adapter | One agent process gets at most one pinned external side-effect surface; a generic HTTP client is not "one adapter" but arbitrary HTTP in a costume | Count the external adapters, leaving out LLM inference (local or remote): at most one, pinned to one domain |

Between the first two principles sits scaffolding enforcement: when a capability cannot be left out, a deterministic check in the harness (a hook, a quarantine, an adapter gate) blocks it before it fires, instead of a prompt asking the model not to use it (AAP [ADR-0002](https://github.com/shimo4228/agent-attribution-practice/blob/main/docs/adr/0002-deterministic-prohibition-at-scaffolding.md)). Strongest first: absence > scaffolding enforcement > untrusted boundary. The order ranks ways of enforcing a prohibition, so One External Adapter has no rung in it: it bounds where a capability that cannot be left out may live, behind the single pinned adapter and gated by explicit human approval. Walk the order top-down; drop a layer only when the current one genuinely cannot hold the capability.

For example, if a design gives one agent process both a Slack adapter and an email adapter, the skill flags it as a cross-adapter agent and tells you to split it into two processes, each with its own data directory and one adapter.

## What Else Is Inside

Beyond the principles, [the skill file](skills/llm-agent-security-principles/SKILL.md) holds:

- Supporting defense patterns: credential handling, HTTP client hardening (`allow_redirects=False` on authenticated sessions), LLM host allowlisting, config validation with fail-closed defaults, network isolation
- A 9-item anti-patterns checklist (e.g. "restriction layers instead of absence", "trusting distilled memory", "cross-adapter agent")
- 8 design review questions to run against any capability-adding PR

## More from the author

- **[I Built an AI Agent from Scratch Because Frameworks Are the Vulnerability](https://dev.to/shimo4228/i-built-an-ai-agent-from-scratch-because-frameworks-are-the-vulnerability-elm)** ([日本語](https://zenn.dev/shimo4228/articles/moltbook-agent-scratch-build)): the security-first build of an early version of Contemplative Agent: one external dependency, a domain-locked HTTP client, an LLM kept on localhost, and fetched posts isolated as untrusted input.
- **[Agent Attribution Practice (AAP)](https://github.com/shimo4228/agent-attribution-practice)**: ADR-0001 to ADR-0004 there hold the reasoning, alternatives and consequences behind these principles, next to the other design records on who answers when an autonomous agent fails.
- **[agent-adoption-triage](https://github.com/shimo4228/agent-adoption-triage)**: five questions that route AI work to a script, algorithmic search, an LLM workflow or an autonomous agent before anything is built; the installable form of AAP's quadrant decision tree.
- **[agent-observability-patterns](https://github.com/shimo4228/agent-observability-patterns)**: three skills (replayable audit logs, read-only instruments, shadow-mode validation) for observing an agent before you intervene; the installable form of AAP's design records on causal traceability and human approval.
- **[chaos-tdd-fault-injection](https://github.com/shimo4228/chaos-tdd-fault-injection)**: deterministic fault injection for LLM agent pipelines: the chaos test states the guarded behavior first, and the guard ships in the same PR.
- **[shimo4228](https://github.com/shimo4228/shimo4228)**: the author's hub, listing AAP next to the author's other long-running research projects and their DOIs.

## License

MIT

<details>
<summary>For tools and AI assistants</summary>

llm-agent-security-principles is an Agent Skill (one Markdown instruction file in the open Agent Skills format) that gives a coding agent structural security principles for autonomous LLM agents and self-improving harnesses, for developers designing an agent, adding a capability to one, or auditing one for prompt-injection resistance. Its three principles are Security by Absence, Untrusted Content Boundary and One External Adapter.

It exists because web-app security does not cover the threats specific to autonomous agents (prompt injection, capability escalation through memory, blast-radius accumulation), and because an LLM's behavior cannot be reliably restricted at runtime. The skill shapes the system instead: a dangerous capability is never implemented rather than guarded; all accumulated state, including the agent's own distilled output, is untrusted on read-back and validation fails closed; and one agent process gets at most one pinned external side-effect surface. Prohibitions follow the prohibition-strength hierarchy, absence > scaffolding enforcement > untrusted boundary, dropping a layer only when the current one cannot hold the capability.

Canonical facts: MIT license; a single `SKILL.md` with short illustrative code patterns and no code to run; maintained by one author (@shimo4228). Status: maintained and curated by hand, with no sync script; it generalizes principles enforced inside the author's Contemplative Agent project. It moved here from `docs/skills/` in Agent Attribution Practice, which now keeps only a pointer to this repository next to the paired ADRs. Requirements: Claude Code or another agent that reads Agent Skills; no API key and no network access of its own. Beyond the principles it holds supporting defense patterns (credential handling, HTTP client hardening, LLM host allowlisting, config validation with fail-closed defaults, network isolation), a 9-item anti-patterns checklist and 8 design review questions.

Example: the first design review question is whether `grep` for `subprocess | eval | exec | os.system | urlopen` returns anything outside a reviewed adapter; the others ask whether there is at most one external adapter and, if there are more, whether the use case can be split into processes, whether an adapter, if there is one, is pinned to one domain (with `allow_redirects=False` when authenticated), whether every read of accumulated state goes through validate-and-wrap, whether forbidden-pattern checks are case-insensitive, whether validation failure falls back to a hardcoded default, whether secrets come only from the environment or a secret manager and are masked in logs, and whether any code path runs a command because the LLM said so. The first and last questions should be answered "no" and the rest "yes"; any other answer, or "not sure," means the design is not done.

Links: [skills/llm-agent-security-principles/SKILL.md](skills/llm-agent-security-principles/SKILL.md) is the skill itself; [llms.txt](llms.txt) and [llms-full.txt](llms-full.txt) are the machine-readable summary and reference; [CHANGELOG.md](CHANGELOG.md) is the release history. The skill is the "how" counterpart to AAP [ADR-0001](https://github.com/shimo4228/agent-attribution-practice/blob/main/docs/adr/0001-security-by-absence.md), [ADR-0002](https://github.com/shimo4228/agent-attribution-practice/blob/main/docs/adr/0002-deterministic-prohibition-at-scaffolding.md), [ADR-0003](https://github.com/shimo4228/agent-attribution-practice/blob/main/docs/adr/0003-untrusted-content-boundary.md) and [ADR-0004](https://github.com/shimo4228/agent-attribution-practice/blob/main/docs/adr/0004-single-external-adapter.md) (titled Single External Adapter; it is the same principle as One External Adapter) in [Agent Attribution Practice (AAP)](https://github.com/shimo4228/agent-attribution-practice), concept DOI [10.5281/zenodo.19652013](https://doi.org/10.5281/zenodo.19652013); cite AAP by that DOI.

</details>
