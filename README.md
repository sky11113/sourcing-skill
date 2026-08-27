# sourcing-skill

Professional procurement sourcing skill library, including AI role prompts, supplier screening logic, sourcing workflow, supplier qualification standards and cost analysis rules.

## Included skill

### AI寻源需求澄清Skill V2.1.0

The repository root is the Codex Skill root. `SKILL.md` and all supporting workflow files live directly in this directory, so the repository can be used as the local Codex Skill working directory.

Current rule highlights (see `SKILL.md` for the full workflow and `CHANGELOG.md` for version history):

- **one-question-at-a-time requirement clarification** with a 12-item first-level field list and an 80% stop rule (price is second-level, never probed);
- **three-tier supplier-type preference** (A manufacturers only / B either with no priority / C either but factories first) driving keyword switching and trader tier placement;
- **five-level downgrade matrix (V2.1.0)**: ❓ missing/insufficient evidence never downgrades; ⚠️ indirect risk signals cap at C-tier "quick confirm" (never D); only ❌ hard counter-evidence and 🚫 user exclusions eliminate a candidate;
- **discover → verify closed loop**: industrial-belt analysis as a ranker (not a filter), full-coverage registry channels before marketing channels, upstream-category keywords, seed-supplier recall checks, pool-size plausibility check;
- **three-layer verification** (enterprise authenticity → product match → procurement fit) with three-state spec marking (✅/❓/❌) and funnel-triggered evidence channels;
- **archive with explicit authorization (V2.1.0)**: verification conclusions produce review-pending archive cards by default; writing to the archive requires explicit user confirmation, and git commit/push + Codex-copy sync each require separate authorization — never automatic;
- **front-stage output completeness**: full registry names, website + public contact per candidate, all four tiers A/B/C/D with a one-line basis each, coverage summary as a standalone section, phone-verification feedback forms attached automatically.

Start with [`00_启动口令.md`](./00_启动口令.md), or read [`SKILL.md`](./SKILL.md) for the complete workflow. Version history (V1.3 → V2.1.0) lives in [`CHANGELOG.md`](./CHANGELOG.md).
