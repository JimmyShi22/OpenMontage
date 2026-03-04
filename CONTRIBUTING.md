# Contributing to OpenMontage

OpenMontage is building the infrastructure for collaborative AI filmmaking — think GitHub for movies. Whether you make AI videos, direct films, curate quality, or write code, there's a place for you here.

This guide walks you through everything you need to start contributing.

---

## Ways to Contribute

- **AI Video Creators** — Generate video segments for active film projects and earn revenue from your work
- **Directors / Producers** — Launch film projects with your vision and build the Standard Libraries that make collaboration possible
- **Backers / Curators** — Back clips you believe will win in Shot Markets and earn from your correct picks. Top backers of winning clips earn from the competition reward pool AND ongoing streaming revenue (10% of film revenue distributed to merged-clip backers).
- **Developers** — Help build the platform: Discord bot, smart contracts, web dashboard
- **Community Builders** — Grow and moderate the Discord community

---

## Getting Started

### Step 1: Join the Discord

All OpenMontage activity happens on Discord during Phase 1.

- Join at [discord.gg/openmontage](https://discord.gg/openmontage)
- Introduce yourself in `#introductions` — tell us what you do and what interests you
- Pick your role(s) in `#role-select`: Creator, Director, Backer / Curator, Developer, or Community

### Step 2: Verify Your Wallet

- Use the Collab.Land bot in the `#verify-wallet` channel
- Connect your Ethereum or Base wallet
- Once verified, you'll receive the **Verified** role, which unlocks submission and Shot Market channels

### Step 3: Get Test Tokens

- During Phase 1 (testnet), request test **USDC** from `#faucet` to participate in Shot Market backing competitions, and test `$MONTAGE` from `#faucet` for platform governance voting. No real money required — everything runs on testnet until mainnet launch.

### Step 4: Explore Active Films

- Browse `#film-listings` to see all active film projects
- Each listing includes: logline, genre, number of open shots, and Standard Library quality preview
- Download a Standard Library for any film you want to contribute to
- Ask questions in the film's `#director-chat` channel before starting work

---

## Creator Guide (Video Segment Contributor)

### Tools You Need

**AI Video Generation** (any of these):
- Runway Gen-3 or later
- Pika 1.5 or later
- Kling
- Sora
- ComfyUI with video generation workflows

**Image Generation** (for storyboarding and reference):
- Midjourney
- SDXL / Flux
- Any tool that supports LoRA loading

**Basic Video Editing**:
- DaVinci Resolve (free)
- CapCut
- Any tool that can export MP4/H.264

### Understanding the Standard Library

The Standard Library is the dependency package for each film. It's what keeps characters, lighting, and style consistent across hundreds of creators. Before creating anything:

1. Download it from Discord using `/standard-library film:[film-id]`
2. Load character LoRA files into your generation pipeline
3. Read the style guide carefully — pay attention to color palette, lighting direction, and camera language
4. Study the prompt templates and understand the placeholder structure
5. **Test your setup** with a simple generation before starting the real shot — verify the character LoRA produces recognizable results in your pipeline

### Claiming a Shot

1. Browse `#shots-available` in the film's channel category
2. Use `/claim-shot film:[id] shot:[id]` to reserve a shot
3. You have **72 hours** to submit (or release the claim with `/release-shot`). If the lock expires without a submission, the shot is automatically released back to `#shots-available`. You'll receive a DM reminder 12 hours before expiration.
4. The bot DMs you: shot description, technical requirements, duration target, and Standard Library download links

### Creating Your Segment

**Technical Requirements:**
- **Format**: MP4, H.264 codec
- **Resolution**: Minimum 1080p (1920x1080), 4K preferred
- **Duration**: Must match the shot specification within ±0.5 seconds
- **Frame rate**: 24fps for cinematic look, 30fps acceptable
- **Audio**: Include if the shot requires dialogue or sound effects; use the provided voice models for character speech

**Quality Guidelines:**
- Follow the prompt templates from the Standard Library — they exist for a reason
- **Character LoRA adherence is the most important factor**. Backers heavily weight visual consistency. If your protagonist doesn't look like the protagonist, the submission will lose regardless of how good the cinematography is.
- Lighting should match the film's style guide. Check reference images.
- Smooth motion: avoid AI artifacts like melting faces, warping limbs, or flickering textures
- Composition: follow any storyboard references in the shot description. Match camera angles and framing.

### Submitting Your Work

1. Upload your video to a publicly accessible URL (IPFS preferred, cloud storage acceptable)
2. Use `/submit film:[id] shot:[id] url:[video-url] notes:[brief description of your approach]`
3. The bot automatically validates format, resolution, duration, and Standard Library version
4. **If approved**: your Clip NFT is minted on-chain and your segment enters the Shot Competition Market
5. **If rejected**: the bot DMs you the specific validation error with instructions to fix and resubmit

### The Shot Competition Market

After you submit a clip, it enters the Shot Competition Market — a 7-day economic competition to determine which clip wins the shot.

**Phase 1: Commit Phase (Days 1–5)**
- Your submission stake is automatically entered as your initial backing
- Community members can back your clip (or others) by submitting hidden commitments — they lock USDC without revealing the amount
- You don't know who is backing which clip during this phase (prevents bandwagoning)
- You can see *how many* commitments each clip has received, but not the amounts

**Phase 2: Reveal Phase (Days 6–7)**
- Backers reveal their actual backing amounts by submitting their salt
- The contract verifies each reveal against the commitment hash
- Any backer who committed but fails to reveal loses their stake (counted as forfeit)

**Resolution**
- Winner = clip with highest **quadratic-weighted backing**: `Σ √(deposit_i)` for each backer i
- Quadratic weighting means 100 people backing at $100 each outweighs 1 whale backing at $1,000,000 — quality beats capital
- Results posted publicly in Discord channel

**If your clip wins:**
- Your Clip NFT status changes to **“merged”** → begins earning streaming revenue immediately
- You receive your submission stake back in full
- You receive your share of the **Reward Pool** (α = 50% of the 20% reward allocation from losing stakes goes to the winning creator)
- Example: 3 competing clips, $1,000 total losing stakes → Reward Pool = $200 → your creator share = $100

**If your clip loses:**
- You receive **75% of your submission stake** back (you lose 25%)
- Your Clip NFT remains **“archived”** — you keep it, and can resubmit an improved version
- Example: $50 submission stake → you get $37.50 back

### Tracking Your Earnings

- Use `/my-earnings` in Discord to see revenue across all your merged segments
- Your earnings come from two sources: (1) streaming revenue (75% of film revenue distributed by screen time), and (2) Shot Market competition rewards (for winning clips). Use `/my-earnings` to see both.
- Revenue flows in real-time as films are streamed by audiences
- Claim your earnings weekly via the Treasury smart contract using `/claim-earnings`

---

## Backer Guide

Backers (previously called Curators or Voters) now participate by staking real capital on clips they believe will win. This “skin-in-the-game” model produces better quality signals than free token votes.

### How Shot Market Backing Works

1. Browse the active Shot Market for any open shot using `/shot-market film:[id] shot:[id]`
2. During the **Commit Phase** (days 1–5), submit a backing commitment:
   ```
   /back-clip film:[id] shot:[id] clip:[clip-id] amount:[USDC] salt:[your-secret]
   ```
   - Your USDC is locked; the amount is hidden from others
   - Keep your salt — you need it to reveal
3. During the **Reveal Phase** (days 6–7), reveal your backing:
   ```
   /reveal-backing film:[id] shot:[id] clip:[clip-id] salt:[your-secret]
   ```
   - Must reveal or lose your stake
4. After resolution, claim your winnings or refund:
   ```
   /claim-market film:[id] shot:[id]
   ```

### How to Evaluate Clips

Before committing, evaluate each clip on:

1. **Standard Library Adherence** (most important): Does the character look right? Are the prompt templates followed? This is the #1 predictor of community consensus.
2. **Technical Quality**: Smooth motion, accurate lighting, no artifacts
3. **Creative Execution**: How well does it interpret the shot description?

Download the Standard Library and literally compare the clip's characters against the LoRA reference: `/standard-library film:[id]`

### Backer Rewards

If you backed the winning clip:
- Get your full backing stake returned
- Earn proportional share of the **Reward Pool** (β = 50% of the 20% reward allocation from losing stakes, split among all winning backers proportional to their stake size)
- Earn ongoing **streaming revenue** (10% of all film streaming revenue, split among backers of all merged clips proportional to their backing share)
- Example: You backed $500 on a winning clip, total winning backing = $5,000 → you get 10% of the backer streaming revenue pool

If you backed a losing clip:
- Get **75% of your stake** back (you lose 25%)
- No streaming revenue from that clip

### Early Detection Bonus

The backing weight formula doesn't give time-weighted bonuses — but being early matters in a subtler way: if few others have committed yet, your backing has higher marginal impact on the quadratic weight. Smart backers research early and commit confidently before the crowd.

### Backer Reputation

Future versions will introduce an on-chain reputation score tracking your historical backing accuracy rate. High reputation backers may receive fee discounts on platform features.

---

## Director Guide

### Is OpenMontage Right for Your Film?

**Best candidates:**
- Short-to-medium films (20–120 minutes of final runtime)
- Clear visual style that can be encoded in a Standard Library
- Scripts with discrete, visually-describable shots
- Stories where creative variation between shots is acceptable or even desirable

**Less suitable:**
- Films requiring extremely tight frame-level continuity across long takes
- Documentary style (talking heads, real-world locations)
- Projects where the director needs granular control over every detail of every frame

### Creating Your Film Project

1. Write your script with AI generation in mind — discrete shots, vivid visual descriptions
2. Build your Standard Library: character LoRAs, style guide, prompt templates, voice models
3. Use `/create-film` in Discord to launch the setup wizard
4. **Configure Film Bond & Treasury**
   - Deposit minimum **500 USDC** Film Creation Bond when using `/create-film`
   - This bond is held in the FilmNFT.sol contract:
     - 50% refunded when film reaches **≥80% shots merged** (film completion milestone)
     - 50% retained in Film Treasury to fund competition operations
   - You receive the **Film NFT** — represents copyright and **10% of all streaming/licensing revenue**
   - Optionally issue **Film Ticket NFTs** to crowdfund production: Ticket holders receive lifetime streaming access and a 10% revenue share taken from the creator pool (reducing it from 75% to 65%). The Film NFT holder's 10% share, Standard Library's 5%, and Backers' 10% remain unchanged.
5. Announce your project in `#film-listings` with a compelling pitch and sample generations

### Standard Library Requirements

**Mandatory:**
- Character LoRA models for all speaking and recurring characters
- Style guide document with color palette, lighting references, and camera language
- Prompt template dictionary with clear placeholder structure

**Optional but recommended:**
- ControlNet specifications for complex action shots or precise character poses
- Voice clone models for dialogue-heavy films
- Location and environment reference packs

For detailed Standard Library guidelines, see [Product Mechanism: Standard Library Best Practices](doc/product-mechanism.md#11-standard-library-best-practices).

### Your Film NFT

The Film NFT is your copyright instrument. It:
- Earns 10% of all streaming revenue from your film (paid monthly via Treasury smart contract)
- Is freely transferable at any time — you can sell it (and its associated revenue stream) whenever you choose
- Standard Library contributors receive a separate 5% share — your Film NFT represents the full 10% director share
- Can be supplemented with Film Ticket NFT crowdfunding (see above) — Ticket NFTs take from the creator pool, not your 10%

---

## Code of Conduct

- **Respect all contributors** regardless of experience level or background
- **No plagiarism** — all submissions must be your own original AI-generated work
- **Copyright attestation** is required on every submission
- **No coordinated backing** — backing rings or self-backing schemes (using multiple wallets to inflate quadratic backing weight) will result in stake forfeiture and ban. No coordinated voting in platform governance — voting rings will result in $MONTAGE stake slashing.
- **Constructive feedback** in `#director-chat` channels. Critique the work, not the person.
- **No NSFW content** unless the film project is explicitly tagged and age-gated

---

## Getting Help

| Topic | Channel |
|-------|---------|
| AI tool setup, LoRA loading, generation pipelines | `#tools-help` |
| Standard Library questions and troubleshooting | `#standard-library-workshop` |
| Discord bot issues or command errors | `#bot-support` |
| General questions about OpenMontage | `#general` |
| Direct help from the team | DM any member with the `@Admin` role |

---

## For Developers

The repository is currently documentation-only (pre-alpha). We are actively seeking developers starting Q2 2026 for:

- **Discord bot** — Node.js or Python, handles all user commands and blockchain interactions. See the [Discord Platform Design](doc/discord-platform.md) for bot command specs and architecture.
- **Smart contracts** — Solidity, deploying on Base L2 (Coinbase). Core contracts: ClipNFT, FilmNFT, Treasury, ShotMarket, StandardLibrary. See [Whitepaper Section 6](doc/whitepaper.md#6-technical-implementation) for smart contract interfaces and chain selection rationale.
- **Web dashboard** — React / Next.js, initially read-only film browser and stats, later full workflow support (Phase 2+)

For the full development timeline and technical milestones, see the [Roadmap](doc/roadmap.md).

### Contributing to Documentation

Documentation contributions are welcome now. If you find errors, unclear explanations, or missing information:

1. Fork the repository
2. Make your changes — keep docs consistent across all files (the [whitepaper](doc/whitepaper.md) is the authoritative source)
3. Submit a pull request with a clear description of what you changed and why

### Developer Channels

- Watch `#announcements` for developer calls and contributor onboarding
- Join `#dev-chat` for technical discussion
- Check the [Roadmap](doc/roadmap.md) for upcoming development milestones

---

## Thank You

Every contributor — whether you generate a single shot, back one clip, or build an entire film — is helping prove that collaborative filmmaking works. We're building something new here, and we're glad you're part of it.

Questions? Drop them in `#general` on [Discord](https://discord.gg/openmontage) or open a GitHub issue.

---

**Version**: 1.1
**Last Updated**: 2026-03-04
**Authors**: OpenMontage Core Team
