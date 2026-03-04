# OpenMontage Governance Framework

**Version 1.1 — How Decisions Get Made**

---

## 1. Governance Philosophy

OpenMontage governance is built on four foundational principles:

### Meritocracy

Influence in the OpenMontage ecosystem is earned through contributions, not purchased through connections. A first-time creator whose clip wins a Shot Market has the same economic rights as a veteran with dozens of merged clips. Quality is the only currency that matters for film-level decisions. Platform-level governance introduces token-weighted voting, but caps and reputation multipliers ensure that deep engagement always outweighs raw capital.

### Transparency

Every vote is recorded on-chain. Every revenue distribution is auditable. Every governance proposal, discussion, and outcome is public. There are no backroom deals — the blockchain is the single source of truth for all platform decisions. Monthly treasury reports, quarterly audits, and real-time dashboards ensure that every participant can independently verify that the system operates as documented.

### Progressive Decentralization

OpenMontage does not launch as a fully decentralized DAO on day one. That would be irresponsible — early-stage protocols need the ability to respond quickly to bugs, exploits, and market feedback. Instead, governance decentralizes in deliberate phases: from a founding team multi-sig, to token-weighted advisory votes, to binding on-chain governance, and ultimately to a constitution-based DAO with dispute courts. Each phase hands off more authority to the community as the protocol matures and the tooling can support it.

### Separation of Concerns

Film-level governance (which segment gets merged into Shot 042) and platform-level governance (should we raise the platform fee from 3% to 4%) are fundamentally different decisions requiring different participants, different quorums, and different timelines. OpenMontage enforces this separation structurally — a $MONTAGE whale cannot override a film community's segment vote, and individual film contributors cannot unilaterally change platform economics.

---

## 2. Governance Layers

### 2.1 Platform Governance (Meta-Level)

Platform governance controls the parameters and evolution of the OpenMontage protocol itself. $MONTAGE token holders vote on:

- **Platform fee changes** (currently 2-5% of film revenue)
- **New feature prioritization** (roadmap direction, tooling investment)
- **Treasury allocation** (grants, partnerships, ecosystem development)
- **Smart contract upgrades** (proxy upgrades, migration proposals)

#### Proposal Process

All platform governance proposals follow a structured pipeline:

1. **Discussion Phase** (minimum 7 days): Author posts proposal in Discord `#governance-proposals` channel. Community discusses, asks questions, and suggests amendments. Proposals must include: motivation, specification, risk analysis, and implementation plan.

2. **Signal Vote** (48-72 hours): Off-chain vote on Snapshot.org to gauge community sentiment. This is non-binding but serves as a filter — proposals that fail the signal vote are sent back for revision rather than proceeding to on-chain execution.

3. **On-Chain Execution**: If the signal vote passes, the proposal is submitted to the Governor contract (OpenZeppelin Governor with timelock). Token holders vote on-chain with their staked $MONTAGE balance.

4. **Quorum and Threshold**: 4% of circulating $MONTAGE supply must participate (quorum), and 50%+1 of votes cast must be in favor (simple majority). Contract upgrades and treasury allocations above $100,000 require a supermajority of 67%.

5. **Timelock**: Approved proposals enter a 48-hour timelock before execution, allowing the community to review and flag any last-minute concerns.

#### Emergency Actions (Phase 1)

Before full DAO governance is active, emergency actions (contract pause, critical bug fixes, security responses) are controlled by a **3-of-5 Gnosis Safe multi-sig** composed of founding team members. Emergency actions are retroactively ratified by the community within 7 days.

---

### 2.2 Film-Level Governance — Shot Competition Markets

Film-level governance determines which clips get merged into a film's Main Branch. This is the creative and economic heart of OpenMontage.

The key governance mechanism at the film level is no longer direct token voting — it is the **Shot Competition Market**: an economic mechanism where participants express quality judgments by staking real capital. Economic skin-in-the-game produces more honest quality signals than free token votes.

#### Market Lifecycle

Each Shot's competition follows a structured commit-reveal market:

1. **Commit Phase (5 days)**: Backers submit hidden commitments — a hash of `(clipId, amount, salt)`. Clip creators also “back” their own clip automatically; their submission stake (Creator Bond) counts as initial backing in the commit phase.

2. **Reveal Phase (2 days)**: Backers reveal their actual backing amounts. The `ShotMarket` contract verifies each reveal against stored commitment hashes.

3. **Resolution**: The clip with the highest **quadratic-weighted backing** wins. Ties are broken by earliest submission timestamp.

#### Quadratic Backing Weight

Winner selection uses quadratic backing weight rather than raw capital totals. This ensures **breadth of support matters more than depth of capital**:

**Formula**:
```
BackingWeight(clip) = Σ √(deposit_i)    for all backers i of that clip (including creator)
```

The creator's own submission stake (min 50 USDC) is automatically entered as their initial backing commitment. The quadratic formula counts all backers including the creator: `BackingWeight = Σ √(deposit_i)` for all i (creator + external backers).

**Why quadratic**: 100 backers at $100 each produce a weight of 1,000, while 1 whale at $10,000 produces a weight of only 100. Crowd wisdom beats capital dominance by design.

**Example**: Clip A has 3 backers ($200, $100, $50):
- Weight = √200 + √100 + √50 = 14.1 + 10.0 + 7.1 = **31.2**

#### Winner Payouts

After resolution, the smart contract distributes funds:

- **Winning clip backers**: Full stake returned + proportional share of the Reward Pool (20% of losing stakes)
- **Winning clip creator**: Submission stake (Creator Bond) returned in full + 50% of Reward Pool
- **Competition Tax**: 5% of losing stakes → Film Treasury (funds ongoing operations, streaming infrastructure)
- **Protocol share**: Included in platform fee structure (see tokenomics)

#### Loser Refunds

- **Losing clip backers**: 75% of backing stake returned. The remaining 25% funds the Reward Pool + Competition Tax.
- **Losing clip creators**: 75% of submission stake (Creator Bond) returned. Same 25% penalty applies.
- **All refunds** are claimable immediately after market resolution.

#### Insufficient Submissions Edge Cases

- **1 clip submitted**: Auto-wins with no competition. Creator gets submission stake back in full. No prize pool (no competition occurred).
- **2 clips**: Market runs normally with full commit-reveal cycle.
- **0 clips after 30 days**: Shot flagged as “stalled”; director can extend the deadline or re-describe the shot requirements.

#### Replacement Challenge Protocol

Once a clip is merged, it can be replaced if a genuinely superior version appears. The replacement process uses deliberate friction to protect incumbent creators:

1. **Challenge initiation**: Any creator can challenge an existing merged clip by depositing **2× the original Creator Bond** (Challenge Bond)
2. **Incumbency advantage**: A new mini-market opens where the incumbent clip starts with a **synthetic backing weight** equal to 50% of its original winning weight. The challenger's clip must achieve a quadratic-weighted backing score greater than 1.5× the incumbent's current score (50% incumbency advantage) to trigger a replacement.
3. **Cooldown**: 21-day cooldown per shot after any market resolution (initial or challenge)
4. **If challenger wins**: Incumbent clip archived (revenue stops), challenger clip merged (revenue starts). Challenger gets Challenge Bond back + reward pool share. Incumbent creator receives no additional penalty beyond losing their merged status.
5. **If challenger loses**: 75% of Challenge Bond refunded to challenger; 25% of forfeited amount goes to incumbent creator as a “defense reward”
6. **Incumbent response window**: The incumbent creator is notified and has 7 days to submit an improved version of their own clip (at no additional bond cost) before the mini-market's backing phase begins

#### Director Restrictions in Shot Markets

Directors **cannot back any clips** in their own film's Shot Markets. This is a hard conflict-of-interest rule enforced at the smart contract level — the `ShotMarket.commitBacking()` function rejects transactions from the Film NFT holder's address.

#### Anti-Whale Mechanism

Quadratic backing weight **is** the anti-whale mechanism. No additional voting power cap is needed for film-level decisions. The math guarantees that broad community consensus always outweighs concentrated capital. Maximum single-address backing is still tracked for transparency reporting.

#### Dispute Resolution

If a participant believes a market outcome was unfair (due to manipulation, technical glitch, or rule violation):

1. **Appeal Window**: 48 hours after the market result is finalized
2. **Appeal Stake**: Appellant must stake **10 $MONTAGE** (slashed if the appeal fails, returned if it succeeds)
3. **Jury Selection**: 7 jurors randomly selected from a pool of high-reputation backers (reputation score >= 75th percentile, no participation in the original market)
4. **Jury Deliberation**: 72 hours to review clips, backing records, and appellant's argument
5. **Jury Verdict**: Simple majority (4/7) to overturn; if overturned, the market is re-run with the disputed clip(s) flagged
6. **Final Escalation**: If either party contests the jury verdict, the dispute escalates to a platform-level DAO vote (binding, but rare)

---

### 2.3 Standard Library Governance

The Standard Library is the artistic backbone of each film. Changes to it affect every contributor, so updates are governed carefully:

#### Minor Updates (Director Authority)

The film's director can unilaterally push **minor version** changes (PATCH and MINOR under SemVer):
- Bug fixes in LoRA models or prompt templates
- Adding new assets (e.g., a new supporting character model)
- Style guide clarifications

These updates increment the version (e.g., 1.2.0 -> 1.2.1 or 1.3.0) and do not invalidate existing work-in-progress submissions.

#### Major Updates (Community Approval Required)

**Breaking changes** that would invalidate existing submissions require community consensus:
- Re-training core character LoRAs with different base weights
- Fundamental art style changes (e.g., switching from photorealistic to stylized)
- Removing or replacing ControlNet rigs

Process for major updates:
1. Director publishes a **migration proposal** detailing what changes and why
2. **70% approval** from active contributors (anyone with a submission in the last 30 days)
3. **2-week migration period**: Existing work-in-progress submissions can be completed and voted on under the old version; new submissions must use the new version after the migration period ends
4. Version increments to next MAJOR (e.g., 1.x.x -> 2.0.0)

#### Versioning

Standard Libraries follow **Semantic Versioning (SemVer)**:
- **MAJOR**: Breaking changes requiring re-generation of existing work
- **MINOR**: New assets or non-breaking enhancements
- **PATCH**: Bug fixes and documentation corrections

---

## 3. Roles and Participation Rights

OpenMontage separates film-level participation (Shot Markets, economic staking) from platform-level participation ($MONTAGE governance voting).

| Role | Definition | Film-Level Rights | Platform-Level Rights |
|------|-----------|-------------------|----------------------|
| **Creator** | Any wallet that has submitted >=1 clip | Submit clips + stake Creator Bond; back clips in other shots | None (unless holding $MONTAGE) |
| **Backer** | Any wallet that has backed >=1 clip | Stake USDC to back clips in Shot Markets | None (unless holding $MONTAGE) |
| **Director** | Film initiator who holds the Film NFT | Set film parameters; manage Standard Library; **cannot** back clips in own film | $MONTAGE governance (if holding) |
| **$MONTAGE Holder** | Wallet staking $MONTAGE tokens | Fee discounts on Competition Tax | Platform governance votes |
| **Platform Team** | OpenMontage founding/engineering team | No participation in Shot Markets | Emergency multi-sig (Phase 1 only) |

### Director Restrictions

Directors hold a privileged position as film initiators but face governance constraints to prevent conflicts of interest:
- Directors **cannot back any clips** in their own film's Shot Markets (smart contract enforced)
- Directors **can** set film parameters (submission deadlines, competition timing, quality thresholds, Standard Library versions)
- Directors **can** extend deadlines and manage stalled shots
- Directors **cannot** override Shot Market outcomes — the market result is final (subject to dispute resolution)

### $MONTAGE Token Role

The $MONTAGE token is used for:
- **Platform governance votes**: Proposal approval, fee changes, treasury allocation, contract upgrades
- **Fee discount staking**: Staking $MONTAGE reduces the Competition Tax rate from 5% down to a minimum of 2%
- **NOT for film-level quality selection**: Shot Markets use USDC staking, not $MONTAGE voting. This separation ensures that film quality decisions are driven by economic conviction rather than token accumulation.

---

## 4. Anti-Corruption Measures

Governance integrity requires structural safeguards beyond code — it requires rules that make corruption economically irrational. The Shot Market model provides inherent economic discipline: backing a bad clip means losing 25% of your stake.

### Conflict of Interest Rules
- **Directors cannot back clips** in their own film's Shot Markets (smart contract enforced)
- **Creators automatically back their own clip** via the Creator Bond — this is transparent, not a conflict
- **Backers cannot back multiple clips** in the same shot (must commit to a single choice, forcing conviction)

### Market Integrity
- **All backing records are public and on-chain** — anyone can audit any market at any time
- **Blind commit-reveal**: Backing amounts and clip choices are hidden during the commit phase; revealed only after commits close. This prevents bandwagoning, last-minute sniping, and strategic herding.
- **Commitment locking**: Once a commitment hash is submitted, it cannot be changed. Non-reveal forfeits the deposit entirely.

### Economic Safeguards
- **Quadratic backing weight**: The primary anti-whale mechanism. Broad community consensus always outweighs concentrated capital (see Section 2.2).
- **Maximum single-address backing**: 5,000 USDC per backer per clip (prevents extreme concentration beyond quadratic dampening)
- **Non-reveal penalty**: Failing to reveal during the reveal phase forfeits the entire deposit to the reward pool. This prevents strategic abstention.
- **Challenge Bond (2×)**: Replacement challenges require double the Creator Bond, discouraging frivolous challenges against incumbent clips.
- **Appeal bond**: 10 $MONTAGE stake required to appeal (prevents frivolous appeals; slashed on loss)

### Platform Governance Delegation
- $MONTAGE vote delegation is allowed for platform-level governance — token holders can delegate their voting power to trusted representatives
- Delegation is revocable at any time
- Delegates cannot re-delegate (no delegation chains)
- Delegation does **not** apply to Shot Markets (backing requires direct USDC deposits, not delegated tokens)

---

## 5. Progressive Decentralization Timeline

OpenMontage follows a deliberate path from centralized control to full community sovereignty:

### Phase 1: Guided Launch (Q2-Q3 2026)
- **Control**: 3-of-5 founding team multi-sig
- **Community role**: Advisory votes via Discord polls (non-binding)
- **Rationale**: Rapid iteration, bug fixes, and security responses require centralized authority. The protocol is unproven and needs the ability to pivot quickly.
- **Commitments**: All multi-sig actions logged publicly; monthly governance transparency reports

### Phase 2: Hybrid Governance (Q4 2026)
- **Control**: Multi-sig for platform operations; Shot Market competition tools (economic backing) available for all film projects
- **Community role**: Platform governance proposals via $MONTAGE token-weighted voting for fee structures, feature priorities; Shot Markets handle all film-level clip selection independently
- **Rationale**: Film-level decisions operate autonomously via Shot Markets from day one. Platform-level governance begins transitioning from team oversight to token-weighted community input during mainnet stabilization.

### Phase 3: Full DAO (2027)
- **Control**: On-chain Governor contract for all platform governance; multi-sig retained only for emergency pause
- **Community role**: Binding votes on all platform decisions — platform fees, treasury allocation, contract upgrades; expanded platform governance scope includes parameter updates and grant programs. Film-level clip selection continues via Shot Markets (unchanged)
- **Rationale**: Protocol is stable, audited, and battle-tested. Community has developed sufficient governance expertise.
- **Transition**: Multi-sig signers publicly commit to executing all DAO-approved proposals; emergency pause authority transfers to DAO with a fast-track voting mechanism (24h quorum, 67% threshold)

### Phase 4: Constitutional Governance (2028+)
- **Control**: Ratified constitution defining immutable rights and governance procedures
- **Community role**: Constitutional amendments require 75% supermajority + 30-day deliberation
- **Features**: On-chain dispute courts (Kleros-style), elected governance council for day-to-day operations, formal separation of executive/legislative/judicial functions
- **Rationale**: Mature protocols benefit from stable, predictable governance that cannot be easily captured by transient token majorities

---

## 6. Transparency Requirements

Trust in governance requires radical transparency. OpenMontage commits to the following:

### Financial Transparency
- **Monthly treasury reports**: Published on-chain and summarized in Discord `#treasury-reports`; includes all inflows, outflows, token burns, and grant disbursements
- **Real-time revenue tracking**: Public dashboard showing per-film revenue, creator earnings, and treasury balance — updated every block
- **Quarterly protocol audits**: Independent financial review of treasury operations and fee collection accuracy

### Governance Transparency
- **Public dashboard**: All governance proposals, votes, outcomes, and execution status displayed in real-time
- **Vote explorer**: Any user can look up any address's voting history across all films and platform proposals
- **Multi-sig transparency**: All multi-sig transactions published with signer identities and rationale within 24 hours of execution

### Technical Transparency
- **Open-source contracts**: All smart contract code is verified on-chain and published on GitHub
- **Audit reports**: All security audit reports published in full (no redactions except for active vulnerabilities under remediation)
- **Incident reports**: Post-mortems for any security incident, governance failure, or unplanned downtime published within 7 days

---

## Conclusion

OpenMontage governance is designed to be **fair, transparent, and progressively decentralized**. By separating film-level quality selection (Shot Markets with economic staking) from platform-level governance ($MONTAGE token voting), requiring on-chain transparency, and following a deliberate decentralization timeline, the protocol ensures that governance serves the community rather than any single stakeholder.

The Shot Market model replaces opinion-based voting with economic skin-in-the-game. Backers who consistently identify the best clips earn returns; those who back mediocre work lose a portion of their stake. Quadratic backing weight ensures that crowd wisdom always outweighs whale capital. The result: a self-sustaining ecosystem where films improve through merit-based economic competition, creators earn proportionally to their contributions, and quality judgments reflect genuine community conviction — not the capital concentration of passive holders.

---

**Version**: 1.1
**Last Updated**: 2026-03-04
**Authors**: OpenMontage Core Team
