# sourcing-skill

Professional procurement sourcing skill library, including AI role prompts, supplier screening logic, sourcing workflow, supplier qualification standards and cost analysis rules.

## Included skill

### AI寻源需求澄清Skill V2.0.3

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

V1.7 adds **connector-first 1688 cloud collection**:

- discovery stage prefers the Bazhuayu connector in WorkBuddy to cloud-collect 1688 product lists (tested template 2850 "1688-keyword search product list", fields include title/price/volume/store name/store age/store tag);
- when no connector is configured, falls back to web searching 1688, without blocking existing flow;
- collected data needs de-duplication by product link; when search noise is high, switch to more precise platform-specific keywords.

V1.8 adds a **verified-supplier archive** — solving the "zero accumulation of historical verification conclusions" defect:

- new file `17_供应商验证档案库.md` stores verified conclusions (enterprise authenticity / product match / procurement fit / supplier-type judgment);
- three-step flow: **check archive first → create new verification → write back after verification**. Before sourcing starts and before candidate-pool building, query the archive; hit entries reuse historical conclusions without re-calling connectors; entries judged as traders must not enter A-class;
- the feedback-writeback landing point in file 14 now points to the archive: enterprises marked "加入供应商库=是" in phone feedback are written back in archive format;
- every sourcing run now "runs with memory" — verified conclusions are reused across tasks, saving connector budget and duplicate web searches.

V1.9 adds **parameter three-state marking** and **funnel-triggered evidence channels** — fixing "AI mistakenly rejects qualified suppliers by coverage rate" and "running every channel for every candidate makes the workflow slow and fragile":

- archive data split into a dedicated file `17a_供应商档案数据.md` (append-only), while file 17 keeps only rules and format definitions;
- **parameter classification + three-state marking**: hard specs / soft specs / unknown specs; for each hard spec the AI outputs only ✅ evidence found / ❓ no evidence (no downgrade, goes to phone-verification checklist) / ❌ counter-evidence (the only case allowing downgrade). Core principle: the AI judges whether evidence is sufficient, never whether the product is qualified;
- **funnel-triggered evidence channels**: fast lane (Qichacha registry + store/website pages) runs for every candidate; mid lane (AI opens 1688 product detail pages directly to read SKU parameter tables — tested working without login, ~60/65 specs readable; 5-8 promoted candidates only); counter-evidence lane (CNCA certificate verification at cx.cnca.cn, certificate-listed candidates only); bottom lane (phone + samples, 1-3 finalists). Evidence investment is proportional to promotion; elimination relies only on hard rules (counter-evidence / deregistration / user exclusions / archive exclusions), never on AI's subjective coverage judgment; any channel failure is recorded as ❓ without blocking the flow. Patent and standards-drafting channels are not adopted (coverage among SME suppliers is too low); Bazhuayu template 2857 (product detail scraping) tested unusable (0 rows, likely login wall) — do not use.

V1.9.1 is a defect-fix release after a full-chain audit: layer-3 procurement fit now follows the same three-state rule (❓ no evidence → "pending confirmation" only, never downgrade; only ❌ counter-evidence downgrades); quick mode gets a minimal verification set (archive query + A-class fast-lane registry check only); negative cases are written back to the archive ("排除" even when "add to supplier archive = no"); archive writes require git push + Codex copy sync; CNCA certificate verification tested — the site is accessible but queries require a CAPTCHA, so the AI generates a "certificate verification checklist" for the user to check manually (~1 min each); A-class hard-spec three-state marking and an aggregated phone-verification checklist are now front-stage display fields; B-class layer-2 depth clarified as shallow verification.

V1.9.2 adds a **three-tier supplier-type preference** — turning "what about traders?" from an after-the-fact patch into a first-round requirement input:

- **Preference A (manufacturers only)**: traders are excluded from the candidate pool entirely;
- **Preference B (both accepted, no priority)**: traders compete on equal footing with factories and may enter A-class;
- **Preference C (both accepted, factories first)**: traders are capped at B-class, ranked after factory candidates;
- the old one-size-fits-all rule "traders must never enter A-class" (previously written across files 08/13/15/17 and the SKILL checklist) is replaced by preference-driven tiering; archive hits for "trader" follow the same rule;
- **negative write-back states are graded (archive stores facts, not verdicts)**: identity facts are always written back; "排除" is reserved for integrity-level counter-evidence (active impersonation, forged certificates, fake addresses), capability-level counter-evidence (cannot make the category / cannot meet hard specs), or explicit user rejection; a trader identity alone never triggers "排除" — the state follows the current task's preference;
- archives marked "排除" solely due to trader identity trigger a front-stage prompt ("this trader is in the archive — re-include?") whenever the current preference is B or C, instead of silently skipping.

V1.9.3 is driven by the first real-world run (2026-08-25 rock-wool strip sourcing) and fixes two funnel-entry gaps:

- **supplier-region scope is now a first-class question**: delivery location (logistics endpoint) and acceptable supplier region (candidate-pool boundary) are asked separately; when unconfirmed, the AI searches nationwide and labels it front-stage instead of silently excluding distant production areas on its own;
- **known-supplier seed list + recall check**: the AI proactively asks for current/former suppliers during questioning; after the candidate pool is built, each seed supplier is checked for recall — a missed seed is a hard signal of a search blind spot (keyword gap / channel gap / name mismatch), triggering targeted re-search, and the recall rate is reported in the round-7 coverage summary.

V2.0 is a structural rework of the search architecture, driven by a **second-round recall failure** in the same run (the user reported 5 current suppliers across two batches; 2 were still missing after the first recall round):

- **industrial-belt hypothesis demoted from filter to ranker**: it only decides search order, never candidate-pool admission; no province may be excluded without explicit user instruction; round 4 becomes a nationwide production-area scan verified via full-coverage directories (no more confirming a hypothesis with the same hypothesis);
- **mandatory-regime check → tiered full-coverage directories**: the AI first checks whether the category has legally mandatory licensing/certification/registration (SC food permits, CCC, green-building-material catalogs, discharge permits, etc. — mapped per category, not a fixed list); if yes, the official registry is enumerated first as the industry baseline; if no, Qichacha industry enumeration + discharge-permit platform serve as fallback; for categories with no directory at all (generic machining / pure trading / emerging), the output honestly states "no full directory exists — best-effort enumeration";
- **recall failure ≥1 forces channel escalation**: switch to full-coverage directories to rebuild the baseline pool instead of endlessly tweaking keywords on marketing channels (which systematically miss strong factories that simply don't advertise — both missed suppliers had 64/77 insured employees);
- **pool-size plausibility check**: before stopping at the 30–50 quota, estimate the industry's total factory count and report the coverage rate (the run stopped at 35 of ~150 ≈ 23%);
- **upstream-category keywords are mandatory**: searching only the target product name finds only factories marketing that name; the matrix must also search the upstream category set (rock-wool strips → rock-wool boards / rock-wool manufacturers).

V2.0.2 fixes the candidate-ranking dimension exposed by the same run ("results didn't meet expectations"):

- **same-application-scenario supply experience is now the primary A-class ranking key**: every candidate is checked for verifiable evidence of producing/supplying the same-use product for the confirmed application scenario (dedicated product line / government registration / peer-customer references / test reports issued under that use); evidence ✅ ranks first, no-evidence ❓ is not downgraded but ranks lower and goes to the phone-checklist — ranking priority is above company size, brand endorsement, and distance. Lesson: giants ranked by size/brand mainly sell "boards/insulation systems", while the user's actual suppliers are all sandwich-panel/insulation-board core strip specialists;
- **Octoparse 2850 short-keyword rule (tested)**: the connector is alive (earlier "not connected" was a misjudgment); keywords must be single short terms — space-combined phrases return completely irrelevant items (a combined keyword returned 10 rows of T-shirts); results are fuzzy category matches, suitable for discovery rather than precise filtering.

V2.0.3 tightens the scenario-experience criterion into a verifiable record, driven by follow-up feedback from the same run:

- **explicit production/sales records are the most accurate match criterion**: the best evidence that a candidate fits the target application scenario is a documented production/sales record for that exact use — a dedicated product line on sale for that scenario (e.g. sandwich-panel core strips), customer references/supply records in that scenario, government/industry registration under that use, or test reports issued for that use; vague "category relevance", company size, brand endorsement, and distance never substitute. The scenario must be questioned down to the concrete product/process level ("building insulation" → "exterior insulation decorative panel core") before this standard applies; when the user cannot answer, record "待供应商确认" and apply weak category-level matching with a label;
- three-state marking unchanged: documented record ✅ (with source noted); category-relevant but no record ❓ — no downgrade, ranked lower, and the phone checklist asks "do you have supply records for this application?".

Start with [`00_启动口令.md`](./00_启动口令.md), or read [`SKILL.md`](./SKILL.md) for the complete workflow.
