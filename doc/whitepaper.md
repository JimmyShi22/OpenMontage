# OpenMontage Whitepaper

**Version 1.2 — Git for Movies**

---

## Abstract

OpenMontage is a decentralized platform for collaborative filmmaking that solves the three fundamental barriers to AI-powered crowdsourced video production:

1. **Visual/audio consistency** via mandatory Standard Library dependencies
2. **Economic sustainability** through NFT-based ownership and streaming revenue
3. **Quality assurance** using version control and community governance

By combining Git-style workflows, blockchain tokenomics, and AI generation tools, OpenMontage enables anyone to contribute professional video segments to evolving films while earning proportional revenue.

---

## 1. Introduction

### 1.1 The AI Video Revolution

AI tools like Sora, Runway, and Pika have democratized video creation. However, producing a **complete, high-quality film** through crowdsourced AI generation faces critical challenges:

**The Consistency Problem**  
Different creators using AI tools produce incompatible visual styles, character appearances, and audio — making collaborative work look disjointed.

**The Incentive Problem**  
Video generation requires significant computational costs and creative effort. Without economic rewards, contributors lack motivation to participate.

**The Quality Problem**  
Traditional filmmaking is “append-only” — once released, films cannot improve. Yet AI technology advances rapidly, enabling better lighting, motion, and effects.

### 1.2 Our Solution: Git for Movies

OpenMontage applies software development principles to filmmaking:

- **Modularization**: Scripts decomposed into discrete Shots (like functions in code)
- **Dependency management**: Mandatory Standard Library ensures consistency (like npm packages)
- **Version control**: Better segments replace existing ones via Pull Requests
- **Transparent economics**: Blockchain-based ownership and automated revenue distribution

**Vision**: A future where any script becomes a professional film through global collaboration, with creators earning fairly based on merit.

---

## 2. Core Architecture

### 2.1 The Film Repository

Each film is organized as a **Git repository**:

```
/space-odyssey-2077/
├── film.json                    # Root manifest (metadata + segment pointers)
├── README.md                    # Project overview
├── script.md                    # Full screenplay
├── standard-library/            # ⭐ THE CRITICAL INNOVATION
│   ├── characters/
│   │   ├── protagonist.safetensors    # LoRA model
│   │   ├── protagonist.json           # Metadata (training data, version)
│   │   └── antagonist.safetensors
│   ├── style/
│   │   ├── art-direction.png          # Reference images
│   │   ├── color-palette.json
│   │   └── lighting-guide.md
│   ├── prompts/
│   │   └── templates.json             # Standardized prompt structures
│   ├── controlnets/
│   │   └── character-rig.zip          # 3D skeletal controls
│   └── voices/
│       ├── protagonist-voice.model
│       └── antagonist-voice.model
├── segments/
│   ├── 001-opening/
│   │   ├── segment.md                 # Script + requirements
│   │   ├── segment.json               # Metadata (selected Clip NFT pointer)
│   │   └── submissions/
│   │       ├── 0xabc-clip.json        # Clip NFT metadata
│   │       ├── 0xdef-clip.json
│   │       └── ...
│   ├── 002-introduction/
│   │   └── ...
│   └── ...
├── assets/
│   ├── props/
│   ├── locations/
│   └── music/
└── governance/
    ├── markets.json                   # Shot Market records
    └── treasury.json                  # Revenue distribution config
```

### 2.2 The Standard Library (Solving Consistency)

**The Innovation**: Every film **must** include an official dependency package before accepting contributions.

#### What's Included

**1. Character LoRA Models**  
Pre-trained Stable Diffusion/Video LoRAs for all main characters, ensuring consistent facial features across all submissions.

**2. Art Style References**  
- Color palette definitions
- Lighting guides (e.g., “noir high-contrast” vs. “bright natural”)
- Cinematography references (lens choices, camera angles)

**3. Prompt Templates**  
Standardized structures that contributors must follow:
```json
{
  "character_shot": "[PROTAGONIST_LORA] in [LOCATION], [ACTION], cinematic lighting, [STYLE_TOKENS]",
  "environment_shot": "[LOCATION_DESCRIPTION], [STYLE_TOKENS], establishing shot"
}
```

**4. ControlNet Specifications**  
3D skeletal rigs or pose control networks for character animation consistency.

**5. Voice Clone Models**  
Authorized TTS models for each character to maintain audio coherence.

#### Why This Matters

**Without Standard Library**: 10 creators generate 10 versions of the protagonist with different faces, hair, clothing → unusable.

**With Standard Library**: All 10 creators pull the same protagonist LoRA → perfect consistency, community votes on best execution.

**Analogy**: Like npm dependencies ensuring JavaScript libraries work together, Standard Library ensures video segments visually integrate.

---

## 3. Workflow & Roles

### 3.1 Film Initiator (Director/Producer)

**Responsibilities**:
1. Upload screenplay and decompose into Scenes/Shots
2. **Build Standard Library**:
   - Train character LoRAs using reference images/videos
   - Define art style and create prompt templates
   - Document technical requirements (resolution, frame rate, format)
3. Set up Treasury smart contract for revenue distribution
4. (Optional) Launch Film Ticket NFT crowdfunding campaign

**Rewards**: 10% of all film revenue (as Film NFT holder).

---

### 3.2 Contributors (AIGC Creators)

**Workflow**:

1. **Browse & Claim**  
   Browse available Shots in film repositories, claim one to work on.

2. **Pull Dependencies**  
   Download Standard Library (LoRAs, style guides, ControlNets, prompts).

3. **Generate Video**  
   Use AI tools (Sora, Runway, ComfyUI, etc.) with Standard Library assets:
   - Load character LoRAs into generation pipeline
   - Follow prompt templates
   - Apply ControlNet constraints if provided
   - Use official voice models for dialogue

4. **Submit Pull Request**  
   Upload video + metadata to segment's submission pool:
   - Video file hash (IPFS/Arweave)
   - Generation method (AI model, parameters)
   - Standard Library version used
   - Creator wallet address

5. **Minting**  
   Submission automatically minted as **Clip NFT** with:
   - Immutable authorship proof
   - Technical metadata
   - Pointer to video file

**Rewards**: 75% of film revenue distributed proportionally by screen time while in Main Branch.

---

### 3.3 Backers

**Role**: Stake USDC in Shot Competition Markets to back clips they believe will win.

**How They Earn**:
- **Competition payout**: Winning backers receive their full stake back plus a share of the Reward Pool (funded by 20% of losing stakes)
- **Ongoing streaming revenue**: 10% of film streaming revenue is allocated to backers of currently-merged clips, proportional to their stake

**Selection**: The clip with the highest quadratic-weighted backing wins — `BackingWeight = Σ √(deposit_i)` across all backers, plus `√(CreatorBond)` for the creator's own stake. Note: The clip creator's submission stake (min 50 USDC) is automatically counted as their initial backing deposit in the formula. This ensures broad community support outweighs concentrated capital.

**Risk**: Losing backers receive a 75% refund of their stake (lose 25%).

---

### 3.4 Audience

**Experience**:
- Stream films via platform frontend
- Videos dynamically assembled from current Main Branch segments
- Attribution displayed for all creators
- Optional: Purchase Film Ticket NFTs for lifetime access + fractional Film NFT revenue rights

---

## 4. Economic Model (Tokenomics)

### 4.1 NFT-Based Ownership

#### Clip NFT (Segment Ownership)
Every submission is minted as an ERC-721 NFT containing:
```json
{
  "clip_id": "0xabc...",
  "film_id": "space-odyssey-2077",
  "shot_id": "042",
  "creator": "0x123...",
  "timestamp": "2026-02-24T15:30:00Z",
  "video_hash": "QmXyZ...",
  "standard_library_version": "1.2.0",
  "generation_method": "Runway Gen-3 + character LoRA",
  "duration": 8.5,
  "creator_bond": 50,
  "status": "merged" | "archived"
}
```

**Properties**:
- Tradeable on NFT marketplaces
- Earns revenue only when `status: "merged"`
- Creator retains 10% perpetual royalty on secondary sales
- Upon minting, the clip enters the **Shot Competition Market** where community members back it with real capital (see Section 4.4)

#### Film NFT (Film Ownership)
The Film NFT (ERC-721) represents the film as a composite creative work. It is minted when a director pays a **Film Creation Bond** (minimum 500 USDC), a refundable deposit that signals commitment and seeds the film's treasury.

```json
{
  "film_nft": "0xdef...",
  "title": "Space Odyssey 2077",
  "director": "0x123...",
  "creation_bond": 10000,
  "bond_status": "partially_refunded",
  "standard_library_version": "1.2.0",
  "total_shots": 200,
  "merged_shots": 180,
  "segments": [
    {"shot_id": "001", "clip_nft": "0xabc...", "creator": "0x456...", "duration": 8.5},
    {"shot_id": "002", "clip_nft": "0xfgh...", "creator": "0x789...", "duration": 12.3}
  ],
  "total_runtime": 5400,
  "last_updated": "2026-06-15T14:30:00Z"
}
```

**What the Film NFT Represents**:
- **Copyright claim**: The holder asserts creative direction / copyright over the composite work
- **Revenue rights**: 10% of ongoing streaming revenue (the “Director Share”)
- **Governance authority**: Set film parameters (shot count, competition timing, quality thresholds, Standard Library versions)
- **Dynamic pointer array**: Contains references to all currently-merged Clip NFTs — updates when Shot Market competitions replace segments

**Film Creation Bond Allocation**:

| Portion | % | Purpose |
|---------|---|---------|
| Film Treasury Seed | 50% | Funds initial infrastructure (Standard Library hosting, CDN, IPFS pinning) |
| Completion Escrow | 50% | Locked, refundable to director upon film completion (≥80% shots merged) |

**Transferability**: The Film NFT is fully transferable. A director can sell their film (and its associated revenue stream) to another party. The new holder inherits all director rights and the 10% revenue share.

#### Backing Positions
Community members participate in Shot Markets by **backing** clips they believe are best with USDC. Backing positions are not separate NFTs but are recorded on-chain within the ShotMarket contract. Backers of the winning clip earn:
- **Competition payout**: A share of the reward pool from losing stakes (one-time)
- **Ongoing streaming revenue**: 10% of the film's treasury revenue is allocated to backers of currently-merged clips, proportional to their stake

Backing positions are tied to the merged clip — if a clip is replaced via a challenge, the original backers' streaming revenue stops and new backers begin earning.

---

### 4.2 Revenue Sources

1. **Pay-Per-View**: Audience streaming fees
2. **Advertising**: Platform ad revenue share
3. **Film Ticket NFTs**: Crowdfunding + lifetime access
4. **Copyright Licensing**: Studio/platform distribution deals
5. **Merchandise**: Official merch tied to NFT holders
6. **Shot Market Competition Fees**: 5% competition tax on losing stakes from all Shot Markets

All revenue flows into **Treasury Smart Contract** for automated distribution.

---

### 4.3 Revenue Distribution

**Smart Contract Formula**:

```
TreasuryRevenue = TotalRevenue - PlatformFee (2-5%)

FilmNFT_Share = TreasuryRevenue × 10%     // Director / Film NFT holder
StdLib_Share  = TreasuryRevenue × 5%      // Standard Library contributors
Backer_Pool   = TreasuryRevenue × 10%     // Backers of merged clips
Creator_Pool  = TreasuryRevenue × 75%     // Clip creators

// Creator earnings by screen time
Creator_Earnings = Creator_Pool × (Clip_Duration / Total_Film_Duration)

// Backer earnings proportional to their backing of merged clips
Backer_Earnings = Backer_Pool × (Clip_Duration / Total_Film_Duration) × (Backer_Deposit / Total_Winner_Backer_Deposits)
```

**Example** (90-minute film, $50,000/month revenue, 3% platform fee):

Treasury revenue = $50,000 × 0.97 = **$48,500/month**

| Stakeholder | Calculation | Monthly Earnings |
|-------------|-------------|------------------|
| Film NFT Holder (Director) | $48,500 × 10% | $4,850 |
| Standard Library Contributors | $48,500 × 5% | $2,425 |
| Backer Pool (all merged-clip backers) | $48,500 × 10% | $4,850 |
| Creator A (3 segments, 5 min total) | $36,375 × (5/90) | $2,021 |
| Creator B (1 segment, 30 sec) | $36,375 × (0.5/90) | $202 |

**Key Feature**: Earnings flow continuously while segment remains in Main Branch — passive income for both creators and backers.

---

### 4.4 Shot Competition Markets

Shot Markets are the central mechanism for selecting the best clip for each shot. Each shot in a film becomes a **bounded prediction market** where participants back the clip they believe is best with real capital.

#### Shot Market Lifecycle

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  SUBMISSION       │────▶│  BACKING          │────▶│  RESOLUTION      │
│  PHASE            │     │  PHASE            │     │                  │
│  (14 days)        │     │  (7 days)         │     │  Winner          │
│                   │     │                   │     │  determined by   │
│  Creators submit  │     │  Community backs  │     │  highest         │
│  clips + stake    │     │  clips with USDC  │     │  quadratic       │
│  Creator Bond     │     │  (blind commit-   │     │  backing weight  │
│  (50 USDC)        │     │   reveal)         │     │                  │
└──────────────────┘     └──────────────────┘     └──────────────────┘
```

**Submission Phase** (14 days, configurable by director):
1. Creator generates a video segment using the film's Standard Library
2. Creator submits the clip with a **Creator Bond** of 50 USDC
3. The clip is minted as a Clip NFT and enters the competition
4. Backing phase opens once ≥2 submissions exist

**Backing Phase — Blind Commit-Reveal** (7 days):
- **Commit Period (5 days)**: Backers evaluate clips, then commit `keccak256(clipId, amount, salt)` with their USDC deposit locked. Neither the chosen clip nor the amount is visible to others.
- **Reveal Period (2 days)**: Backers reveal their commitments. Non-reveals forfeit their deposit to the reward pool.
- Minimum backing: 10 USDC per backer per clip
- Maximum backing: 5,000 USDC per backer per clip
- A backer **cannot** back multiple clips in the same shot (forces conviction)

**Winner Determination — Quadratic Backing Weight**:

```
BackingWeight(clip_j) = Σ_i √(deposit_i)    for all backers i who backed clip j
                      + √(CreatorBond_j)      creator's own bond counts
```

Quadratic weighting ensures **breadth of support matters more than depth of capital**:

| Scenario | Raw Total | Quadratic Weight |
|----------|-----------|----------------|
| 1 whale backs Clip A with 10,000 USDC | 10,000 | √10,000 = **100** |
| 100 people back Clip B with 100 USDC each | 10,000 | 100 × √100 = **1,000** |

The clip with 100 backers has **10× the weight** of the single whale — crowd consensus wins.

#### Payout Structure

After resolution, payouts are calculated:

- **Competition Tax**: 5% of losing deposits → Film Treasury
- **Loser Refund**: 75% of losing deposits → returned to losers
- **Reward Pool**: 20% of losing deposits → split between winning creator (50%) and winning backers (50%)
- **Winner deposits**: Returned in full

**Example** — Shot 042 with 3 clips, Clip A wins:

| Clip | Creator Bond | Backers | Total Deposited | Quadratic Weight |
|------|-------------|---------|-----------------|----------------|
| **Clip A (Winner)** | 50 USDC | 30 backers, avg 40 USDC | 1,250 USDC | **196.81** |
| Clip B (Loser) | 50 USDC | 15 backers, avg 30 USDC | 500 USDC | 89.23 |
| Clip C (Loser) | 50 USDC | 8 backers, avg 25 USDC | 250 USDC | 47.07 |

Losing deposits: 750 USDC → Tax: 37.50 | Refund: 562.50 | Reward Pool: 150 USDC
- **Winning Creator A**: 50 (bond back) + 75 (reward) = **125 USDC** (150% ROI)
- **Each Backer of A** (avg 40 USDC): 40 + ~2.50 = **42.50 USDC** (6.25% ROI)
- **Losing creators/backers**: 75% of their deposits refunded (25% loss)

#### Film Creation Bond

To create a film, the director pays a **Film Creation Bond** (minimum 500 USDC, recommended 50 USDC × number of shots). The bond is split: 50% seeds the Film Treasury (infrastructure costs) and 50% is locked in Completion Escrow, refundable when ≥80% of declared shots have a merged clip.

If no submissions or director activity occur for 90 consecutive days, the film is marked “Abandoned” — escrowed funds are distributed pro-rata to creators who submitted clips.

---

### 4.5 Iterative Competition & Replacement Challenges

Once a clip is merged, it can be replaced if a genuinely superior version appears. The replacement process uses the same Shot Market mechanism but with deliberate friction to protect incumbent creators.

**Challenge Process**:

1. **Challenger submits** a new clip for an already-merged shot
2. **Challenge Bond**: 2× Creator Bond (100 USDC) — the higher barrier discourages frivolous challenges
3. **Incumbent creator** is notified and has 7 days to respond with an improved version (at no additional cost)
4. **Mini-Competition**: A compressed Shot Market opens (7-day submission + 5-day backing)
5. **Incumbency Advantage**: The incumbent clip starts with a synthetic backing weight equal to 50% of its original winning weight
6. **Resolution**: Same quadratic backing weight formula

**Challenge Outcomes**:
- **Challenger wins**: Incumbent clip archived (revenue stops), challenger merged (revenue starts), Challenge Bond refunded + reward pool share
- **Incumbent survives**: Challenger receives 75% refund of Challenge Bond (loses 25 USDC), incumbent receives 50% of the forfeited portion as a defense bonus

**Challenge Cooldown**: After any challenge, the same shot cannot be challenged again for **21 days**.

**Economic Effect**:

- Continuous quality improvement (race to use latest AI models)
- Meritocracy (earnings tied to on-screen presence, not tenure)
- Film evolution (movies improve as technology advances)
- Long-tail monetization (even older segments earn if they remain best)
- Fair protection for incumbents via higher Challenge Bond and incumbency advantage

---

## 5. Governance & Anti-Gaming

### 5.1 Shot Market Mechanism

OpenMontage replaces token-weighted voting with **Shot Markets** — prediction market-style competitions where community members stake real capital to back the clips they believe are best. This transforms passive opinion polling into active quality investment.

**Commit-Reveal Protocol**:
1. **Commit Phase** (5 days): Backers submit `keccak256(clipId, amount, salt)` with USDC locked. Neither the chosen clip nor the backing amount is visible on-chain — only the commitment hash.
2. **Reveal Phase** (2 days): Backers reveal their commitments. The contract verifies the hash matches. Non-reveals forfeit their deposit.
3. **Resolution**: Quadratic backing weights are computed and the winning clip is determined.

**Quadratic Weighting**:
- `BackingWeight(clip) = Σ √(deposit_i)` for all backers of that clip
- Ensures **breadth of support** (many backers) outweighs **depth of capital** (one whale)
- 100 backers at 100 USDC each produce 10× the weight of one backer at 10,000 USDC

**Winner Determination**:
- Clip with the highest quadratic backing weight wins the shot
- Winner is merged into the Main Branch and begins earning streaming revenue
- Tie-breaking: Earlier submission wins (first-mover advantage)

### 5.2 Anti-Gaming Measures

**Commit-Reveal Prevents Bandwagoning and Sniping**:
- Backings are hidden during the commit phase — backers cannot see which clip is “winning”
- No last-minute capital dumps based on observed totals
- Each backer must independently evaluate clip quality
- The reveal phase produces genuine independent quality assessments

**Quadratic Backing Prevents Whale Dominance**:
- A single wealthy actor cannot guarantee a win by depositing a massive amount
- `√(10,000)` = 100 weight, while `100 × √(100)` = 1,000 weight — the crowd wins 10:1
- This makes the system “broadest consensus wins” rather than “highest bidder wins”

**Minimum Stake Thresholds**:
- Minimum 10 USDC per backing prevents near-zero-cost Sybil attacks
- Creator Bond of 50 USDC per submission prevents spam submissions
- Challenge Bond of 100 USDC (2× Creator Bond) discourages frivolous challenges
- Wallet age requirement (7 days minimum) adds friction for newly created Sybil wallets

**Cooldown Periods for Replacement Challenges**:
- After any challenge (successful or not), the same shot cannot be challenged again for 21 days
- Prevents harassment of incumbent creators through repeated challenges
- Incumbent clips start with a synthetic 50% backing weight advantage in challenges

**Quality Floors**:
- Smart contract validates Standard Library compliance before clip submission
- Technical spec enforcement (resolution, format, duration)
- AI-powered consistency scoring (face recognition, style matching)

---

## 6. Technical Implementation

### 6.1 System Architecture

```
┌─────────────────────────────────────────────────────┐
│              Frontend (Web/Mobile)                  │
│  - Film browsing & streaming                        │
│  - Submission management                            │
│  - Shot Market interface                             │
│  - Wallet integration                               │
└────────────────┬────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────┐
│            Application Layer (API)                  │
│  - User authentication                              │
│  - Video assembly engine                            │
│  - Metadata indexing                                │
│  - Search & discovery                               │
└────────┬─────────────────────┬──────────────────────┘
         │                       │
┌────────▼────────┐     ┌──────▼────────────────────────┐
│  Blockchain     │     │  Storage Layer             │
│  - Smart        │     │  - IPFS/Arweave (videos)   │
│    contracts    │     │  - Cloud CDN (delivery)    │
│  - NFT minting  │     │  - Git repos (metadata)    │
│  - Shot Market  │     │  - Standard Library CDN    │
│  - Treasury     │     └────────────────────────────┘
└─────────────────┘
```

> **Note**: Phase 1 uses Discord as the primary frontend (see Section 6.5). The web/mobile frontend replaces Discord in Phase 2-3.

### 6.2 Smart Contracts

#### Target Chain: Base (Coinbase L2)

OpenMontage will deploy on **Base**, Coinbase's Ethereum L2 rollup, as the primary chain. Rationale:

- **Low fees**: Sub-cent transaction costs make frequent minting and backing economically viable for creators
- **Creator ecosystem**: Base has cultivated the largest on-chain creator community (Zora, Friend.tech), aligning with OpenMontage's target users
- **Coinbase onramp**: Seamless fiat-to-crypto for non-crypto-native filmmakers and audiences
- **Ethereum security**: Inherits Ethereum's security guarantees as an OP Stack rollup
- **EVM compatibility**: Standard Solidity tooling (Hardhat, Foundry, OpenZeppelin)

**Fallback**: Arbitrum One serves as the secondary deployment target if Base throughput or ecosystem conditions change. Base uses OP Stack and Arbitrum uses Nitro, but both are EVM-compatible rollups, so contract migration requires minimal changes.

#### Core Contracts

1. **ClipNFT.sol** — ERC-721 for segment ownership, includes backer registry
2. **FilmNFT.sol** — Film copyright + revenue rights + Film Creation Bond management
3. **Treasury.sol** — Automated revenue distribution (75/10/5/10 split), Competition Tax collection
4. **ShotMarket.sol** — Shot Market: submission, backing, commit-reveal, resolution, payouts
5. **StandardLibrary.sol** — Dependency versioning & compliance checks

#### Contract Interfaces

```solidity
// ─── ClipNFT.sol ─────────────────────────────────────────────────────────
function submitClip(uint filmId, uint shotId, string videoHash) external returns (uint clipId);
function mergeClip(uint shotId, uint clipId) external;
function archiveClip(uint clipId) external;

event ClipSubmitted(uint indexed filmId, uint indexed shotId, uint clipId, address creator);
event ClipMerged(uint indexed shotId, uint clipId, uint previousClipId);
event ClipArchived(uint indexed clipId);

modifier onlyStandardLibraryCompliant(uint filmId, bytes32 contentHash);

// ─── ShotMarket.sol ────────────────────────────────────────────────────────────────
function openMarket(uint filmId, uint shotId, uint duration) external;
function commitBacking(uint shotId, uint clipId, bytes32 commitment) external payable;
function revealBacking(uint shotId, uint clipId, uint amount, bytes32 salt) external;
function resolveMarket(uint shotId) external returns (uint winningClipId);
function claimWinnerReward(uint shotId, address backer) external returns (uint amount);
function claimLoserRefund(uint shotId, uint clipId, address backer) external returns (uint amount);

event MarketOpened(uint indexed filmId, uint indexed shotId, uint commitDeadline, uint revealDeadline);
event BackingCommitted(uint indexed shotId, uint indexed clipId, address indexed backer, bytes32 commitment);
event BackingRevealed(uint indexed shotId, uint indexed clipId, address indexed backer, uint amount);
event MarketResolved(uint indexed shotId, uint winningClipId, uint totalStaked, uint competitionTax);

// ─── FilmNFT.sol ────────────────────────────────────────────────────────────────────
function createFilm(string title, uint bondAmount) external returns (uint filmId);
function getBondStatus(uint filmId) external view returns (uint bonded, uint refundable, bool completed);
function claimBondRefund(uint filmId) external returns (uint refunded);

event FilmCreated(uint indexed filmId, address director, uint bond, uint totalShots);
event FilmCompleted(uint indexed filmId, uint mergedShots);
event FilmAbandoned(uint indexed filmId, uint escrowDistributed);

// ─── Treasury.sol ────────────────────────────────────────────────────────
// Revenue split: 75% creators, 10% Film NFT holder, 5% stdlib, 10% backers
function distributeRevenue(uint filmId) external;
function claimRevenue(uint filmId) external returns (uint amount);
function depositCompetitionTax(uint filmId, uint amount) external;
function getClaimable(uint filmId, address participant) external view returns (uint creatorRevenue, uint backerRevenue, uint directorRevenue);
```

### 6.3 Standard Library Distribution

**Infrastructure**:
- Cloud CDN (Cloudflare, AWS CloudFront) for global distribution
- Git LFS for large LoRA/ControlNet files
- Versioning system (SemVer: v1.2.3)
- Dependency manifest:
```json
{
  "standard_library_version": "1.2.0",
  "characters": [
    {"name": "protagonist", "lora_hash": "QmXyZ...", "size_mb": 142}
  ],
  "style_guide_hash": "QmAbc...",
  "prompt_templates": "..."
}
```

**Validation**:
- Automated checks verify submissions use correct versions
- AI-powered similarity scoring (face recognition, style matching)
- Reject submissions with <70% consistency score

### 6.4 Real-Time Assembly Engine

**Playback Process**:
1. Frontend fetches `film.json` (segment order + Clip NFT pointers)
2. For each shot, query ShotMarket.sol for current Main Branch Clip NFT
3. Fetch video file from IPFS/CDN via video hash
4. Stream segments sequentially with fade transitions
5. Display creator attribution overlay

**Optimization**:
- Pre-cache popular films
- Adaptive bitrate streaming (HLS/DASH)
- Regional CDN edge caching

### 6.5 Discord-First Platform Strategy

Rather than building a full web application from day one, OpenMontage launches **Discord-first** — using Discord as the primary user interface for Phase 1, then progressively migrating to a standalone web app.

#### Why Discord First

Discord is where creator and crypto-native communities already live. By meeting users where they are, OpenMontage eliminates onboarding friction, leverages Discord's built-in identity/roles/moderation, and can ship a usable product in weeks rather than months. A custom web frontend is expensive and risky before product-market fit is validated.

#### Architecture

```
┌──────────────┐     ┌──────────────────┐     ┌─────────────────┐     ┌──────────────┐
│   Discord    │────▶│  Bot Backend     │────▶│  Web3 Provider  │────▶│   Smart      │
│   (User UI)  │◄────│  (Node.js API)   │◄────│  (ethers.js)    │◄────│   Contracts  │
└──────────────┘     └──────────────────┘     └─────────────────┘     │   (Base L2)  │
                              │                                        └──────────────┘
                              ▼
                     ┌──────────────────┐
                     │  IPFS / Arweave  │
                     │  (Video Storage) │
                     └──────────────────┘
```

The Discord bot translates user actions into blockchain transactions:
- `/submit <shot-id>` → uploads video to IPFS, calls `ClipNFT.submitClip()`
- `/back-clip <shot-id> <clip-id> <amount>` — commits backing to IPFS hash, calls `ShotMarket.commitBacking()`
- `/claim` → calls `Treasury.claimRevenue()` for the user's merged clips
- `/library <film-id>` → fetches Standard Library download links from CDN
- `/status <film-id>` → displays film progress, open shots, active markets

Users link their wallet once via `/connect-wallet` and all subsequent commands are signed through a custodial relay or WalletConnect session.

#### Transition Path

| Phase | Interface | Notes |
|-------|-----------|-------|
| **Phase 1** (Q2-Q3 2026) | Discord-only | Bot handles all workflows; testnet deployment |
| **Phase 2** (Q4 2026) | Discord + Web Dashboard | Read-only dashboard for browsing films, viewing stats; Discord remains primary for actions |
| **Phase 3** (2027+) | Full Web App + Discord notifications | Standalone web app for all workflows; Discord bot transitions to notification/alert role |

This incremental approach validates the core product loop (submit → back → earn) cheaply before investing in custom frontend infrastructure.

### 6.6 Security Considerations

#### Smart Contract Auditing

All core contracts (ClipNFT, FilmNFT, Treasury, ShotMarket, StandardLibrary) will undergo two independent audits before mainnet deployment:

1. **OpenZeppelin** — industry-standard audit for ERC-721 compliance, access control, and upgrade patterns
2. **Trail of Bits** — adversarial review focused on economic exploits, MEV risks, and governance attacks

Audit reports will be published publicly. Critical findings must be resolved before deployment; medium-severity findings tracked in a public issue tracker with remediation timeline.

#### Reentrancy Protection

Treasury.sol is the highest-risk contract due to direct ETH/ERC-20 transfers. All payout functions implement the **checks-effects-interactions** pattern:

1. Validate caller eligibility and compute amounts (checks)
2. Update internal accounting and mark claims as processed (effects)
3. Transfer funds last (interactions)

Additionally, all external-facing functions in Treasury.sol use OpenZeppelin's `ReentrancyGuard` as defense-in-depth.

#### Oracle and Price Feed Risks

Revenue attribution for pay-per-view and advertising requires off-chain data. The platform uses **Chainlink Data Feeds** for ETH/USD price conversion and a custom Chainlink Functions integration for revenue event reporting. Oracle failure is mitigated by a 24-hour staleness check — if price data is stale, revenue distribution pauses until fresh data arrives.

#### Upgrade Strategy

Phase 1 contracts deploy behind **OpenZeppelin TransparentProxy** to allow bug fixes during early operation. Proxy admin is the team multi-sig (see below). After the protocol stabilizes (Phase 3), governance can vote to either:
- Lock proxies permanently (immutable)
- Migrate to new immutable contracts with a 30-day migration window

#### Emergency Pause

All core contracts inherit OpenZeppelin's `Pausable`. The team multi-sig can pause any contract in response to exploits or critical bugs. Pause authority transfers to the DAO Governor contract in Phase 3.

#### Key Management

Phase 1 operations are controlled by a **3-of-5 Gnosis Safe multi-sig** composed of founding team members. Signers are geographically distributed and use hardware wallets. The multi-sig controls: contract upgrades, emergency pause, treasury parameter changes, and initial governance settings.

---

## 7. Roadmap

### Phase 1: Foundation (Q2-Q3 2026)
- ✅ Whitepaper & documentation
- ⏳ Core smart contracts (testnet)
- ⏳ Discord bot (MontageBot v0.1)
- ⏳ Standard Library spec v1.0
- ⏳ Pilot film project (community test)

### Phase 2: Alpha Launch (Q4 2026)
- Mainnet deployment on Base (Coinbase L2)
- Public film repository platform
- Shot Market competition tools
- Film Ticket NFT crowdfunding

### Phase 3: Ecosystem Growth (2027)
- $MONTAGE governance token launch
- Advanced Standard Library tools (auto LoRA training)
- Cross-film asset marketplace
- AI-powered consistency validation
- DeFi integration (Clip NFT collateralized loans)
- Mobile apps (iOS/Android)

### Phase 4: Mass Adoption (2028+)
- Partnerships with film festivals
- Studio licensing deals
- Educational institution integrations
- Localization (50+ languages)
- VR/AR immersive viewing experiences

---

## 8. Use Cases

### 8.1 Indie AI Films
Global creators collaborate on original screenplays, earning revenue from successful projects.

### 8.2 Fan Films
Community-driven adaptations of public domain works (Shakespeare, mythology) with guaranteed visual consistency.

### 8.3 Educational Content
Universities/edtech platforms crowdsource science explainer videos segment-by-segment.

### 8.4 Music Videos
Musicians crowdfund visuals via Film Ticket NFTs, contributors compete for 75% revenue share.

### 8.5 Corporate Videos
Brands outsource production to global talent marketplace, paying only for final merged segments.

---

## 9. Competitive Analysis

> For a comprehensive competitive landscape analysis, see [Competitive Analysis](competitive-analysis.md).

| Platform | Model | Consistency Solution | Revenue Model | Iteration |
|----------|-------|---------------------|---------------|-----------|
| **OpenMontage** | Decentralized Git | Mandatory Standard Library | 75% streaming revenue | Segments replaceable |
| Traditional Studios | Centralized | Single production designer | Upfront fees | Fixed after release |
| YouTube Collaborations | Ad-hoc | Manual style guides (often ignored) | Ad revenue (platform takes 45%) | No formal replacement |
| Decentralized Social (Lens, Farcaster) | Creator-owned | None | Tips/subscriptions | No quality curation |

**Key Differentiators**:
1. **Only platform with mandatory dependency system** for AI-generated content
2. **Only streaming revenue model** proportional to screen time
3. **Only version-controlled films** that improve over time

---

## 10. Risk Mitigation

### 10.1 Technical Risks

**Storage Costs**  
- Mitigation: IPFS/Arweave for permanent storage, CDN for delivery
- Contributors cover initial upload costs (offset by revenue potential)

**Blockchain Fees**
- Mitigation: Deploy on Base (Coinbase L2) for sub-cent transaction costs, with Arbitrum as fallback

### 10.2 Legal Risks

**Copyright Infringement**  
- Mitigation: DMCA takedown process, contributor identity verification
- Smart contracts include copyright attestation requirement

**Revenue Distribution Disputes**  
- Mitigation: All payments on-chain and auditable, transparent Shot Market records

### 10.3 Market Risks

**Low Adoption**  
- Mitigation: Partner with film schools, AI tool providers (Runway, Pika)
- Incentivize early adopters with bonus token distributions

**Poor Quality Content**  
- Mitigation: Quality floors via automated checks, economic skin-in-the-game (quadratic backing)

---

## 11. Conclusion

OpenMontage transforms filmmaking from a centralized, capital-intensive industry into a **decentralized, meritocratic marketplace**. By solving the three fundamental barriers — consistency, incentives, and quality — through Standard Library dependencies, NFT-based economics, and version control, we enable:

1. **Global collaboration** on professional-quality films
2. **Fair compensation** for creators based on merit, not connections
3. **Continuous improvement** as technology and talent evolve
4. **Transparent economics** via blockchain-based revenue distribution

**This is not just open-source filmmaking — it's the infrastructure for the next generation of cinema in the age of AI.**

---

**Join us in building the future of collaborative storytelling.**

- **Website**: [openmontage.com](https://openmontage.com)
- **GitHub**: [github.com/digimads-lab/OpenMontage](https://github.com/digimads-lab/OpenMontage)
- **Discord**: [discord.gg/openmontage](https://discord.gg/openmontage)
- **Twitter**: [@OpenMontage](https://twitter.com/openmontage)

---

**Version**: 1.2
**Last Updated**: 2026-03-04
**Authors**: OpenMontage Core Team
