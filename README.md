# AgentForge — family infrastructure

This repository is the AgentForge family's shared GitHub
infrastructure — it is not a tool.

- **`profile/README.md`** — the family index shown on the profile:
  all twelve tools, what each one verifies, install pattern, the
  shared conventions, and the honest limitations.
- **`.github/workflows/agentforge-verify.yml`** — one reusable
  workflow (`on: workflow_call`) that installs an AgentForge tool
  from its git URL, runs it against the caller repository, and
  uploads its JSON + SARIF reports as artifacts. The verifier-tool
  repos call it against their own examples.

## Install

Nothing to install here — this repo works by being named
`.github`: GitHub renders `profile/README.md` on the profile page,
and caller repos reference the reusable workflow by path. The
tools themselves install per repo:
`pipx install git+https://github.com/maticcretic-commits/<tool>`.

## Usage

Caller repos run the verification workflow by adding a thin
`.github/workflows/verify.yml`:

```yaml
jobs:
  verify:
    uses: maticcretic-commits/.github/.github/workflows/agentforge-verify.yml@main
    with:
      tool: secretscan
      args: scan src --format json
```

## License

MIT.

The family: **verify AI-agent work before you ship it** —
[AgentProof](https://github.com/maticcretic-commits/agentproof) ·
[PromptRE](https://github.com/maticcretic-commits/promptre) ·
[AgentCI](https://github.com/maticcretic-commits/agentci) ·
[SecretScan](https://github.com/maticcretic-commits/secretscan) ·
[CostLedger](https://github.com/maticcretic-commits/costledger) ·
[SchemaGuard](https://github.com/maticcretic-commits/schemaguard) ·
[SkillAudit](https://github.com/maticcretic-commits/skillaudit) ·
[PromptDiff](https://github.com/maticcretic-commits/promptdiff) ·
[TraceTape](https://github.com/maticcretic-commits/tracetape) ·
[PromptLint](https://github.com/maticcretic-commits/promptlint) ·
[ClaimCheck](https://github.com/maticcretic-commits/claimcheck) ·
[TrustBadge](https://github.com/maticcretic-commits/trustbadge)

License: MIT.
