# Spine Topics — Laniakea Phase 1

**Generated:** 2026-01-29
**Corpus:** `laniakea-docs` @ commit 3ab8ce9
**Scope anchor:** `phase1/laniakea-phase-1.md`

---

## A. Summary Table

| # | Spine Topic | Phase | Fan-out | Ambiguity Cost | Cross-domain | Decision Leverage | Evidence | Total | Primary Owners |
|---|-------------|-------|---------|---------------|--------------|-------------------|----------|-------|----------------|
| 1 | Settlement Cycle: Monthly (P1) vs Weekly (P2+) | Phase 1 / Phase 2+ | 5 | 5 | 5 | 5 | 5 | **25** | BA Labs, PullUp, Archon |
| 2 | NFAT Facility End-to-End Lifecycle | Phase 1 | 5 | 5 | 4 | 5 | 5 | **24** | PullUp, Amatsu, Archon, Frontier Foundation |
| 3 | Configurator BEAM Hierarchy & SORL Bounds | Phase 1 | 5 | 4 | 4 | 4 | 5 | **22** | PullUp, Atlas Axis, Endgame Edge |
| 4 | Synome-MVP Scope & Consumer Contract | Phase 1 | 4 | 5 | 4 | 5 | 4 | **22** | Archon, BA Labs, Atlas Axis |
| 5 | Diamond PAU Migration & Factory Standardization | Phase 1 (Stage 1) | 4 | 3 | 3 | 5 | 4 | **19** | PullUp, Dewiz, Atlas Axis |
| 6 | NFAT Data Partitioning: On-chain / Synome / Off-chain | Phase 1 | 4 | 5 | 4 | 4 | 4 | **21** | PullUp, Archon, Frontier Foundation |
| 7 | Core Halo Definition & Legacy Cleanup Criteria | Phase 1 (Stage 3) | 4 | 4 | 4 | 4 | 4 | **20** | Atlas Axis, PullUp, BA Labs, Endgame Edge |
| 8 | Structuring Halo Buybox: Legal Bounds for Autonomous Execution | Phase 1 (Stage 5) | 4 | 4 | 4 | 4 | 4 | **20** | Frontier Foundation, Amatsu, PullUp, Archon |
| 9 | LCTS Generation Model & Risk-Capital Token Issuance | Prerequisite for Phase 1 | 4 | 4 | 3 | 3 | 5 | **19** | PullUp, BA Labs, Archon |
| 10 | Beacon Authority Model: Phase 1 LPHA Scope vs Full Taxonomy | Phase 1 | 4 | 3 | 4 | 3 | 4 | **18** | Archon, PullUp, Atlas Axis |

**Scoring key** (each 1–5): Fan-out = downstream specs affected if wrong; Ambiguity Cost = expense of current ambiguity; Cross-domain = teams/doc-folders touched; Decision Leverage = degree to which resolving unlocks parallel work; Evidence = strength of textual anchors in corpus.

---

## B. Detail Sections

---

### 1. Settlement Cycle: Monthly (Phase 1) vs Weekly (Phase 2+)

**Phase:** Phase 1 decision / Phase 2+ transition

**Anchor citations:**
- `phase1/laniakea-phase-1.md` §Executive Summary: *"Phase 1 operates on a monthly settlement cycle; weekly settlement is a Phase 2+ target."*
- `risk-framework/weekly-settlement-cycle.md` §Overview: entire document specifies Tue→Wed weekly timeline (Measurement Period, Processing Period, Moment of Settlement)
- `phase1/laniakea-phase-1.md` §3 Checker Beacon: lists "Settlement Tracking" as a responsibility, referencing "weekly settlement cycle progress"
- `risk-framework/sentinel-integration.md`: key metrics (CRR, TRRC, TRC) assume periodic recalculation cadence
- `smart-contracts/lcts.md` §Generation Model: LCTS has both `weekly` and `weekday` cycle modes, settlement mechanics differ per mode

**Open question:**
The Phase 1 scope doc says "monthly settlement" but the Checker Beacon table within the same document references "weekly settlement cycle progress." The entire risk framework (`weekly-settlement-cycle.md`, `tugofwar.md`, `duration-model.md`) is written assuming weekly cadence. What exactly runs monthly in Phase 1? Is it only the OSRC/SPTP auction cycle that's deferred, while interest accrual and CRR monitoring still run continuously? Which sections of `weekly-settlement-cycle.md` apply to Phase 1 at monthly cadence, and which are fully deferred?

**Why it's a spine topic:**
Every downstream system — smart contract settlement logic, beacon monitoring schedules, risk parameter refresh rates, LCTS queue settlement timing, NFAT deployment/redemption windows — depends on knowing the settlement cadence. If teams build to weekly assumptions while Phase 1 is monthly, integration testing will fail across the entire stack. If monthly settlement is poorly defined (just "weekly but slower"), each team will interpret differently.

**Fan-out:** Touches LCTS settlement, CRR calculation frequency, Tug-of-War allocation timing, NFAT redemption windows, beacon monitoring loops, Configurator rate limit re-evaluation. Every layer from Generator to Halo is affected.

**Suggested resolution owners:**
- **BA Labs** — Define which risk framework calculations run at which cadence in Phase 1 vs Phase 2+
- **PullUp** — Confirm LCTS contract supports both monthly and weekly modes, or identify contract changes needed
- **Archon** — Confirm beacon polling/reporting intervals for monthly cadence

---

### 2. NFAT Facility End-to-End Lifecycle

**Phase:** Phase 1

**Anchor citations:**
- `phase1/laniakea-phase-1.md` §7 NFAT Smart Contracts: Facility (Queue→Claim→Mint→Deploy) and Redeemer (Receive→Notify→Redeem)
- `smart-contracts/nfats.md`: full business requirements — 12 scenarios, Queue contract (share-based), Deal NFAT (ERC-721), Redeem Contract, onchain vs offchain split
- `phase1/laniakea-phase-1.md` §8 NFAT Beacon: lpha-nfat responsibilities (Queue Sweeping, NFAT Issuance, Redemption Funding, RWA Offboarding, Record Keeping)
- `legal-and-trading/structuring-halo.md` §NFAT Lifecycle Phases: lifecycle from Queue to Redeem with lpha-nfat beacon permissions (pBEAM)
- `synomics/atlas-synome-separation.md`: NFAT records as Synome data
- `phase1/laniakea-phase-1.md` §9 Structuring Halo Legal: buybox model, pre-signed agreements, recourse mechanisms

**Open question:**
The lifecycle spans six systems: (1) Prime PAU deposits into Queue, (2) Queue contract holds funds share-based, (3) lpha-nfat claims and mints ERC-721, (4) PAU deploys to RWA endpoint, (5) Redeemer receives returned funds, (6) NFAT holder redeems and burns. The `nfats.md` spec defines the smart contract interfaces in detail but does not specify the handoff protocol between lpha-nfat and the legal layer (structuring-halo buybox). Specifically: who triggers the "deal is ready" signal that causes lpha-nfat to sweep the queue? Is it the Synome-MVP (Operational GovOps writes deal params → lpha-nfat reads them), or does lpha-nfat poll the Queue independently? The Phase 1 doc says both — §2 shows "NFAT deal params" flowing from Operational GovOps through Synome-MVP to lpha-nfat, while §8 says lpha-nfat manages "Queue Sweeping" and "NFAT Issuance."

**Why it's a spine topic:**
This is the most complex cross-domain integration in Phase 1. A misunderstanding at any handoff point (PAU→Queue, Queue→lpha-nfat, lpha-nfat→Synome, lpha-nfat→RWA endpoint, RWA→Redeemer→NFAT holder) cascades into legal exposure (wrong deal terms), financial loss (funds stuck in queue), or operational failure (beacon can't execute). Five teams must agree on the exact message flow.

**Suggested resolution owners:**
- **PullUp** — Finalize Facility and Redeemer contract interfaces; clarify Queue claim authorization model
- **Archon** — Define lpha-nfat's exact trigger conditions, Synome-MVP read/write contract
- **Amatsu** — Confirm Operational GovOps workflow for loading deal params into Synome-MVP
- **Frontier Foundation** — Validate buybox enforcement at the beacon level; confirm legal artifact templates

---

### 3. Configurator BEAM Hierarchy & SORL Bounds

**Phase:** Phase 1 (Stage 4)

**Anchor citations:**
- `smart-contracts/configurator-unit.md` §Role Capabilities: aBEAM actions (timelock), cBEAM actions (operational), 7 invariants
- `smart-contracts/configurator-unit.md` §Architecture: BEAMTimeLock → BEAMState → Configurator stack, Solidity interfaces
- `phase1/laniakea-phase-1.md` §6 Configurator Unit: two-tier access model, SORL (20%/18h), typical flow (5 steps)
- `synomics/beacon-framework.md` §Controllers and Custodians: cBEAM/pBEAM hierarchy for High Authority beacons
- `smart-contracts/configurator-unit.md` §Invariants: 7 invariants including "rate limits can only be set from approved inits"

**Open question:**
The Configurator spec defines aBEAM and cBEAM roles with Solidity interfaces. The beacon framework references pBEAM (permission BEAM) for beacon-level authority. Phase 1 deploys three beacons (lpla-checker, lpha-relay, lpha-nfat) that need PAU access. How does pBEAM relate to cBEAM? Does lpha-relay hold a cBEAM and call through the Configurator, or does it hold a direct relayer role set by a cBEAM-holding GovOps? The Phase 1 doc (§4 Relay Beacon) says the relay has "relay role" but the Configurator spec (§Role Capabilities) says cBEAMs "set relayer/freezer addresses." The composition model — which entity holds which BEAM for which PAU — is not specified end-to-end.

**Why it's a spine topic:**
The Configurator is the gate between governance and execution. If the BEAM composition is wrong, either beacons can't operate (too restrictive) or beacons can exceed their authority (too permissive). Every GovOps team, every beacon, and every Prime deployment depends on getting this right. The 14-day timelock means errors take two weeks to fix.

**Suggested resolution owners:**
- **PullUp** — Finalize the Configurator Solidity interfaces; specify how relayer/freezer roles map to beacon addresses
- **Atlas Axis** — Define the complete BEAM assignment matrix: which GovOps gets cBEAMs for which PAUs
- **Endgame Edge** — Validate operational flows from GovOps perspective; confirm cBEAM usage patterns

---

### 4. Synome-MVP Scope & Consumer Contract

**Phase:** Phase 1 (Stage 2)

**Anchor citations:**
- `phase1/laniakea-phase-1.md` §2 Synome-MVP: data flow diagram (Core Council GovOps → risk params; Operational GovOps → NFAT deal params; outputs to lpla-checker and lpha-nfat)
- `synomics/atlas-synome-separation.md` §The Solution: full Synome vision — graph database with Agent Nodes, Instance Nodes, State Nodes, Config Nodes, Algorithm Nodes, RateLimit Nodes
- `synomics/atlas-synome-separation.md` §Open Questions: 6 open items including "Implementation approach," "Access control model," "Versioning strategy"
- `risk-framework/sentinel-integration.md`: beacons consume risk metrics (CRR, TRRC, TRC, Encumbrance Ratio)
- `synomics/syno-teleonomic-paradigm.md` §Synome layer: Synome as first of five canonical layers

**Open question:**
Synome-MVP stores three data types: risk parameters, NFAT records, and position data. The full Synome vision (`atlas-synome-separation.md`) envisions a graph database with typed nodes, transaction logs, BEAM hierarchy data, and more — with 6 open questions about implementation. What is the MVP cut? Is Synome-MVP a flat key-value store, a relational database, a graph database, or something else? What is the access control model — do beacons authenticate via wallet signatures, API keys, or Synome-native identities? The Phase 1 doc says Synome-MVP accepts "signed statements from authorized parties" but doesn't define the signing scheme or authorization checks.

**Why it's a spine topic:**
Synome-MVP is the data backbone for all Phase 1 beacons. If the data model, access patterns, or authorization scheme is ambiguous, each team will build to incompatible assumptions. The lpla-checker needs to read risk params; lpha-nfat needs to read deal params and write NFAT records; GovOps teams need to write risk params and deal params. All of these are different access patterns with different authorization requirements.

**Suggested resolution owners:**
- **Archon** — Define Synome-MVP data model, API surface, and access control scheme
- **BA Labs** — Specify risk parameter schema (what fields, what types, what units)
- **Atlas Axis** — Define Core Council GovOps write interface; confirm signing authority chain

---

### 5. Diamond PAU Migration & Factory Standardization

**Phase:** Phase 1 (Stage 1)

**Anchor citations:**
- `phase1/laniakea-phase-1.md` §1 Diamond PAU Deployment: EIP-2535 architecture, benefits table, Phase 1 action facets, 4-step migration path
- `smart-contracts/diamond-pau.md`: placeholder spec — facet organization table, migration path, Configurator integration notes
- `smart-contracts/architecture-overview.md` §PAU Pattern: universal building block (Controller + ALMProxy + RateLimits), code reuse principle
- `smart-contracts/architecture-overview.md` §Laniakea Factory: standardized factory deployment for all PAU types

**Open question:**
The Diamond PAU spec (`diamond-pau.md`) is explicitly marked as a placeholder. The Phase 1 doc lists four Phase 1 action facets (NfatDepositFacet, NfatWithdrawFacet, CoreHaloFacet, LegacyMigrationFacet) and a 4-step migration path. But the migration details are thin: step 2 says "Migrate positions from legacy ALMProxy to new Diamond" without specifying whether this is an atomic swap, a gradual migration, or requires dual-running. The architecture doc describes a Laniakea Factory but doesn't specify whether Diamond PAUs are factory-deployed or manually deployed. Since Diamond PAU is Stage 1 and every subsequent stage depends on it, ambiguity here blocks all downstream work.

**Why it's a spine topic:**
Diamond PAU is the foundation contract for all Phase 1 operations. The Configurator connects to it; beacons execute through it; Core Halos and NFAT Facilities plug into its facets. If the facet interface contracts or migration path changes, every team rebuilds. The placeholder status of `diamond-pau.md` means the spec gap is real, not theoretical.

**Suggested resolution owners:**
- **PullUp** — Complete Diamond PAU spec: facet interfaces, DiamondCut governance, migration sequence (atomic vs gradual)
- **Dewiz/Sidestream** — Define spell requirements for Diamond PAU deployment and legacy migration
- **Atlas Axis** — Coordinate Prime-by-Prime migration schedule; confirm first-cohort Primes

---

### 6. NFAT Data Partitioning: On-chain / Synome / Off-chain

**Phase:** Phase 1

**Anchor citations:**
- `smart-contracts/nfats.md` §Onchain vs Offchain: explicit split — on-chain ERC-721 stores minimal data (amount, maturity, facility address); Synome stores deal parameters (APY, counterparty, asset type); off-chain stores legal documents
- `phase1/laniakea-phase-1.md` §8 NFAT Beacon: lpha-nfat writes "issued NFAT parameters to Synome-MVP"
- `synomics/atlas-synome-separation.md` §What Moves to Synome: NFAT records as State Nodes
- `legal-and-trading/structuring-halo.md` §Governance Artifacts: Halo Artifact and Unit Artifact contents

**Open question:**
The NFAT spec defines a clear on-chain/off-chain split, but the boundary between Synome and off-chain is less defined. The `nfats.md` spec says Synome stores "NFAT deal parameters (APY, counterparty, asset type)" — but `structuring-halo.md` says Unit Artifacts contain "per-NFAT operational parameters, legal recourse documentation, deal terms." Are Unit Artifacts Synome records, or separate governance documents? If a beacon needs to verify a deal is within the buybox, does it read the buybox from Synome-MVP or from an artifact? The answer determines whether buybox enforcement is automated (Synome) or manual (governance review).

**Why it's a spine topic:**
Data partitioning determines what can be automated and what requires human intervention. If critical deal parameters live off-chain, lpha-nfat cannot verify buybox compliance autonomously — defeating the purpose of the beacon. If too much data lives on-chain, gas costs and privacy concerns arise. Every team building to the NFAT lifecycle needs to know where to read and write each data field.

**Suggested resolution owners:**
- **PullUp** — Finalize on-chain NFAT token fields (ERC-721 metadata vs storage)
- **Archon** — Define Synome-MVP schema for NFAT records; confirm lpha-nfat read/write fields
- **Frontier Foundation** — Clarify which legal data can be Synome-hosted vs must remain off-chain for confidentiality

---

### 7. Core Halo Definition & Legacy Cleanup Criteria

**Phase:** Phase 1 (Stage 3)

**Anchor citations:**
- `phase1/laniakea-phase-1.md` §5 Core Halos and Legacy Cleanup: decision tree (retain as Core Halo vs wind down), acceptance criteria, initial scope ("Morpho vaults, SparkLend, and other DeFi integrations")
- `whitepaper/appendix-b-sky-agent-framework-primitives.md` §Halo Agents: Halo types, Halo Unit definition ("standardized unit of yield-bearing capacity")
- `smart-contracts/architecture-overview.md` §Prime Layer: Core Halos as "legacy DeFi wrapped as Halo Units under Core Council governance"
- `phase1/laniakea-phase-1.md` §5: "Halo Unit Artifacts describe properties, parameters, and oracle data (no dedicated PAU or smart contracts)"

**Open question:**
The Phase 1 doc says Core Halos have "no dedicated PAU or smart contracts" and are governed by "Halo Unit Artifacts." But the architecture overview shows Core Halos accessed via `CoreHaloFacet` on the Diamond PAU, implying smart contract interaction (ERC4626, Aave, Curve interfaces). These statements appear contradictory. Do Core Halos interact with existing DeFi protocol contracts via the Diamond PAU's CoreHaloFacet, or are they passively tracked positions with governance-only interfaces? Additionally, the "worth retaining?" decision tree lacks quantitative criteria — who decides, using what metrics, whether a legacy position becomes a Core Halo or is wound down?

**Why it's a spine topic:**
Legacy cleanup is a prerequisite for Configurator deployment (Stage 4). The number and type of Core Halos directly determines: Configurator surface area (how many allocation targets), beacon monitoring scope (how many positions lpla-checker tracks), and risk framework inputs (how many CRR calculations). If cleanup criteria are vague, Primes will retain too many exposures, overloading the MVP infrastructure.

**Suggested resolution owners:**
- **Atlas Axis** — Define quantitative cleanup criteria; own the retain/wind-down decision per Prime
- **PullUp** — Clarify CoreHaloFacet interface: does it interact with DeFi protocols or only track positions?
- **BA Labs** — Define risk framework treatment of Core Halos (CRR weights, monitoring requirements)
- **Endgame Edge** — Operational assessment of legacy positions across active Primes

---

### 8. Structuring Halo Buybox: Legal Bounds for Autonomous Execution

**Phase:** Phase 1 (Stage 5)

**Anchor citations:**
- `phase1/laniakea-phase-1.md` §9 Structuring Halo Legal: buybox parameter table (Duration, Size, APY, Counterparties, Asset Types), deliverables list
- `legal-and-trading/structuring-halo.md` §Buybox Model: "defines the parameter range within which lpha-nfat may act without per-deal governance"
- `legal-and-trading/structuring-halo.md` §Beacon Permissions: pBEAM scope for lpha-nfat
- `smart-contracts/nfats.md` §Deal NFAT: ERC-721 representing the claim, minted by lpha-nfat
- `phase1/laniakea-phase-1.md` §10 SOFR Hedging: duration NFATs require hedging or SOFR-plus terms

**Open question:**
The buybox defines ranges (e.g., 6-24 month duration, 8-15% APY, 5M-100M size), but enforcement mechanism is unspecified. Is buybox compliance checked on-chain (smart contract reverts if parameters out of range), off-chain in Synome-MVP (lpha-nfat reads buybox and self-validates), or by governance review (Operational GovOps verifies before loading deal params)? The answer has major implications: on-chain enforcement adds contract complexity but is trustless; off-chain enforcement is simpler but depends on beacon correctness; governance review defeats the automation goal.

Additionally, the SOFR hedging requirement (§10) adds a cross-cutting constraint: Primes deploying into duration NFATs must have declared hedges. Where is this check enforced — lpla-checker (monitoring), lpha-nfat (execution-time block), or Configurator (rate limit gating)?

**Why it's a spine topic:**
The buybox is the legal/technical interface that enables autonomous NFAT operations — the central value proposition of Phase 1 Structuring Halos. If the enforcement model is wrong, either the system can't operate autonomously (too much human gating) or it can execute deals outside legal bounds (not enough enforcement). Three distinct domains — legal, smart contracts, and beacon operations — must agree on enforcement semantics.

**Suggested resolution owners:**
- **Frontier Foundation** — Define legal enforceability requirements: what must be on-chain for legal validity
- **PullUp** — Determine if NFAT Facility contract enforces buybox ranges or delegates to beacon
- **Archon** — Specify lpha-nfat's buybox validation logic and SOFR hedging check
- **Amatsu** — Confirm Operational GovOps role in deal parameter loading vs full delegation to lpha-nfat

---

### 9. LCTS Generation Model & Risk-Capital Token Issuance

**Phase:** Prerequisite for Phase 1

**Anchor citations:**
- `smart-contracts/lcts.md` §Generation Model: ACTIVE→LOCKED→FINALIZED lifecycle, 16 invariants
- `smart-contracts/lcts.md` §Cycle Modes: `weekly` (Tue→Wed settlement) and `weekday` (Mon-Fri daily) modes
- `smart-contracts/architecture-overview.md` §Halo Layer: "LCTS is the natural fit because Halos face capacity/liquidity constraints"
- `smart-contracts/architecture-overview.md` §Risk Capital Tokens: srUSDS, TEJRC, TISRC all issued via LCTS
- `risk-framework/weekly-settlement-cycle.md` §LCTS Settlement: LCTS queues settle at Moment of Settlement
- `risk-framework/risk-capital-ingression.md`: Primes ingress (recognize) external risk capital

**Open question:**
LCTS is used for two distinct purposes: (1) Halo vault tokens for capacity-constrained strategies, and (2) risk capital tokens (srUSDS, TEJRC, TISRC) that back Prime deployments. The LCTS spec defines `weekly` and `weekday` cycle modes, but Phase 1 uses monthly settlement. Does LCTS need a `monthly` cycle mode, or do LCTS queues still settle on a shorter cadence independent of the broader settlement cycle? For risk capital tokens specifically, the ingression model (`risk-capital-ingression.md`) describes continuous ingression, which seems to conflict with batch LCTS settlement. The LCTS spec has 16 invariants — which apply in Phase 1's monthly cadence?

**Why it's a spine topic:**
LCTS is the token standard underlying both Halo access (Passthrough Halos, Core Halos) and risk capital (srUSDS, TEJRC, TISRC). If LCTS mechanics don't align with Phase 1's monthly cadence, either users can't deposit/redeem on reasonable timescales or the settlement model breaks. The standard is highly specified (1090 lines, 16 invariants) but assumes weekly settlement, creating a mismatch with Phase 1 scope.

**Suggested resolution owners:**
- **PullUp** — Determine if LCTS supports monthly mode or if Phase 1 uses weekly LCTS settlement within monthly broader cadence
- **BA Labs** — Clarify risk capital ingression timing: continuous vs batch, and interaction with LCTS queues
- **Archon** — Confirm beacon role in LCTS settlement processing (lpha-lcts is Phase 2+ but queue mechanics start in Phase 1)

---

### 10. Beacon Authority Model: Phase 1 LPHA Scope vs Full Taxonomy

**Phase:** Phase 1

**Anchor citations:**
- `phase1/laniakea-phase-1.md` §Sentinels and Beacons: Phase 1 deploys LPLA (lpla-checker) and LPHA (lpha-relay, lpha-nfat) beacons only
- `synomics/beacon-framework.md` §Beacon Taxonomy: full 2×2 matrix (LPLA, LPHA, HPLA, HPHA), lifecycle (Registration → Authority Envelope → Activation → Monitoring → Revocation)
- `synomics/beacon-framework.md` §Controllers and Custodians: specialized roles (ctl-bridge, cst-synome) that cut across taxonomy
- `legal-and-trading/sentinel-network.md`: Sentinel formations (stl-base, stl-stream, stl-warden) as Phase 2+ HPHA beacons
- `smart-contracts/configurator-unit.md` §Role Capabilities: relayer and freezer roles as beacon execution surfaces

**Open question:**
The beacon framework defines a rich lifecycle (Registration, Authority Envelope, Activation, Monitoring, Revocation) and properties (Registered, Scoped, Anchored, Observable, Revocable). Phase 1 beacons are "low-power" — deterministic programs, not AI. Does the full beacon lifecycle apply to Phase 1's low-power beacons, or is it overkill? Specifically: are Phase 1 beacons "registered in the Synome" (per beacon framework) or just deployed as programs with PAU access (per Phase 1 doc)? The Phase 1 doc never mentions Synome registration for beacons — it only mentions Synome as a data store. If Phase 1 beacons aren't Synome-registered, the governance revocation path described in the beacon framework doesn't apply, and a different shutdown mechanism is needed.

**Why it's a spine topic:**
Phase 1 beacons are the operational workhorses, but the governance model for their lifecycle is undefined at the Phase 1 level. If a beacon misbehaves, who can shut it down and how? The full beacon framework says "synomic governance" via revocation, but Phase 1's Synome-MVP may not support the registration substrate. The Configurator offers an alternative (revoke relayer role via freezer), but that's a different mechanism with different authorization chains. Teams building beacons need to know which governance model they're building to.

**Suggested resolution owners:**
- **Archon** — Define Phase 1 beacon lifecycle: registration, monitoring, and shutdown mechanisms
- **PullUp** — Confirm freezer role as the Phase 1 beacon shutdown mechanism; specify freezer authorization chain
- **Atlas Axis** — Determine whether Phase 1 beacons require Synome registration or operate outside Synome governance

---

## C. Sanity Checks

### Domain Coverage

| Domain / Folder | Topics Touching It | Topic Numbers |
|-----------------|-------------------|---------------|
| `phase1/` | 10/10 | 1–10 |
| `smart-contracts/` | 8/10 | 1, 2, 3, 5, 6, 7, 8, 9 |
| `risk-framework/` | 5/10 | 1, 4, 7, 8, 9 |
| `synomics/` | 5/10 | 4, 6, 10, (3 via BEAM), (2 via Synome records) |
| `legal-and-trading/` | 4/10 | 2, 6, 8, 10 |
| `whitepaper/` | 2/10 | 7, 9 |

**No orphan folders.** Every documentation folder is touched by at least two spine topics.

### Team Coverage

| Team | Topics | Topic Numbers |
|------|--------|---------------|
| PullUp (Smart Contracts) | 9/10 | 1, 2, 3, 5, 6, 7, 8, 9, 10 |
| Archon (Sentinel/Synome/AI) | 7/10 | 1, 2, 4, 6, 8, 9, 10 |
| Atlas Axis (Atlas dev/GovOps) | 5/10 | 3, 4, 5, 7, 10 |
| BA Labs (Risk) | 5/10 | 1, 4, 7, 9, (8 via SOFR) |
| Frontier Foundation (Strategy/Legal) | 3/10 | 2, 6, 8 |
| Endgame Edge/Redline (OEA GovOps) | 3/10 | 3, 7, (1 via operations) |
| Amatsu/Soter Labs (Allocation) | 2/10 | 2, 8 |
| Dewiz/Sidestream (Spells) | 1/10 | 5 |

**Observation:** PullUp appears on 9/10 topics, reflecting that smart contracts are the substrate for nearly every Phase 1 deliverable. Archon (7/10) is second, reflecting that beacons and Synome are the integration layer. Dewiz/Sidestream appears only once (Diamond PAU migration spells), which is consistent with Phase 1's goal of eliminating spell dependency via the Configurator.

### Phase Distribution

| Phase Label | Count | Topics |
|-------------|-------|--------|
| Phase 1 | 7 | 2, 3, 4, 6, 7, 8, 10 |
| Phase 1 (specific stage) | 2 | 5 (Stage 1), 7 (Stage 3), 8 (Stage 5) |
| Prerequisite for Phase 1 | 1 | 9 |
| Phase 1 / Phase 2+ boundary | 1 | 1 |

**No topic is exclusively Phase 2+.** This is correct — the prompt specified Phase 1 scope, and all topics either directly belong to Phase 1 or define the boundary between Phase 1 and Phase 2+.

### Cross-Domain Gate Verification

Every topic touches ≥2 distinct documentation folders and ≥2 distinct teams:

| # | Doc Folders | Teams |
|---|-------------|-------|
| 1 | phase1, risk-framework, smart-contracts | BA Labs, PullUp, Archon |
| 2 | phase1, smart-contracts, legal-and-trading, synomics | PullUp, Amatsu, Archon, Frontier Foundation |
| 3 | smart-contracts, phase1, synomics | PullUp, Atlas Axis, Endgame Edge |
| 4 | phase1, synomics, risk-framework | Archon, BA Labs, Atlas Axis |
| 5 | phase1, smart-contracts | PullUp, Dewiz, Atlas Axis |
| 6 | smart-contracts, synomics, legal-and-trading | PullUp, Archon, Frontier Foundation |
| 7 | phase1, smart-contracts, whitepaper | Atlas Axis, PullUp, BA Labs, Endgame Edge |
| 8 | phase1, legal-and-trading, smart-contracts | Frontier Foundation, Amatsu, PullUp, Archon |
| 9 | smart-contracts, risk-framework, phase1 | PullUp, BA Labs, Archon |
| 10 | phase1, synomics, legal-and-trading, smart-contracts | Archon, PullUp, Atlas Axis |

**All topics pass the cross-domain hard gate.**

### Hard-Anchor Gate Verification

Every topic cites ≥1 specific section, table, invariant, or quoted passage from the corpus. No topic relies solely on inferred relationships.

### Candidate Longlist (for transparency)

The following 30 candidates were evaluated before the hard gates and scoring cut produced the final 10:

| # | Candidate | Passed Gates? | Score | Disposition |
|---|-----------|--------------|-------|-------------|
| 1 | Settlement cycle monthly vs weekly | ✓ | 25 | **Selected (#1)** |
| 2 | NFAT Facility lifecycle | ✓ | 24 | **Selected (#2)** |
| 3 | Configurator BEAM hierarchy | ✓ | 22 | **Selected (#3)** |
| 4 | Synome-MVP scope | ✓ | 22 | **Selected (#4)** |
| 5 | Rate limits universal mechanism | ✓ | 22 | Merged into #3 (SORL is the cross-domain tension) |
| 6 | NFAT onchain/offchain split | ✓ | 21 | **Selected (#6)** |
| 7 | Core Halo wrapping | ✓ | 20 | **Selected (#7)** |
| 8 | Structuring Halo buybox | ✓ | 20 | **Selected (#8)** |
| 9 | Diamond PAU migration | ✓ | 19 | **Selected (#5)** |
| 10 | LCTS generation model | ✓ | 19 | **Selected (#9)** |
| 11 | Halo Unit definition | ✓ | 19 | Subsumed by #7 (Core Halo) |
| 12 | Beacon taxonomy | ✓ | 18 | **Selected (#10)** |
| 13 | SPTP duration matching | ✓ | 16 | Partially in #8 (SOFR hedging) |
| 14 | CRR calculation inputs | ✓ | 16 | Partially in #4 (Synome stores params) |
| 15 | SOFR hedging | ✓ | 16 | Folded into #8 |
| 16 | Sentinel formations | Partial | 15 | Phase 2+ focus |
| 17 | Passthrough Halo allocation | Partial | 14 | Phase 2+ sentinel dependency |
| 18 | Identity Network registry | Partial | 13 | Phase 2+ |
| 19 | Atlas/Synome separation | ✓ | 18 | Subsumed by #4 (Synome-MVP scope) |
| 20 | SORL constraint | Partial | — | Subconcept of #3 |
| 21 | Risk capital ingression | ✓ | 16 | Partially in #9 (LCTS issuance) |
| 22 | Loss absorption waterfall | Partial | 15 | Risk-internal |
| 23 | JRC/SRC tiering | Partial | 14 | Risk-internal |
| 24 | Tug-of-War capacity | Partial | 15 | Phase 2+ weekly cadence |
| 25 | OSRC auction | Partial | 15 | Phase 2+ weekly cadence |
| 26 | Factory standardization | Partial | — | Folded into #5 |
| 27 | Foreign layer bridge | Partial | 13 | Phase 2+ |
| 28 | Sky Intents / Exchange Halo | Partial | 12 | Phase 2+ |
| 29 | Sentinel carry calculation | Partial | 12 | Phase 2+ |
| 30 | Fortification Conserver | Partial | 13 | Mostly legal-internal |

---

## Appendix: Team Key

| Short Name | Full Name | Primary Domain |
|------------|-----------|----------------|
| BA Labs | BA Labs | Risk framework, capital formulas |
| PullUp | PullUp Labs | Smart contract development |
| Dewiz | Dewiz / Sidestream | Spell crafting, governance execution |
| Archon | Archon | Sentinel network, Synome, AI agents |
| Atlas Axis | Atlas Axis | Atlas development, GovOps coordination |
| Endgame Edge | Endgame Edge / Redline | OEA GovOps operations |
| Amatsu | Amatsu / Soter Labs | Allocation system, Halo operations |
| Frontier Foundation | Frontier Foundation | Strategy, legal infrastructure |
| Ranked Delegates | Ranked Delegates | Governance voting |
