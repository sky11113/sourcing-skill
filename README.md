# sourcing-skill

Professional procurement sourcing skill library, including AI role prompts, supplier screening logic, sourcing workflow, supplier qualification standards and cost analysis rules.

## Included skill

### AI寻源需求澄清Skill V1.6

The repository root is the Codex Skill root. `SKILL.md` and all supporting workflow files live directly in this directory, so the repository can be used as the local Codex Skill working directory.

V1.4 adds three mandatory pre-output checklist items (missing any item means the run is not complete):

- official website and public contact info are required columns for every candidate (write "未找到" when not found, never fabricate);
- the round-7 coverage summary of missed-supplier checks must be shown as a standalone front-stage section in deep mode;
- phone-verification feedback forms are automatically attached to deep sourcing results, without waiting for the user to ask.

V1.5 adds four module upgrades — building a **discover → verify** closed loop:

- **Multi-channel supplier discovery**: search channels split into discovery channels (1688/Baidu/maps/Aigo/Douyin/vertical sites) and verification channels (Qichacha/Tianyancha/bidding/patents/recruitment); industrial-zone analysis moved from round-4 missed-check to before candidate-pool building;
- **Platform-specific keyword generation**: keyword matrix generates different search terms for web search, 1688 search, and map search; exclusion words are condition-triggered (only when user explicitly wants manufacturers);
- **Three-layer supplier verification**: enterprise authenticity → product match → procurement fit; A-class candidates get full three-layer verification, B-class gets first two layers, C-class gets cross-source counting only;
- **First-round question upgrade**: supplier type preference as mandatory first-level question (triggers keyword switching); optional image + technical document upload (AI extracts parameters, missing fields are asked back to user).

V1.6 adds **connector-first business-registry verification**:

- verification stage prefers the Qichacha/Tianyancha connectors in WorkBuddy for business registry data (existence status, business scope, shareholder structure, actual controller, beneficial owner, affiliated enterprises);
- when no connector is configured, falls back to searching public pages, marking paywalled content as "待人工核验" (pending manual verification);
- connector queries give hard evidence for detecting trader-masquerading-as-factory: check whether the candidate's shareholders/affiliated enterprises contain a real production facility.

Start with [`00_启动口令.md`](./00_启动口令.md), or read [`SKILL.md`](./SKILL.md) for the complete workflow.
