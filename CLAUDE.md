# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a **documentation-only repository** at the concept/whitepaper stage. There is no code, no build system, and no tests. All content is Markdown documentation describing a platform design.

## Structure

```
README.md                 # Project summary (Git for Movies concept)
CONTRIBUTING.md           # Contributor onboarding guide (creators, backers, directors, devs)
doc/
  whitepaper.md           # Full technical architecture and vision (v1.2)
  product-mechanism.md    # Workflows, Standard Library, Shot Market mechanics (v1.2)
  tokenomics.md           # NFT ownership, revenue distribution, $MONTAGE token (v2.0)
  governance.md           # Shot Market governance and platform governance (v1.1)
  discord-platform.md     # Discord server architecture and bot commands (v1.1)
  roadmap.md              # Development timeline and milestones (v1.0)
  competitive-analysis.md # Market landscape and competitive positioning (v1.1)
```

## Project Concept

OpenMontage is a proposed decentralized platform for crowdsourced AI filmmaking, applying Git/open-source concepts to film production:

- **Film Repository**: Scripts decomposed into discrete Shots (like functions in code)
- **Standard Library**: Mandatory dependency package per film — character LoRA models, style guides, prompt templates, ControlNets, voice clone models — solving the visual consistency problem in crowdsourced AI content
- **Pull Requests**: Contributors submit AI-generated video segments for individual Shots
- **Shot Competition Markets**: Economic backing (USDC stake) selects the best segment per Shot — highest quadratic-weighted backing wins. Replaces token-weighted voting.
- **Revenue split**: 75% creators (by screen time) / 10% Film NFT holder (director) / 5% Standard Library contributors / 10% backers (merged-clip backing) — distributed via smart contracts

## Economic Model (Shot Markets)

The core innovation is the **Shot Competition Market**:

1. Creator submits clip → pays 50 USDC submission stake → Clip NFT minted
2. **Commit phase (5 days)**: Backers submit hidden commitments (hash of clip + amount + salt)
3. **Reveal phase (2 days)**: Backers reveal actual amounts; contract verifies
4. **Resolution**: Clip with highest `BackingWeight = Σ √(deposit_i)` wins (quadratic weighting prevents whale dominance)
5. **Payouts**:
   - Winners: full stake back + 50% of Reward Pool (creator) / 50% of Reward Pool (backers, proportional)
   - Losers: 75% refund (25% funds Competition Tax 5% + Reward Pool 20%)
6. **Film Creation Bond**: Director deposits min 500 USDC → receives **Film NFT** (copyright + 10% streaming revenue). 50% of bond refunded when ≥80% shots merged.

## Architecture (from whitepaper v1.2)

The planned technical stack:
- **Blockchain layer**: ERC-721 Clip NFTs (per segment) and Film NFTs (composite film + copyright); smart contracts: `ClipNFT.sol`, `FilmNFT.sol`, `Treasury.sol`, `ShotMarket.sol`, `StandardLibrary.sol`
- **Storage**: IPFS/Arweave for video files, Git LFS for Standard Library assets (LoRAs, ControlNets), Cloud CDN for delivery
- **Application layer**: REST API for authentication, video assembly, metadata indexing
- **Governance token**: `$MONTAGE` — staked for **platform governance** voting and Competition Tax fee discounts (5%→2%). NOT used for film-level clip selection (that's Shot Markets).
- **Primary chain**: Base (Coinbase L2), Arbitrum One as fallback

The film manifest (`film.json`) is a Film NFT containing pointers to currently-merged Clip NFTs, updating dynamically as Shot Markets resolve.

## Key Discord Commands (v1.1)

- `/create-film` — director launches film with Film Creation Bond
- `/claim-shot film:[id] shot:[id]` — creator claims a shot (72h lock)
- `/submit film:[id] shot:[id] url:[url]` — submit clip, pay 50 USDC bond, mint Clip NFT
- `/back-clip film:[id] shot:[id] clip:[id] amount:[USDC] salt:[secret]` — commit backing
- `/reveal-backing film:[id] shot:[id] clip:[id] salt:[secret]` — reveal during reveal phase
- `/claim-market film:[id] shot:[id]` — claim winner reward or loser refund
- `/standard-library film:[id]` — download Standard Library assets
- `/my-earnings` — view streaming revenue + competition rewards

## Roadmap Status

As of whitepaper v1.2 (2026-03-04):
- Phase 1 (Q2–Q3 2026): Discord-first platform, testnet smart contracts, pilot film "Genesis" (100 shots, sci-fi)
- Phase 2 (Q4 2026): Base mainnet, web dashboard v1, Film Ticket NFT crowdfunding, public launch
- Phase 3 (2027): $MONTAGE token IDO (TGE Q1 2027), full DAO governance, creator marketplace, mobile apps
- Phase 4 (2028+): Festival partnerships, studio licensing, VR/AR

## Working in This Repo

Since there is no code, contributions here are documentation changes. The **whitepaper is the authoritative source**; all other docs must not contradict it. The **design spec** (created during the Shot Market redesign) lives at `/tmp/openmontage_economic_design.md` but is not checked in — use tokenomics.md v2.0 as the authoritative economic reference.

Key consistency rules:
- Revenue split is always **75/10/5/10** (creators/Film NFT/stdlib/backers)
- Film Ticket NFTs take 10% from the **creator pool** (75%→65%), not from the director's 10%
- Shot Markets (not token voting) determine clip winners at the film level
- $MONTAGE governs the **platform**, not individual film decisions
