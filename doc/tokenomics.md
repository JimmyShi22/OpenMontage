# OpenMontage Tokenomics (Crypto Economic Model)

> **Executive Summary**
>
> | Metric | Value |
> |--------|-------|
> | **Total $MONTAGE Supply** | 1,000,000,000 (1B tokens) |
> | **Initial Circulating at TGE** | ~8% |
> | **Platform Fee** | 2–5% of film revenue (used for buyback & burn) |
> | **Revenue Split** | 75% creators / 10% Film NFT holder / 5% Standard Library / 10% clip backers |
> | **Winner Selection** | Shot Market — highest quadratic-backed clip wins |
> | **Film Creation Bond** | Minimum 500 USDC (refundable upon film completion) |
> | **Creator Bond** | 50 USDC per clip submission |
> | **Loser Refund** | 75% of stake returned |
> | **Primary Network** | Base (Coinbase L2) |
>
> OpenMontage replaces traditional token-weighted voting with **Shot Markets** — per-shot prediction market competitions where community members stake real capital (USDC) to back the clips they believe are best. The clip with the broadest community support (measured via quadratic backing weight) wins, and winners earn from losers' stakes plus ongoing streaming revenue. This transforms passive voting into active quality investment, aligning economic incentives with creative excellence.

---

## 1. Asset Ownership (NFT-ization)

### 1.1 Clip NFT (Segment NFT)

Every video segment submitted by a creator is minted as an independent ERC-721 NFT containing:

**Metadata**:
- Creator wallet address
- Generation timestamp
- Standard Library version used
- AI model/tool information
- Technical specifications (resolution, duration, format)
- IPFS hash or storage pointer to video file

**Backing Pool**: Each Clip NFT maintains a registry of all backers who staked USDC on it during its Shot Market competition. This backing pool determines both competition payouts and ongoing streaming revenue distribution. The backer registry is immutable once the competition resolves — backers of a winning clip retain their revenue share as long as the clip remains merged.

**Purpose**: Establishes immutable proof of authorship and contribution to the collaborative work.

### 1.2 Film NFT

The Film NFT (formerly “Root NFT” / “Movie NFT”) is an ERC-721 token representing the film as a composite creative work.

**What the Film NFT Represents**:
- **Copyright claim**: The holder asserts creative direction and copyright over the composite work
- **Revenue rights**: 10% of ongoing streaming revenue (the “Director Share”)
- **Governance authority**: Set film parameters — shot count, competition timing, quality thresholds, Standard Library versions
- **Dynamic pointer array**: Contains references to all currently-merged Clip NFTs

**Transferability**: The Film NFT is fully transferable. A director can sell their film (and its associated revenue stream) to another party. The new holder inherits all director rights and the 10% revenue share.

**Structure Example**:
```json
{
  "film_id": "space-odyssey-2077",
  "title": "Space Odyssey 2077",
  "film_nft_address": "0x...",
  "director": "0x123...",
  "creation_bond": 10000,
  "bond_status": "partially_refunded",
  "standard_library_version": "1.2.0",
  "total_shots": 200,
  "merged_shots": 180,
  "segments": [
    {"shot_id": "001", "clip_nft": "0xabc...", "creator": "0x456...", "duration": 8.5},
    {"shot_id": "002", "clip_nft": "0xdef...", "creator": "0x789...", "duration": 12.3}
  ],
  "total_runtime": 5400,
  "last_updated": "2026-06-15T14:30:00Z"
}
```

### 1.3 Backing Position

When a backer stakes USDC on a clip during a Shot Market competition, the contract records a **Backing Position** — a staking record (not a separate NFT) that tracks:

- Backer wallet address
- Clip NFT backed
- USDC amount deposited
- Whether the position is winning or losing (after resolution)
- Ongoing revenue claim (for winning positions on merged clips)

Backing positions are non-transferable. A backer's ongoing revenue share is tied to their wallet and the specific clip they backed.

---

## 2. Film Creation & Film NFT

### 2.1 Film Creation Bond

To create a film on OpenMontage, the director must pay a **Film Creation Bond** — a refundable deposit that signals commitment and seeds the film's treasury.

**Bond Structure**:
- **Minimum Bond**: 500 USDC
- **Recommended Bond**: 50 USDC × number of planned shots (e.g., 200-shot film → 10,000 USDC recommended)
- **No maximum**: Higher bonds signal greater director commitment and attract more contributors

**Bond Allocation**:

| Portion | % | Purpose |
|---------|---|---------|
| Film Treasury Seed | 50% | Funds initial infrastructure (Standard Library hosting, CDN, IPFS pinning) |
| Completion Escrow | 50% | Locked, refundable to director upon film completion |

**Refund Conditions**: Film completion is defined as **≥80% of declared shots having a merged clip**. Upon completion, the escrowed 50% is returned to the director.

**Film Abandonment**: If no new submissions or director activity occur for 90 consecutive days:
1. Escrowed portion is distributed pro-rata to all creators who submitted clips (based on number of submissions)
2. Treasury seed portion remains in the Film Treasury for outstanding obligations
3. Film is marked as “Abandoned” — existing merged clips continue to earn streaming revenue if the film has viewers

**Example**:
- Director creates a 200-shot sci-fi film with a 10,000 USDC bond
- 5,000 USDC → Film Treasury (for CDN costs, IPFS pinning)
- 5,000 USDC → Completion Escrow
- Film reaches 180 merged shots (90% > 80% threshold) → Director claims 5,000 USDC back
- Director's net cost: 5,000 USDC (the Treasury seed, which funded real infrastructure)

### 2.2 Film NFT Minting

When the Film Creation Bond is paid, a Film NFT (ERC-721) is minted to the director's wallet. This NFT carries the copyright claim, the 10% revenue share, and the governance authority over the film's parameters. The Film NFT is the director's primary asset — it can be held indefinitely for streaming revenue or sold to transfer all rights.

### 2.3 Film Ticket NFTs (Crowdfunding)

Directors can optionally issue **Film Ticket NFTs** to crowdfund production before or during the filmmaking process.

**How They Work**:
- Limited supply set by the director (e.g., 10,000 tickets at 50 USDC each = 500,000 USDC production budget)
- Holders receive:
  - Lifetime streaming access to the film
  - **10% of film revenue** (drawn from the creator pool — see Section 5 for adjusted split)
  - Behind-the-scenes content and exclusive access

**Revenue Integration**: When Film Ticket NFTs exist, the creator pool shrinks from 75% to 65%, and the 10% difference flows to Ticket NFT holders. All other shares remain unchanged. Films without Film Ticket NFTs retain the standard 75/10/5/10 split.

---

## 3. Shot Competition Market — The Core Innovation

The Shot Market is the central mechanism replacing community voting. Each shot in a film becomes a **bounded prediction market** where participants stake real capital to back the clip they believe is best.

### 3.1 Overview

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  SUBMISSION       │────▶│  BACKING          │────▶│  RESOLUTION      │
│  PHASE            │     │  PHASE            │     │                  │
│  (14 days)        │     │  (7 days)         │     │  Winner          │
│                   │     │                   │     │  determined by   │
│  Creators submit  │     │  Community backs  │     │  highest         │
│  clips + stake    │     │  clips with USDC  │     │  quadratic       │
│  Creator Bond     │     │  (blind commit-   │     │  backing weight  │
│                   │     │   reveal)         │     │                  │
└──────────────────┘     └──────────────────┘     └──────────────────┘
```

All Shot Market transactions are denominated in **USDC** (or platform-approved stablecoin) to eliminate volatility risk. Participants' stakes gain or lose value only due to competition outcomes, not crypto market fluctuations.

### 3.2 Submission Phase

**Duration**: 14 days (configurable by director, minimum 7 days, maximum 30 days)

**Process**:
1. Creator generates a video segment using the film's Standard Library
2. Creator calls `ShotMarket.submitClip(filmId, shotId, videoHash)` with a **Creator Bond** deposit of **50 USDC**
3. The clip is minted as a Clip NFT (ERC-721) and enters the shot competition
4. Multiple creators can submit clips for the same shot — no limit on submissions

**Creator Bond**:
- **Amount**: 50 USDC (fixed across all films; adjustable by DAO governance)
- **Purpose**: Prevents spam submissions, signals creator confidence
- **Refund**: Winners get full bond back + reward; losers get 75% back

**Minimum Submissions**: The backing phase only opens if a shot has **≥2 submissions**. If only 1 clip is submitted after the deadline, the director may auto-merge it (no competition, Creator Bond fully refunded). If 0 submissions, the shot remains open.

### 3.3 Backing Phase (Blind Commit-Reveal)

**Duration**: 7 days total
- **Commit Period**: First 5 days
- **Reveal Period**: Last 2 days

#### Step 1 — Commit (Days 1–5)

1. Backer watches all submitted clips and evaluates quality
2. Backer creates a commitment: `commitment = keccak256(clipId, amount, salt)`
3. Backer calls `ShotMarket.commitBacking(shotId, commitment)` and deposits their USDC
4. The contract records the commitment hash and locks the deposit
5. **No information is revealed** — neither the chosen clip nor the amount is visible to others

#### Step 2 — Reveal (Days 6–7)

1. Backer calls `ShotMarket.revealBacking(shotId, clipId, amount, salt)`
2. Contract verifies `keccak256(clipId, amount, salt) == stored commitment`
3. Backing is recorded for the chosen clip

**Backing Parameters**:

| Parameter | Value |
|-----------|-------|
| Minimum backing | 10 USDC per backer per clip |
| Maximum backing | 5,000 USDC per backer per clip |
| Backers per clip | No limit |
| Multiple clip backing | **Not allowed** — a backer must choose one clip per shot (forces conviction) |

**Non-Reveal Penalty**: If a backer fails to reveal during the reveal period, their deposit is **forfeited** and added to the reward pool. This prevents strategic non-revelation.

### 3.4 Winner Determination — Quadratic Backing Weight

If the winner were determined by total USDC staked (simple sum), a single wealthy actor could guarantee any clip wins. **Quadratic Backing Weight** ensures that breadth of support matters more than depth of capital.

**Formula**:
```
BackingWeight(clip_j) = √(CreatorBond_j) + Σ_i √(deposit_i)

    for all backers i who backed clip j
```

The creator's own bond counts toward the clip's weight.

**Why Crowds Beat Whales — Concrete Example**:

| Scenario | Raw Total | Quadratic Weight |
|----------|-----------|----------------|
| 1 whale backs Clip A with 10,000 USDC | 10,000 | √10,000 = **100** |
| 100 people back Clip B with 100 USDC each | 10,000 | 100 × √100 = **1,000** |
| 25 people back Clip C with 400 USDC each | 10,000 | 25 × √400 = **500** |

All three scenarios have the same total capital (10,000 USDC), but:
- Clip B (100 backers) has **10× the weight** of Clip A (1 whale)
- Clip C (25 backers) has **5× the weight** of Clip A (1 whale)

**Result**: The clip with the broadest community consensus wins, regardless of any single backer's wealth. **Winner** is the clip with the highest `BackingWeight` after all reveals are processed. Ties (extremely unlikely with real numbers) are broken by first submission time.

### 3.5 Worked Example — Three Clips Competing (Shot 042)

**Setup**: Shot 042 in “Space Odyssey 2077” receives 3 clip submissions.

| Clip | Creator Bond | Backers | Total Backer Deposits | Total Deposited | Quadratic Weight |
|------|-------------|---------|----------------------|-----------------|----------------|
| **Clip A** | 50 USDC | 30 backers, avg 40 USDC each | 1,200 USDC | 1,250 USDC | √50 + 30×√40 = 7.07 + 189.74 = **196.81** |
| **Clip B** | 50 USDC | 15 backers, avg 30 USDC each | 450 USDC | 500 USDC | √50 + 15×√30 = 7.07 + 82.16 = **89.23** |
| **Clip C** | 50 USDC | 8 backers, avg 25 USDC each | 200 USDC | 250 USDC | √50 + 8×√25 = 7.07 + 40.00 = **47.07** |

**Winner**: Clip A — highest quadratic weight (196.81)

Despite Clip A having more total capital staked, it won primarily because it attracted the most independent backers (30 vs. 15 vs. 8), demonstrating quadratic backing's preference for crowd consensus.

---

## 4. Payout Structure

After a Shot Market resolves, payouts are calculated deterministically by the smart contract. The system ensures **100% funds conservation** — every USDC deposited is accounted for.

### 4.1 Definitions

| Symbol | Meaning |
|--------|--------|
| `D_w` | Total USDC deposited on winning clip (creator bond + all winning backer deposits) |
| `D_l` | Total USDC deposited on all losing clips (creator bonds + all losing backer deposits) |
| `τ` | Competition Tax rate = **5%** |
| `ρ` | Loser Refund rate = **75%** |
| `α` | Creator Reward Share = **50%** of reward pool |
| `β` | Backer Reward Share = **50%** of reward pool |

### 4.2 Winner Payouts

All winning deposits are returned in full, plus a share of the reward pool:

```
Winning Creator Payout = CreatorBond (returned in full)
                       + α × RewardPool
                       = CreatorBond + 50% × RewardPool

Winning Backer i Payout = deposit_i (returned in full)
                        + (deposit_i / TotalWinnerBackerDeposits) × β × RewardPool
```

### 4.3 Loser Payouts

Losers receive 75% of their stake back — a significant refund that limits downside risk:

```
Losing Creator j:  refund = 75% × CreatorBond_j
Losing Backer k:   refund = 75% × deposit_k
```

### 4.4 Competition Tax & Reward Pool

```
Competition Tax = τ × D_l = 5% × D_l         → flows to Film Treasury
Loser Refund    = ρ × D_l = 75% × D_l        → returned to losers
Reward Pool     = D_l × (1 - τ - ρ)
                = D_l × (1 - 0.05 - 0.75)
                = 20% × D_l                   → split between winning creator (50%) and winning backers (50%)
```

### 4.5 Funds Conservation Proof

Every USDC deposited is accounted for:

```
Total In  = D_w + D_l

Total Out = D_w                              (all winning deposits returned in full)
          + ρ × D_l                          (75% loser refunds)
          + τ × D_l                          (5% competition tax → Film Treasury)
          + (1 - τ - ρ) × D_l               (20% reward pool → distributed to winners)
          = D_w + D_l × (ρ + τ + 1 - τ - ρ)
          = D_w + D_l × 1
          = D_w + D_l  ✓
```

### 4.6 Large Example — $10,000 Total Staked

**Setup**: A popular shot competition with significant capital.

| Side | Deposits |
|------|----------|
| Winning clip (1 creator + backers) | D_w = 4,000 USDC |
| Losing clips (creators + backers) | D_l = 6,000 USDC |
| **Total** | **10,000 USDC** |

**Calculation**:

| Flow | Formula | Amount |
|------|---------|--------|
| Competition Tax | 5% × 6,000 | **300 USDC** → Film Treasury |
| Loser Refund | 75% × 6,000 | **4,500 USDC** → returned to losers |
| Reward Pool | 20% × 6,000 | **1,200 USDC** |
| → Winning Creator Share | 50% × 1,200 | **600 USDC** |
| → Winning Backers Share | 50% × 1,200 | **600 USDC** (split proportionally) |

**Final Payouts**:

| Recipient | Calculation | Payout |
|-----------|-------------|--------|
| Winning Creator | 50 (bond back) + 600 (reward) | **650 USDC** (1,200% ROI on bond) |
| Winning Backers (total) | 3,950 (deposits back) + 600 (reward) | **4,550 USDC** (~15.2% ROI) |
| Losing Participants | 75% × 6,000 | **4,500 USDC** (25% loss) |
| Film Treasury | Competition Tax | **300 USDC** |
| **Total Out** | | **10,000 USDC ✓** |

### 4.7 Small Example — $200 Total Staked

**Setup**: A small indie film shot with modest participation.

| Clip | Creator Bond | Backers | Total Deposited |
|------|-------------|---------|----------------|
| **Clip X** (winner) | 50 USDC | 3 backers: 10, 15, 20 USDC | 95 USDC |
| **Clip Y** (loser) | 50 USDC | 2 backers: 10, 10 USDC | 70 USDC |
| **Total** | | | **165 USDC** |

**Losing Deposits**: D_l = 70 USDC

| Flow | Amount |
|------|--------|
| Competition Tax | 5% × 70 = **3.50 USDC** |
| Loser Refund | 75% × 70 = **52.50 USDC** |
| Reward Pool | 20% × 70 = **14.00 USDC** |
| → Winning Creator | 50% × 14 = **7.00 USDC** |
| → Winning Backers | 50% × 14 = **7.00 USDC** |

**Final Payouts**:

| Recipient | Payout |
|-----------|--------|
| Creator X | 50 + 7 = **57 USDC** (14% ROI) |
| Backers of X | 45 + 7 = **52 USDC** (~15.6% ROI combined) |
| Creator Y | 75% × 50 = **37.50 USDC** (25% loss) |
| Backers of Y | 75% × 20 = **15 USDC** (25% loss) |
| Film Treasury | **3.50 USDC** |
| **Total Out** | **165 USDC ✓** |

The model works at any scale — modest but meaningful returns even in small competitions.

---

## 5. Revenue Streams (Post-Release)

### 5.1 Revenue Sources

Films generate income through multiple channels:

1. **Pay-Per-View**: Audience streaming fees
2. **Advertising Revenue**: Platform ad revenue share
3. **Copyright Licensing**: Studio/platform distribution deals
4. **Merchandise**: Official merch tied to NFT holders
5. **Film Ticket NFTs**: Crowdfunding pre-sales
6. **Competition Tax Revenue**: 5% of losing stakes from all Shot Markets

All revenue flows into a **Treasury Smart Contract** for transparent, automated distribution.

### 5.2 Revenue Distribution

**Platform Fee**: 2–5% of all film revenue is taken first and used for $MONTAGE buyback & burn.

#### Standard Split (No Film Ticket NFTs)

| Stakeholder | Share | Description |
|-------------|-------|-------------|
| **Clip Creators** | 75% | Proportional to screen time of merged clips |
| **Film NFT Holder** (Director) | 10% | Compensation for vision, Standard Library, management |
| **Standard Library Contributors** | 5% | Split among contributors who built Standard Library assets |
| **Clip Backers Pool** | 10% | Distributed to backers of currently-merged clips |

#### Modified Split (With Film Ticket NFTs)

| Stakeholder | Share | Description |
|-------------|-------|-------------|
| **Clip Creators** | 65% | Reduced to fund Ticket NFT holders |
| **Film NFT Holder** (Director) | 10% | Unchanged |
| **Standard Library Contributors** | 5% | Unchanged |
| **Clip Backers Pool** | 10% | Unchanged |
| **Film Ticket NFT Holders** | 10% | Crowdfunding investors |

### 5.3 Clip Creator Revenue Formula

```
Creator_Earnings = CreatorPool × (Clip_Duration / Total_Film_Duration)

Where:
  CreatorPool = TreasuryRevenue × 75%  (or 65% with Film Tickets)
  TreasuryRevenue = TotalRevenue × (1 - PlatformFee)
```

Each Clip NFT in the current Main Branch earns revenue proportional to its **screen time**. Revenue flows continuously while the segment remains merged.

### 5.4 Clip Backer Revenue Formula

Backers of currently-merged clips earn a share of the **Clip Backers Pool** (10% of treasury revenue). Distribution is proportional to each backer's share of the winning backing for that clip, weighted by the clip's screen time.

```
BackerPool = TreasuryRevenue × 10%

For clip j (merged) with backer i:
  Backer_i_Earnings = BackerPool
                    × (Clip_j_Duration / Total_Film_Duration)
                    × (deposit_i / TotalWinnerBackerDeposits_j)
```

Backer returns from streaming are modest per-clip but compound across a portfolio. A backer with winning positions in 20 clips across 5 films earns meaningful passive income.

### 5.5 Revenue Example

**Film**: 90-minute film, $50,000/month revenue, 3% platform fee

```
TreasuryRevenue = $50,000 × 0.97 = $48,500/month

CreatorPool     = $48,500 × 75% = $36,375
DirectorShare   = $48,500 × 10% = $4,850
StdLibShare     = $48,500 × 5%  = $2,425
BackerPool      = $48,500 × 10% = $4,850
```

**Creator with 30-second clip**:
- Earnings = $36,375 × (30 / 5400) = **$202.08/month**

**Creator with 2-minute clip**:
- Earnings = $36,375 × (120 / 5400) = **$808.33/month**

**Backer who staked 100 USDC on a winning 30-second clip** (clip had 1,000 USDC total backer deposits):
- Earnings = $4,850 × (30 / 5400) × (100 / 1,000) = **$2.69/month**

### 5.6 What Happens When a Clip is Replaced?

When a merged clip is replaced via the Challenge mechanism (Section 6):
1. **Old creator**: Streaming revenue stops immediately for that clip
2. **Old backers**: Streaming revenue stops for their positions in that clip
3. **New creator + new backers**: Begin earning streaming revenue immediately
4. **Clip NFT**: Old clip's NFT still exists (tradeable, has historical value) but status changes to `"archived"`

---

## 6. Replacement Challenge

Once a clip is merged, it can be replaced if a genuinely superior version appears. The replacement process uses the same Shot Market mechanism but with deliberate friction to protect incumbent creators.

### 6.1 Challenge Process

1. **Challenger submits** a new clip for an already-merged shot
2. **Challenge Bond**: Challenger must pay **2× Creator Bond (100 USDC)** — the higher barrier discourages frivolous challenges
3. **Notification**: Incumbent creator is notified and has 7 days to respond with an improved version (at no additional cost)
4. **Mini-Competition**: A compressed Shot Market opens:
   - Submission Phase: 7 days (incumbent's response window)
   - Backing Phase: 5 days (3 days commit, 2 days reveal)
5. **Incumbency Advantage**: The incumbent clip starts with a **synthetic backing weight equal to 50% of its original winning backing weight**. The challenger must overcome this advantage.
6. **Resolution**: Same quadratic backing weight formula as initial competitions

### 6.2 Challenge Outcomes

**If Challenger Wins**:
- Incumbent clip → status `"archived"` (revenue stops)
- Challenger clip merged into Main Branch (revenue starts)
- Challenge Bond → refunded in full + reward pool share
- New backers begin earning streaming revenue

**If Incumbent Survives**:
- Incumbent clip remains merged
- Challenger → 75% of Challenge Bond refunded (loses 25 USDC)
- Incumbent → receives 50% of the forfeited 25% of Challenge Bond as a “defense bonus”

### 6.3 Challenge Cooldown

After any challenge (successful or not), the same shot **cannot be challenged again for 21 days**. This prevents harassment of incumbent creators and provides stability.

---

## 7. $MONTAGE Token Role

In the new Shot Market model, $MONTAGE shifts from a voting power token to a **governance and utility token**. Curation is now handled by USDC-denominated Shot Markets; $MONTAGE governs the platform itself.

### 7.1 Use Cases

| Use Case | Description |
|----------|-------------|
| **Platform Governance** | Vote on platform fee changes, contract upgrades, treasury allocation, grants |
| **Fee Reduction** | Staking $MONTAGE reduces Competition Tax for the staker (from 5% down to minimum 2%) |
| **Participation Rewards** | Earned by successful creators and backers as bonus incentives |
| **Value Accrual** | Platform fee (2–5%) buys back and burns $MONTAGE (deflationary) |
| **Access** | Priority access to premium Standard Libraries, early access to new tools |

### 7.2 Fee Reduction via Staking

Backers who stake $MONTAGE receive a reduced Competition Tax rate on their losing positions:

```
EffectiveTaxRate = max(2%, 5% - StakingDiscount)

Where:
  StakingDiscount = min(3%, StakedMONTAGE / 10,000 × 1%)
```

| $MONTAGE Staked | Effective Tax Rate | Discount |
|-----------------|-------------------|----------|
| 0 | 5% (standard) | None |
| 10,000 | 4% | 1% |
| 20,000 | 3% | 2% |
| 30,000+ | 2% (minimum) | 3% |

The difference between the standard and discounted tax rate stays in the backer's refund (losers) or isn't charged (winners).

### 7.3 Value Accrual Mechanism

```
Platform Fee Revenue → $MONTAGE Buyback → Burn

Each quarter:
1. Platform collects 2-5% of all film revenue across the platform
2. 80% of collected fees used to buy $MONTAGE on open market
3. Purchased tokens are burned (permanently removed from supply)
4. 20% of fees retained for platform operations
```

### 7.4 Token Allocation

| Allocation | % | Amount | Vesting |
|------------|---|--------|---------|
| Community Rewards | 30% | 300,000,000 | Released over 5 years, earned through platform activity (winning competitions, successful backing, active participation) |
| Ecosystem & Grants | 20% | 200,000,000 | 3-year linear vest, governed by DAO, for film projects, developer grants, Standard Library bounties |
| Team & Advisors | 15% | 150,000,000 | 4-year vest, 1-year cliff |
| Treasury Reserve | 15% | 150,000,000 | Unlocked but requires DAO vote to spend |
| Public Sale / IDO | 10% | 100,000,000 | 30% at TGE, remaining over 12 months |
| Seed & Strategic | 7% | 70,000,000 | 2-year vest, 6-month cliff |
| Liquidity Provision | 3% | 30,000,000 | At TGE for DEX liquidity |

### 7.5 Circulating Supply Projection

TGE is projected for Q1 2027.

| Year | New Tokens Released | Cumulative Circulating | % of Total Supply |
|------|--------------------|-----------------------|-------------------|
| TGE (Q1 2027) | 80,000,000 | 80,000,000 | 8.0% |
| End Year 1 (2027) | 155,000,000 | 235,000,000 | 23.5% |
| End Year 2 (2028) | 175,000,000 | 410,000,000 | 41.0% |
| End Year 3 (2029) | 160,000,000 | 570,000,000 | 57.0% |
| End Year 4 (2030) | 130,000,000 | 700,000,000 | 70.0% |
| End Year 5 (2031) | 100,000,000 | 800,000,000 | 80.0% |
| Long-tail (2032+) | 200,000,000 | 1,000,000,000 | 100.0% |

**Notes**:
- TGE circulating comprises: 30% of Public Sale (30M) + Liquidity Provision (30M) + early Community Rewards (20M) = ~80M tokens
- Buyback & burn from platform fees reduces effective circulating supply over time, partially offsetting new unlocks
- Treasury Reserve tokens (150M) are technically unlocked but require DAO governance votes and are excluded from circulating supply until deployed

---

## 8. Game Theory & Economic Sustainability

### 8.1 Nash Equilibrium — Creators

**Dominant strategy**: Submit the highest quality clip possible.

Higher quality attracts more independent backers → higher quadratic weight → more likely to win → Creator Bond returned + Reward Pool share + ongoing streaming revenue. Submitting low-quality work attracts few backers, leading to low quadratic weight and a 25% bond loss.

The replacement mechanic amplifies this: even after a clip is merged, it can be displaced by a superior submission at any time. Creators cannot rest on past success. Each new AI model release (Sora v4, Runway Gen-4) triggers waves of improved submissions, and films organically improve over time.

**Equilibrium**: All creators submit their best work.

### 8.2 Nash Equilibrium — Backers

**Dominant strategy**: Back the clip you genuinely assess as highest quality.

Blind backing eliminates herding — the only way to consistently earn returns is to correctly identify winners. Backing a clip you believe is inferior (hoping others will also make mistakes) is a losing strategy long-term. Random backing gives a 1/N chance of winning where N = number of clips, yielding negative expected value due to the 25% loss on wrong picks.

Self-dealing (backing a friend's clip regardless of quality) only profits if the clip actually wins. Friendship doesn't improve clip quality.

**Equilibrium**: Backers develop quality assessment skills over time; the market rewards genuine curation expertise.

### 8.3 Nash Equilibrium — Directors

**Dominant strategy**: Invest meaningfully in the Film Creation Bond and Standard Library.

A higher Film Creation Bond signals commitment and attracts more creators. A well-built Standard Library (character LoRAs, style guides, ControlNets) produces more visually consistent submissions, leading to a higher-quality film, more audience revenue, and a larger 10% director share. The bond's 50% completion refund ensures that directors who follow through recoup half their investment, making the effective cost of a 10,000 USDC bond only 5,000 USDC for a completed film.

**Equilibrium**: Directors invest in quality infrastructure and follow through to completion.

### 8.4 Attack Vectors & Mitigations

| Attack | Description | Mitigation |
|--------|-------------|------------|
| **Whale dominance** | Rich actor deposits huge amount on one clip | Quadratic backing: √(10,000) = 100 weight, while 100×√(100) = 1,000 weight. Crowd wins 10:1 with same capital. |
| **Sybil backing** | Create many wallets to maximize quadratic weight | Minimum 10 USDC per back, wallet age requirement (7 days), optional KYC for weight bonus |
| **Creator-backer collusion** | Creator recruits friends to back their clip | Blind backing prevents coordination. Friends still lose 25% if wrong. Financial incentive overwhelms social pressure at scale. |
| **Self-funding** | Creator backs own clip heavily | √(deposit) weight means self-backing provides minimal advantage vs. many genuine backers |
| **Quality flooding** | Submit many mediocre clips to split backing | Each submission costs 50 USDC Creator Bond. Backers concentrate on 1-2 best clips regardless. |
| **Timing attacks** | Track on-chain commits to infer patterns | Commit-reveal hides both clip choice AND amount. Only the hash is visible. Zero information leakage. |

### 8.5 Break-Even Analysis for Creators

**Cost to compete**:
- Creator Bond: 50 USDC
- Compute costs (AI generation): $5–$20
- Time investment: 2–4 hours

**If you win**:
- Bond returned: 50 USDC
- Competition reward: 50% × (20% × D_l) — varies by competition size
- Ongoing streaming revenue: proportional to screen time, indefinitely

**If you lose**:
- Bond refund: 37.50 USDC (75%)
- Net loss: 12.50 USDC + compute costs

**Break-even scenario**: A 30-second clip in a film earning $50,000/month generates approximately $202/month in passive streaming revenue. Even after accounting for several lost competitions (each costing ~$15–$30), a single win pays for itself within the first month. A creator who wins 1 in 3 competitions is profitable.

**Portfolio perspective**: A creator with 3 merged segments in a $50,000/month film earns approximately $600+/month — a meaningful passive income stream that compounds across multiple films.

---

## 9. Anti-Gaming Mechanisms

### 9.1 Blind Commit-Reveal

The commit-reveal scheme is the primary anti-manipulation mechanism:

1. **Commit phase (5 days)**: Backers submit `keccak256(clipId, amount, salt)` — an opaque hash. Neither the chosen clip nor the amount is visible to anyone.
2. **Reveal phase (2 days)**: Backers reveal their choices. The contract verifies against the stored hash.
3. **Non-reveal penalty**: Unrevealed deposits are forfeited to the reward pool, preventing strategic withholding.

**What this prevents**:
- **Bandwagoning**: Backers can't see which clip is “winning” and pile on
- **Last-minute sniping**: Can't dump money at the last second based on observed totals
- **Social signaling**: No way to telegraph your backing to influence others
- **Strategic non-revelation**: Forfeiture penalty makes it always better to reveal

### 9.2 Quadratic Backing Weight

The mathematical properties of quadratic weighting provide structural resistance to capital concentration:

```
Whale (1 backer × $10,000):    Weight = √10,000 = 100
Crowd (100 backers × $100):    Weight = 100 × √100 = 1,000
```

To match 100 genuine backers, a Sybil attacker would need:
- 1,000 fake wallets at minimum 10 USDC each = $10,000 + gas for 2,000 transactions (commit + reveal)
- This costs the same as the legitimate side but requires enormous operational complexity and is easily detectable

### 9.3 Minimum Thresholds

| Threshold | Value | Purpose |
|-----------|-------|---------|
| Creator Bond | 50 USDC | Prevents spam submissions |
| Minimum Backing | 10 USDC | Makes Sybil attacks expensive |
| Maximum Backing | 5,000 USDC per clip | Caps single-backer influence |
| Minimum Submissions | 2 clips per shot | Ensures genuine competition |
| Wallet Age | 7 days minimum | Prevents instant Sybil wallet creation |

### 9.4 Cooldown Periods

- **Challenge Cooldown**: 21 days after any challenge attempt (successful or not)
- **Purpose**: Prevents harassment of incumbent creators through repeated frivolous challenges
- **Effect**: Gives incumbent clips stability and makes challenges a considered investment

---

## 10. Economic Flywheel

```
Higher Quality Films
       ↓
More Audience Revenue
       ↓
Higher Creator Earnings + Higher Backer Returns
       ↓
Attracts Better Creators + More Backers
       ↓
More Shot Market Competition + Deeper Curation Capital
       ↓
Higher Quality Films (cycle repeats)
```

**Reinforcing Loops**:

1. **Creator loop**: Higher film revenue → higher earnings per screen-second → top AI creators compete for slots → better clips → more audience → more revenue
2. **Backer loop**: Successful backers earn competition payouts + streaming revenue → reinvest in more competitions → deeper liquidity → stronger quality signal → better films
3. **Director loop**: Successful films attract more contributors → more competition → higher quality → more revenue → director's 10% share grows → incentivizes more directors to launch films
4. **Standard Library loop**: Better Standard Libraries → more visually consistent films → higher audience satisfaction → more revenue → creators of Standard Library assets earn their 5% → incentivizes better tooling
5. **Token loop**: More platform revenue → more $MONTAGE buyback & burn → reduced supply → higher token value → more staking for fee discounts → more platform engagement

---

## 11. Case Study: Revenue Distribution Example

### Film: "Cyberpunk Dreams" (90 minutes, 150 shots)

**Monthly Revenue**: $50,000
**Platform Fee**: 3%
**Treasury Revenue**: $48,500/month

**Distribution**:

| Stakeholder | Share | Monthly Amount |
|-------------|-------|----------------|
| Clip Creators | 75% | $36,375 |
| Film NFT Holder (Director) | 10% | $4,850 |
| Standard Library Contributors | 5% | $2,425 |
| Clip Backers Pool | 10% | $4,850 |
| **Total** | **100%** | **$48,500** |

**Top Creator** — Creator_A has 3 merged segments totaling 5 minutes (5.6% of runtime):
- Streaming revenue: $36,375 × (300s / 5400s) = **$2,020.83/month**

**Average Creator** — 1 segment, 36 seconds (0.67% of runtime):
- Streaming revenue: $36,375 × (36s / 5400s) = **$242.50/month**

**Active Backer** — Backed winning clips for 10 shots, averaging 100 USDC per position (across clips totaling 4 minutes):
- Monthly backer revenue: $4,850 × (240s / 5400s) × (1,000 / avg 2,000 per clip) ≈ **$107.78/month**
- Plus competition payout profits from winning those 10 Shot Markets

**All earnings are passive** as long as clips remain in the Main Branch.

---

## 12. Changes from Previous Model

| Aspect | v1.x (Old Model) | v2.0 (New Model) | Rationale |
|--------|-------------------|-------------------|-----------|
| **Winner selection** | Token-weighted community voting | Shot Market: highest quadratic-backed clip wins | Skin-in-the-game produces stronger quality signal than costless voting |
| **Curation mechanism** | Stake $MONTAGE → vote (opinion) | Stake USDC → back clips (investment) | Real financial stakes drive careful quality assessment |
| **Curation reward** | 5% of streaming revenue | 10% of streaming revenue + competition payouts | Greater reward attracts deeper curation capital |
| **Film creation** | Free (no bond) | Film Creation Bond (min 500 USDC) | Filters out uncommitted projects; seeds infrastructure |
| **Submission cost** | Free (gas only) | Creator Bond: 50 USDC per clip | Prevents spam; signals creator confidence |
| **Loser outcome** | No financial loss (just lost time) | 25% of stake lost (75% refund) | Creates real downside risk for poor quality |
| **Winner outcome** | Clip merged, earns streaming revenue | Clip merged + competition payout + streaming revenue | Additional upfront reward for quality work |
| **Revenue split** | 80/15/5 (creators/director+stdlib/curators) | 75/10/5/10 (creators/director/stdlib/backers) | Dedicated backer pool incentivizes curation investment |
| **Anti-manipulation** | Quadratic voting (optional) | Quadratic backing + blind commit-reveal + minimum stakes | Multiple reinforcing mechanisms prevent gaming |
| **$MONTAGE role** | Voting power (stake for influence) | Governance + fee discounts (no voting power) | Separates governance from curation; USDC handles curation |
| **Replacement mechanism** | 20% improvement signal + 60% supermajority vote | Challenge Bond (2× Creator Bond) + economic competition | Market-based replacement is more reliable than voting |
| **Core contract** | Voting.sol | ShotMarket.sol (Voting.sol removed) | New mechanism requires new contract architecture |

---

## Conclusion

OpenMontage's Shot Market model transforms filmmaking from a voting-based system into a **prediction market for creative quality**. By requiring real financial stakes for curation, the platform aligns incentives across all participants: creators compete on quality, backers compete on judgment, and directors invest in infrastructure that attracts both.

**Key Innovation**: The combination of quadratic backing weight (crowd consensus beats capital concentration), blind commit-reveal (independent judgment beats herding), and ongoing streaming revenue for backers (long-term skin in the game) creates the first truly market-driven creative curation system — where films improve over time and all stakeholders earn proportionally to their contribution.

---

**Version**: 2.0
**Last Updated**: 2026-03-04
**Authors**: OpenMontage Core Team
