# AgentForge — verify AI-agent work before you ship it

AI agents write the code, the prompts, the reports and the run logs.
AgentForge is the family of small tools that checks their work —
twelve single-file, stdlib-only Python CLIs. No dependencies, no
network, no accounts: every tool runs offline and deterministically,
in a terminal or in CI, and answers exactly one verification
question.

| Tool | What it verifies | Try it |
|---|---|---|
| [AgentProof](https://github.com/maticcretic-commits/agentproof) | The agent actually did the work — real git changes, protected files untouched, acceptance criteria and the project's own tests checked, each pass / fail / unknown | `agentproof check` |
| [PromptRE](https://github.com/maticcretic-commits/promptre) | What the prompt actually says — persona, sections, tool references, constraints and output rules, reverse-engineered with a structure score | `promptre analyze examples/support-agent-prompt.md` |
| [AgentCI](https://github.com/maticcretic-commits/agentci) | The agent still behaves after a prompt change — saved outputs re-checked against a case suite; regressions fail the build | `agentci run examples/cases.json examples/outputs.v2.json` |
| [SecretScan](https://github.com/maticcretic-commits/secretscan) | Nothing leaked — secrets and PII in agent outputs and artifacts, caught before they ship (and redacted copies on request) | `secretscan scan examples/agent-output-with-secrets.md` |
| [CostLedger](https://github.com/maticcretic-commits/costledger) | What the runs cost — token usage tallied per run / model / tool from logs you already have; unknown models are UNKNOWN, never $0 | `costledger tally examples/sample-run.jsonl --pricing examples/pricing.yaml` |
| [SchemaGuard](https://github.com/maticcretic-commits/schemaguard) | Outputs fit the contract — saved outputs validated against a JSON Schema, with baselines inferred and contract diffs checked for breaking changes | `schemaguard validate examples/outputs --schema examples/contract.json` |
| [SkillAudit](https://github.com/maticcretic-commits/skillaudit) | A skill is safe to install — SKILL.md packages pattern-checked for overrides, exfiltration, destructive shell and hidden content, plus a quality pass | `skillaudit audit examples/hostile-skill` |
| [PromptDiff](https://github.com/maticcretic-commits/promptdiff) | What a prompt edit really changed — section- and constraint-aware diff that flags removed guardrails and weakened obligations | `promptdiff diff examples/old.md examples/new.md --fail-on-risk` |
| [TraceTape](https://github.com/maticcretic-commits/tracetape) | The run record is sound — one canonical `trace.jsonl` format, validated precisely, with run summaries over the tape | `tracetape validate examples/broken.jsonl` |
| [PromptLint](https://github.com/maticcretic-commits/promptlint) | The prompt is well-built — structure, ambiguity, contradiction candidates, hygiene and testability, scored 0–100 | `promptlint lint examples/weak.md` |
| [ClaimCheck](https://github.com/maticcretic-commits/claimcheck) | Report claims are backed — every checkable claim in an agent report must cite evidence, and every number must match it | `claimcheck check examples/report-hype.md --evidence examples/evidence` |
| [TrustBadge](https://github.com/maticcretic-commits/trustbadge) | The whole picture, honestly — the family's reports combined with published weights into one score and badge; a failed check caps the score, coverage is always shown, missing never counts as passing | `trustbadge aggregate examples/reports-real` |

Did it · told it · still works · nothing leaked · what it cost ·
fits the contract · safe to install · what changed · on tape ·
linted clean · claims backed · one badge.

## Install

Every tool installs straight from its repo — no package index
account needed:

```bash
pipx install git+https://github.com/maticcretic-commits/secretscan
# or
pip install git+https://github.com/maticcretic-commits/secretscan
```

…or clone and run the single file: `python src/secretscan.py …`.
Requires Python 3.9+. A PyPI release is planned.

## The family conventions

All twelve tools share one contract (the full text is
`CONVENTIONS.md` in every repo):

- **Exit codes** — 0 clean / PASS · 1 findings / FAIL · 2 caller
  error (with a `{"error": {"code", "message"}}` envelope on
  stderr) · 3 environment error.
- **JSON envelope** — every JSON report carries
  `{tool, version, schema_version}`; finding-type tools add
  `findings_summary` and per-finding severity tiers
  (CRITICAL / HIGH / INFO).
- **SARIF** — the six finding-type tools also emit SARIF 2.1.0
  (`--format sarif`) for GitHub code scanning and other consumers.
- **Pipes** — `-` reads an input from stdin; JSON modes write only
  JSON to stdout, so the tools chain in shell pipelines.
- **Dogfood gate** — before any release, every tool is run against
  every sibling's source, examples and READMEs. A finding is fixed
  or explicitly allow-listed with an expiry — never silently
  ignored. The family's own reports, including the failures, are
  published in TrustBadge's examples.

## CI

Each verifier tool's repo runs the shared reusable workflow in this
repository (`.github/workflows/agentforge-verify.yml`) against its
own examples — steal it for your projects:

```yaml
uses: maticcretic-commits/.github/.github/workflows/agentforge-verify.yml@main
with:
  tool: secretscan
  args: scan src --format json
  sarif-args: scan src --format sarif
```

## Honest limitations

These are deterministic, pattern- and structure-based checks. They
prove an output fits a contract, a claim cites matching evidence, a
known secret shape is absent — they do not prove an agent is "safe",
and no score here is a safety probability. The tools are
deliberately small: no hosted dashboard, no telemetry, no
model-based judging. What they find, they show; what they cannot
check, they say.

## License

MIT, every repo.
