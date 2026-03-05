# OpenMontage Operations Manual

**Version 2.0 | Platform Operations & Community Guide**

---

## Table of Contents

1. [Discord-First Strategy](#1-discord-first-strategy)
2. [Discord Server Architecture](#2-discord-server-architecture)
3. [Bot Command Reference (MontageBot)](#3-bot-command-reference-montagebot)
4. [Director Complete Journey](#4-director-complete-journey)
5. [Creator Complete Workflow](#5-creator-complete-workflow)
6. [Backer Complete Workflow](#6-backer-complete-workflow)
7. [Standard Library Best Practices](#7-standard-library-best-practices)
8. [Quality Validation Pipeline](#8-quality-validation-pipeline)
9. [Community Management](#9-community-management)
10. [Roadmap](#10-roadmap)
11. [Getting Started Guide](#11-getting-started-guide)
12. [Discord to Web Transition Plan](#12-discord-to-web-transition-plan)

---

## 1. Discord-First Strategy

### 1.1 Why Discord First

OpenMontage launches as a Discord-first platform. Rather than building a custom web application before validating the core product loop, all Phase 1 interactions happen inside the OpenMontage Discord server at [discord.gg/SS8BdjYH6W](https://discord.gg/SS8BdjYH6W).

The AI creator community already lives on Discord. Midjourney's server has 19M+ members. Runway, Pika, and Sora communities each host hundreds of thousands of active creators who already know how to generate video from prompts, load LoRAs, and iterate on AI output. These are exactly the people OpenMontage needs — and they are already logged in.

Building a standalone web app from day one means competing for attention against platforms creators already use daily. A Discord-first approach meets them where they are.

**Concrete advantages:**

- **No new account required**: Creators authenticate with their existing Discord identity.
- **Familiar interface**: Channels, threads, embeds, and slash commands are second nature to this audience.
- **Built-in community features**: Voice channels for director-creator calls, threaded discussions per shot, role-based permissions, and notification controls.
- **Mobile-ready**: Discord's mobile app gives creators on-the-go access to claim shots, check earnings, and back clips — without building native apps.
- **Faster iteration**: Discord bots can be deployed and updated in hours. A slash command interface lets the team test workflows, gather feedback, and iterate on UX before committing to expensive frontend development. The bot is the MVP.

### 1.2 Transition Path (Phase 1 to Phase 3)

Discord-first does not mean Discord-only. The platform evolves across phases:

| Phase | Timeline | Primary Interface | Discord Role |
|-------|----------|-------------------|--------------|
| Phase 1 | Q2-Q3 2026 | Discord bot | Full platform — all actions happen here |
| Phase 2 | Q4 2026 | Web dashboard launches | Discord as notification + quick-action layer |
| Phase 3 | 2027+ | Web + mobile apps | Discord as community hub + alerts |

Discord remains a permanent part of the stack — it transitions from primary interface to companion channel, never disappearing entirely.

---

## 2. Discord Server Architecture

The server is organized into five top-level categories, each serving a distinct function. Film-specific categories are created dynamically by MontageBot when a director launches a new project.

### 2.1 Channel Structure

#### Category: ANNOUNCEMENTS

| Channel | Purpose | Permissions |
|---------|---------|-------------|
| `#announcements` | Platform updates, partnerships, milestones | Read-only (Admin post) |
| `#changelog` | Bot updates, new features, bug fixes | Read-only (Admin post) |
| `#governance-proposals` | $MONTAGE governance proposals and discussion | `@Verified-Voter` can discuss; Admin posts proposals |

#### Category: FILM PROJECTS

| Channel | Purpose | Permissions |
|---------|---------|-------------|
| `#film-listings` | Bot posts new films seeking contributors — browsable catalog | Read-only (Bot post) |

#### Category: CREATOR HUB

| Channel | Purpose |
|---------|--------|
| `#tools-help` | Troubleshooting AI tools, LoRA loading, ComfyUI workflows |
| `#standard-library-workshop` | Discussion on building and using Standard Libraries |
| `#showcase` | Completed/merged segments — creators share their wins |
| `#ai-tools-discussion` | News and discussion about Sora, Runway, Pika, ComfyUI, etc. |

#### Category: TOKENOMICS

| Channel | Purpose |
|---------|--------|
| `#earnings-dashboard` | Bot responds to `/my-earnings` — personal revenue tracking |
| `#nft-marketplace` | Clip NFT listings, secondary sales, trading discussion |
| `#governance-voting` | Active $MONTAGE platform governance votes |
| `#faucet` | Request testnet $MONTAGE tokens and testnet USDC during Phase 1 |

#### Category: COMMUNITY

| Channel | Purpose |
|---------|--------|
| `#introductions` | New members introduce themselves |
| `#general` | Off-topic and general discussion |
| `#feedback` | Platform feedback and feature requests |
| `#verify-wallet` | Connect wallet via Collab.Land for on-chain identity verification |
| `#role-select` | Self-assign Creator, Backer, Director, Developer roles via bot reactions |
| `#bot-support` | Report MontageBot issues and command errors |

### 2.2 Dynamic Film Category Creation

When a director creates a film via `/create-film`, MontageBot automatically generates a dedicated category for that film:

**Category: [FILM-TITLE] (e.g., SPACE-ODYSSEY-2077)**

| Channel | Purpose | Permissions |
|---------|---------|-------------|
| `#film-overview` | Pinned: script link, Standard Library download, technical specs, treasury parameters | Read-only (`@Director` pins) |
| `#shots-available` | Bot-maintained list of unclaimed shots with descriptions and durations | Read-only (Bot-managed) |
| `#submissions` | All submitted segments posted as embeds with clip details | `@Creator` can submit; all can view |
| `#shot-markets` | Active Shot Market Menu Auctions — commit/reveal valuation menus for clips | `@Backer` can back; all can view |
| `#main-branch` | Current merged film — progress tracker showing completion %, timeline of merges | Read-only (Bot-managed) |
| `#director-chat` | Q&A between director and contributors — threaded per topic | `@Director` + `@Creator` |

### 2.3 Permissions and Role System

| Discord Role | Requirement | Capabilities |
|-------------|-------------|--------------|
| `@Verified-Voter` | Wallet connected via Collab.Land | View earnings, participate in platform governance |
| `@Creator` | Wallet verified + first `/claim-shot` | Submit segments, claim shots (up to 5 active simultaneously) |
| `@Backer` | Wallet verified + USDC balance | Back clips in Shot Markets, earn backer rewards |
| `@Director` | Wallet verified + Film Creation Bond deposited | Manage film channels, pin notes, Film NFT holder |
| `@Admin` | Core team assignment | Full server management, bot configuration, ban/kick |

Role assignment is self-serve via `#role-select` for Creator, Backer, and Developer. Director role is granted automatically when a Film Creation Bond is deposited via `/create-film`.

---

## 3. Bot Command Reference (MontageBot)

MontageBot handles all platform operations through slash commands. Every command provides immediate feedback via Discord embeds. Commands can be run in the relevant film channel or as DMs to the bot.

### `/create-film`

```
/create-film title:[name] script:[url]
```

Starts the film setup wizard. MontageBot DMs the director a step-by-step flow: upload script, upload Standard Library assets, deposit Film Creation Bond (minimum 500 USDC; recommended: 50 USDC x number of shots), configure shot durations, and confirm treasury parameters.

On completion, MontageBot:
- Mints the Film NFT on-chain
- Deploys the Treasury smart contract
- Creates the film's full channel category
- Posts the listing embed to `#film-listings`

**Example embed posted to `#film-listings`:**

```
+---------------------------------------------+
|  NEW FILM: Space Odyssey 2077               |
|---------------------------------------------|
|  Director: @CosmicDirector                  |
|  Genre: Sci-fi / Drama                      |
|  Shots: 240 total (240 available)           |
|  Avg Shot Duration: 6-12 seconds            |
|  Standard Library: v1.0.0 (5 characters,   |
|    cyberpunk style, 3 voice models)         |
|  Film NFT: 0x7b2...a41 (minted)            |
|  Film Creation Bond: 5,000 USDC            |
|  Revenue Split: 75% creators / 10%         |
|    Film NFT holder / 5% Standard Library   |
|    / 10% backers                           |
|                                             |
|  Script: [View Script]                     |
|  Standard Library: [Download]              |
|                                             |
|  [Browse Shots]  [Read Overview]           |
+---------------------------------------------+
```

---

### `/claim-shot`

```
/claim-shot film:[film-id] shot:[shot-id]
```

Locks the shot for 72 hours. MontageBot DMs the creator with Standard Library download links, the shot's script excerpt, target duration, and technical requirements. If the shot is already claimed, the bot notifies and shows when the lock expires.

**Claim rules:**
- Maximum 5 active shot claims per creator across all films simultaneously.
- Creators receive a DM notification 12 hours before expiration and again at expiration.
- The same creator may re-claim a shot immediately after expiration unless they have hit the 5-claim limit.
- Consecutive claim expirations (3+ in 30 days) trigger a 48-hour claim cooldown.

**Example DM to creator:**

```
+---------------------------------------------+
|  Shot Claimed: #042 -- Chase Scene          |
|---------------------------------------------|
|  Film: Space Odyssey 2077                   |
|  Duration: 8-12 seconds                     |
|  Resolution: 1080p minimum                  |
|  Lock expires: 2026-03-06 14:00 UTC         |
|                                             |
|  Script excerpt:                            |
|  "The protagonist sprints through neon-lit  |
|  alleyways, pursuers close behind..."       |
|                                             |
|  Standard Library v1.0.0:                  |
|  - protagonist.safetensors [Download]       |
|  - antagonist.safetensors [Download]        |
|  - cyberpunk-style-guide.png [Download]     |
|  - prompt-templates.json [Download]         |
|  - protagonist-voice.model [Download]       |
|                                             |
|  Submit with:                               |
|  /submit film:space-odyssey shot:042        |
|    url:[your-video] notes:[desc]            |
+---------------------------------------------+
```

---

### `/release-shot`

```
/release-shot film:[film-id] shot:[shot-id]
```

Releases a claimed shot back to `#shots-available` before the 72-hour lock expires. Use this if you cannot complete the shot so other creators can pick it up. Does not count against the repeated-expiry cooldown tracker.

---

### `/submit`

```
/submit film:[film-id] shot:[shot-id] url:[video-url] notes:[description]
```

MontageBot downloads and validates the video (see Section 8: Quality Validation Pipeline). If validation passes, the submission is minted as a Clip NFT and posted to the film's `#submissions` channel. The 50 USDC Creator Bond is charged from the connected wallet. If validation fails, the bot DMs the creator with specific errors — the shot lock remains active for fixes and resubmission.

**Example embed posted to `#submissions`:**

```
+---------------------------------------------+
|  New Submission: Shot #042                  |
|---------------------------------------------|
|  Creator: @NeonArtist                       |
|  Duration: 9.2s | Resolution: 1080p         |
|  AI Model: Runway Gen-3 + protagonist LoRA  |
|  Std Library: v1.0.0 [OK]                  |
|  Clip NFT: 0xa3f...8c2                      |
|                                             |
|  Notes: "Went for a Blade Runner-inspired   |
|  lighting approach with heavy fog."         |
|                                             |
|  [Watch Video]                              |
|                                             |
|  Shot Market Menu Auction opens when >=2   |
|  clips are in.                             |
|  Current clips for this shot: 1            |
+---------------------------------------------+
```

---

### `/back-shot` (Rank-Based Shortcut)

```
/back-shot film:[film-id] shot:[shot-id] deposit:[USDC] rank:[clip1,clip2,...] salt:[your-secret]
```

Commits a **valuation menu** covering ALL competing clips for a shot during the 5-day commit phase. The rank-based shortcut automatically distributes your deposit across clips in descending order based on rank position (e.g., rank 1 gets the full deposit amount as its valuation, rank 2 gets 50%, etc.). Your deposit equals the maximum valuation (the top-ranked clip's value). Only a commitment hash (`hash(clipIds[] + valuations[] + salt)`) is stored on-chain. MontageBot DMs a confirmation with the commitment hash and deadlines.

**Keep your salt.** You must use it in `/reveal-backing`. If you lose your salt, you cannot reveal and will forfeit your deposit.

---

### `/back-shot-menu` (Explicit Valuation Menu)

```
/back-shot-menu film:[film-id] shot:[shot-id] deposit:[USDC] values:[clip1:amt1,clip2:amt2,...] salt:[your-secret]
```

Commits an **explicit valuation menu** where you set a specific USDC valuation for each competing clip. Your deposit must equal the maximum of your stated valuations. After resolution, you pay only your stated valuation for the winning clip; excess deposit is refunded in full. Only a commitment hash (`hash(clipIds[] + valuations[] + salt)`) is stored on-chain.

Use this command when you want precise control over your valuations rather than the automatic rank-based distribution of `/back-shot`.

**Example DM confirmation (applies to both `/back-shot` and `/back-shot-menu`):**

```
+---------------------------------------------+
|  Valuation Menu Committed                   |
|---------------------------------------------|
|  Film: Space Odyssey 2077                   |
|  Shot: #042 -- Chase Scene                  |
|  Deposit: 100 USDC (= max valuation)       |
|  Commitment Hash: 0x9f2...b71              |
|                                             |
|  Commit Phase Deadline: 2026-03-09 14:00   |
|  Reveal Phase: 2026-03-09 to 2026-03-11   |
|                                             |
|  Current commitments on this shot:         |
|  - 10 total commitments                    |
|  - 3 competing clips                       |
|  (Valuations hidden until reveal phase)    |
+---------------------------------------------+
```

---

### `/reveal-backing`

```
/reveal-backing film:[film-id] shot:[shot-id] salt:[your-secret]
```

Reveals your full valuation menu during the 2-day reveal phase. The bot verifies the reveal against the stored commitment hash — all clip IDs and valuations must match exactly. Backers who committed but fail to reveal lose their entire deposit (counted as forfeit, not a partial refund).

**Example DM after reveal:**

```
+---------------------------------------------+
|  Valuation Menu Revealed                    |
|---------------------------------------------|
|  Film: Space Odyssey 2077                   |
|  Shot: #042 -- Chase Scene                  |
|  Your Valuations:                           |
|  - Clip 0xa3f...8c2: 100 USDC              |
|  - Clip 0xb7e...d43:  50 USDC              |
|  - Clip 0xc1a...f92:  20 USDC              |
|  Deposit held: 100 USDC (= max valuation)  |
|                                             |
|  Current Leaderboard (SelectionWeight):    |
|  1. Clip 0xa3f...8c2  -- weight: 22.4      |
|  2. Clip 0xb7e...d43  -- weight: 18.1      |
|  3. Clip 0xc1a...f92  -- weight: 12.7      |
|                                             |
|  Reveal Phase Deadline: 2026-03-11 14:00   |
+---------------------------------------------+
```

---

### `/claim-market`

```
/claim-market film:[film-id] shot:[shot-id]
```

After Shot Market resolution, claim your winner reward or loser refund. The ShotMarket smart contract calculates your payout automatically.

**Example DM — winner:**

```
+---------------------------------------------+
|  Shot Market Menu Auction Resolved -- Won!  |
|---------------------------------------------|
|  Film: Space Odyssey 2077                   |
|  Shot: #042 -- Chase Scene                  |
|  Winning Clip: 0xa3f...8c2 by @NeonArtist  |
|                                             |
|  Your valuation for winner: 100 USDC       |
|  Amount charged: 100 USDC                  |
|  Deposit refund (excess): 0 USDC           |
|  Competition payout: +18.50 USDC           |
|  Net claimable: 18.50 USDC                 |
|                                             |
|  You also earn ongoing streaming revenue   |
|  from the 10% backer pool for this shot.   |
|                                             |
|  [Claim Now]                               |
+---------------------------------------------+
```

*(Example calculation: 3 losing Creator Bonds x 50 USDC = 150 USDC forfeited (25% each = 37.50 USDC total). Reward Pool = 20% of losing bonds = 30 USDC. Backer share = 50% x 30 = 15 USDC total. Your share based on quadratic proportion of winning-clip valuations.)*

**Example DM — backer whose top-ranked clip lost:**

```
+---------------------------------------------+
|  Shot Market Menu Auction Resolved          |
|---------------------------------------------|
|  Film: Space Odyssey 2077                   |
|  Shot: #042 -- Chase Scene                  |
|  Winning Clip: 0xb7e...d43                  |
|                                             |
|  Your valuation for winner: 50 USDC        |
|  Amount charged: 50 USDC                   |
|  Deposit refund (excess): 50 USDC          |
|  (Deposit was 100, max valuation refunded) |
|                                             |
|  [Claim Refund]                            |
+---------------------------------------------+
```

---

### `/film-status`

```
/film-status film:[film-id]
```

Displays a real-time snapshot of a film's progress, active markets, and recent activity.

**Example response:**

```
+---------------------------------------------+
|  Space Odyssey 2077 -- Status               |
|---------------------------------------------|
|  Progress: [========--] 78% (187/240 shots) |
|  Active claims: 23 shots                    |
|  Open shots: 30 shots                       |
|  Clips in active markets: 12               |
|  Total contributors: 94                     |
|  Estimated runtime: 38m 22s                 |
|                                             |
|  Recent merges:                             |
|  - Shot #187 -> @PixelMage (2h ago)         |
|  - Shot #042 -> @NeonArtist (5h ago)        |
|  - Shot #103 -> @CinemaAI (1d ago)          |
+---------------------------------------------+
```

---

### `/my-earnings`

```
/my-earnings
```

Shows your revenue across all films — streaming revenue from merged Clip NFTs and Shot Market competition rewards. Results are sent as a DM.

**Example DM:**

```
+---------------------------------------------+
|  Your Earnings -- @NeonArtist               |
|---------------------------------------------|
|  Active Clip NFTs: 4 (merged)               |
|  Archived Clip NFTs: 1                      |
|                                             |
|  Monthly revenue estimate: $342.18         |
|                                             |
|  Breakdown:                                 |
|  - Space Odyssey, Shot #042 (9.2s): $128.44 |
|  - Space Odyssey, Shot #119 (6.8s):  $95.02 |
|  - Neon Dreams, Shot #007 (11.1s):   $78.40 |
|  - Neon Dreams, Shot #033 (5.4s):    $40.32 |
|                                             |
|  Lifetime earnings: $1,204.55              |
|  Wallet: 0x7a2...f91                        |
+---------------------------------------------+
```

---

### `/leaderboard`

```
/leaderboard film:[film-id]
```

Shows top contributors ranked by total merged screen time for the given film. Useful for directors promoting their project and for creators scouting competitive films.

---

### `/standard-library`

```
/standard-library film:[film-id]
```

Returns download links for all Standard Library assets for the specified film, including version number and individual file sizes. Always pull the latest version before starting work on a new shot.

---

## 4. Director Complete Journey

### 4.1 Project Concept and Script Selection

Not every script works well on OpenMontage. The best candidates are films with discrete, visually-describable shots that can be independently generated by different creators:

**Best fits:**
- Short-to-medium films (20–120 minutes of final runtime)
- Clear visual style that can be encoded in a Standard Library
- Anthology stories, episodic narratives, music videos, sci-fi with distinct environments
- Scripts where creative variation between shots is acceptable or desirable

**Less suitable:**
- Films requiring extremely tight frame-level continuity across long takes
- Documentary style relying on real-world footage
- Projects where the director needs granular control over every frame

### 4.2 Writing Scripts for AI Generation

Write with AI generation in mind. Each shot description should read like a detailed image/video prompt: specific about visual elements, lighting, camera angle, and character action. Avoid vague directions like "they exchange a meaningful look." Instead write: "close-up of PROTAGONIST, tears forming, warm backlighting, slight head tilt left." The more precise your shot descriptions, the better submissions will be.

### 4.3 Shot Decomposition Guide (Best Practices)

Break the script into individual shots following these guidelines:

- **Duration**: 5–15 seconds per shot. Shorter shots are easier to generate consistently; longer shots compound AI artifacts.
- **Visual clarity**: Each shot should have one primary action or visual focus.
- **Character limits**: Two characters maximum per shot yields the best results with current AI tools.
- **Transitions**: Plan for hard cuts. Complex transitions (dissolves, wipes) are applied in platform post-production.

A typical 90-minute film decomposes into 200–400 shots.

### 4.4 Standard Library Construction (Detailed Steps)

The Standard Library is the single most important factor in a film's success. A great library attracts top creators and produces visually coherent results; a weak library produces inconsistent submissions that frustrate everyone.

**Step 1 — Train Character LoRAs**

Use Kohya SS or equivalent tools. Budget approximately 3–5 hours of training time per character.

- Provide 50+ reference images per character minimum (ideally 100+)
- Include diverse angles: frontal, 3/4 view, profile, varied expressions
- Cover multiple lighting conditions and clothing/settings
- Training image resolution: minimum 512x512, ideally 1024x1024
- After training, generate 20+ test images across varied prompts to verify recognition

**Step 2 — Write the Style Guide**

Define the film's visual identity in concrete, measurable terms:

- Color palette with hex codes
- Lighting approach with reference images (e.g., "high-contrast noir, key light 45° camera-left, rim light from behind, 4500K")
- Camera language — preferred focal lengths, angles, depth of field
- Mood/tone references — link 3–5 reference films or images
- Optional: title card typography, aspect ratio preference (16:9, 2.39:1), sound design direction

**Step 3 — Build Prompt Templates**

Structure templates with clear placeholders:

```
[CHARACTER_LORA] [ACTION_DESCRIPTION], [LOCATION], [LIGHTING_STYLE],
[CAMERA_ANGLE], [STYLE_TOKENS]
```

Example for a dialogue shot:
```
[PROTAGONIST_LORA] speaking to [ANTAGONIST_LORA], indoor office setting,
noir high-contrast lighting with teal accent, medium close-up, cinematic
35mm film grain, shallow depth of field
```

Include negative prompt templates to suppress common AI artifacts.

**Step 4 — Add Voice Clone Models** (if dialogue)

Use ElevenLabs or equivalent services. Create one voice model per speaking character. Consistent voice synthesis matters as much as visual consistency for audience immersion.

**Step 5 — Add ControlNet Specifications** (optional but recommended for action shots)

Provide skeletal rigs or depth maps for shots requiring precise character poses or camera movements.

**Step 6 — Test End-to-End**

Before opening the film for contributions, run a complete test: generate at least one sample clip using every element of the Standard Library. Verify characters are recognizable, style is consistent, and prompt templates produce usable results. Fix issues before contributors encounter them.

### 4.5 Launching on Discord

Run `/create-film` in any channel or DM MontageBot to start the setup wizard. The wizard walks through:

1. Film metadata: title, genre, logline
2. Script upload and shot breakdown review
3. Standard Library asset upload (LoRAs, style guide, prompt templates, voice models)
4. Film Creation Bond deposit — minimum 500 USDC; recommended 50 USDC x number of shots
5. Revenue split confirmation (default: 75% creators / 10% Film NFT / 5% Standard Library / 10% backers)
6. Optional Film Ticket NFT configuration for crowdfunding

**Important**: If you enable Film Ticket NFTs, the revenue split adjusts from the standard **75/10/5/10** to **65/10/5/10/10** — the 10% for Ticket NFT holders is drawn from the creator pool (75%→65%). The Film NFT holder's 10%, Standard Library's 5%, and Backer pool's 10% remain unchanged.

On completion, MontageBot mints the Film NFT, deploys the Treasury smart contract, creates all film channels, and posts to `#film-listings`.

**Film Creation Bond mechanics:**
- 50% refunded when the film reaches 80%+ shots merged (completion milestone)
- 50% retained in Film Treasury to fund ongoing competition operations
- The Film NFT you receive represents your copyright and 10% of all streaming/licensing revenue

### 4.6 Daily Management Responsibilities

Your job does not end at launch. Active directors produce better films:

- Monitor `#submissions` for new clip submissions and post constructive feedback in `#director-chat`
- Answer creator questions in `#director-chat` promptly — response time correlates with contributor retention
- Track the film's progress in `#main-branch` and `#shot-markets`
- Update the Standard Library if consistency issues emerge — bump the version number and notify contributors via bot
- Promote the film externally to attract top-tier AI creators (Twitter/X, Reddit, AI art communities)

### 4.7 Director Restrictions

**Directors cannot back clips in their own film's Shot Markets.** This prevents conflicts of interest and ensures the market remains a genuine community quality signal. The ShotMarket smart contract enforces this restriction on-chain — any backing transaction from the Film NFT holder's wallet on that film's Shot Markets will revert.

Directors can post feedback in `#director-chat` but cannot delete submissions or override Shot Market results. Film-level governance remains community-driven.

---

## 5. Creator Complete Workflow

### 5.1 Discovering and Browsing Films

Start in `#film-listings` to browse all active film projects. Each listing includes the logline, genre, number of open shots, Standard Library version and quality preview, and revenue split.

Click through to the film's `#film-overview` to read the full script and style direction. Download the Standard Library and review the style guide before deciding whether to contribute. If you have questions, ask in `#director-chat` before starting work.

### 5.2 Claiming a Shot (72-Hour Lock Mechanism)

1. Browse `#shots-available` in the film's channel category to see unclaimed shots with descriptions
2. Run `/claim-shot film:[id] shot:[id]` to lock the shot for 72 hours exclusively to you
3. MontageBot DMs you: shot description, script excerpt, technical requirements, duration target, and Standard Library download links
4. If you cannot complete the shot, run `/release-shot film:[id] shot:[id]` to return it immediately

**Claim limits and restrictions:**
- Maximum 5 active claims simultaneously across all films
- Shots that expire without submission are released automatically back to `#shots-available`
- 3 or more consecutive expirations within 30 days trigger a 48-hour claim cooldown

### 5.3 Generating Your Video (Technical Requirements)

**Format requirements:**
- Container: MP4
- Codec: H.264 (or H.265)
- Resolution: Minimum 1920x1080 (1080p); 4K preferred
- Frame rate: 24fps (cinematic) or 30fps
- Duration: Must fall within the shot's specified range within ±0.5 seconds
- Audio: Include if the shot requires dialogue (use provided voice models); omit if background-only

**Quality guidelines:**
- Load character LoRA files into your generation pipeline before starting
- Follow the prompt templates from the Standard Library — they exist for a reason
- Character LoRA adherence is the most important factor. Backers weight visual consistency heavily. If your protagonist does not look recognizably like the protagonist, the submission will lose regardless of cinematographic quality.
- Lighting must match the film's style guide — check reference images carefully
- Avoid AI artifacts: melting faces, warping limbs, flickering textures
- Match camera angles and framing from any storyboard references in the shot description

**Recommended tools:**
- AI video generation: Runway Gen-3+, Pika 1.5+, Kling, Sora, ComfyUI
- Image generation for storyboarding: Midjourney, SDXL/Flux
- Basic video editing: DaVinci Resolve (free), CapCut

### 5.4 Submitting Your Work

1. Upload your video to a publicly accessible URL (IPFS preferred; cloud storage acceptable)
2. Run `/submit film:[id] shot:[id] url:[video-url] notes:[brief description of your approach]`
3. MontageBot validates the submission automatically (format, resolution, duration, Standard Library version)
4. If approved: a Clip NFT is minted on-chain; your 50 USDC Creator Bond is charged; the submission embed appears in `#submissions`
5. If rejected: MontageBot DMs you the specific validation errors with remediation steps; your shot lock remains active

### 5.5 Shot Market Menu Auction Mechanics

Once a shot has 2 or more submissions, the **Shot Market Menu Auction** opens:

**Commit Phase (Days 1–5)**

Your 50 USDC Creator Bond is locked and automatically contributes to your clip's SelectionWeight via `√(CreatorBond) = √50 ≈ 7.07` — counted as a separate term in the quadratic formula. Note: this is distinct from backer valuations; your revenue as a creator comes from the creator pool (75% of film revenue), not the backer pool (10%). Community backers evaluate all competing clips and submit hidden **valuation menus** — a hash of `(clipIds[] + valuations[] + salt)` — expressing a USDC valuation for EVERY competing clip. The backer's deposit equals their maximum valuation across all clips. During the commit phase, total commitment counts are publicly visible on-chain. However, **valuations** and **backer identities** remain hidden until the reveal phase — preventing bandwagoning.

**Reveal Phase (Days 6–7)**

Backers reveal their full valuation menus by submitting their salt. The ShotMarket contract verifies each reveal against the stored commitment hash — all clip IDs and valuations must match. Backers who committed but fail to reveal lose their entire deposit — there is no partial refund for non-revealers.

**Resolution**

The clip with the highest SelectionWeight wins: `SelectionWeight = √(CreatorBond) + Σ√(v_i_c)` where `v_i_c` is backer `i`'s valuation for clip `c`. The creator's 50 USDC bond contributes `√(50) ≈ 7.07` as a separate term. Quadratic weighting means 100 backers valuing a clip at 100 USDC each outweighs 1 whale valuing at 1,000,000 USDC — quality of consensus beats concentration of capital. Ties are broken by earliest submission timestamp.

After resolution, each backer pays only their stated valuation for the winning clip. Since deposits equal the maximum valuation, any excess (deposit minus winning-clip valuation) is refunded in full.

**If your clip wins:**
- Clip NFT status changes to "merged" and begins earning streaming revenue immediately
- Full 50 USDC Creator Bond returned
- 50% of the Reward Pool paid to you as the winning creator

**If your clip loses:**
- 75% of the 50 USDC Creator Bond refunded (you lose 25% = 12.50 USDC)
- The forfeited 25% funds the Reward Pool (20%) and Competition Tax (5%)
- Clip NFT status is "archived" — you retain it; you can resubmit an improved version

**Single-submission shots:** If a shot does not reach 2 clips within 30 days, the director may trigger an auto-merge of the single submission per the governance framework.

### 5.6 Replacement Challenges

After a shot is merged, any creator can challenge the incumbent by submitting a new clip:

- **21-day cooldown** after each market resolution per shot (protects incumbents from harassment)
- **Challenge Bond**: 100 USDC (2x the Creator Bond), signaling serious intent
- **Incumbency advantage**: The existing merged clip starts with 50% synthetic backing weight from its original win
- A compressed Shot Market runs: 7-day submission window for the incumbent to respond, then a mini-market backing phase: 5 days total (3-day commit + 2-day reveal) — deliberately compressed vs. the standard 7-day backing phase (5+2) to accelerate challenge resolution
- **If challenger wins**: Incumbent clip archived, challenger clip merged; challenger gets full Challenge Bond back plus their Reward Pool share
- **If challenger loses**: 75 USDC (75%) refunded to the challenger. Of the forfeited 25 USDC: 12.50 USDC → incumbent creator as a defense reward; 12.50 USDC → Film Treasury

### 5.7 Tracking Earnings

Run `/my-earnings` in any channel or DM MontageBot to see:

- All active (merged) and archived Clip NFTs
- Streaming revenue breakdown by film and shot
- Shot Market competition reward history
- Monthly revenue estimate and lifetime total

Revenue comes from two sources:
1. **Streaming revenue**: 75% of film revenue distributed proportionally by screen time
2. **Competition rewards**: Share of the Reward Pool for winning Shot Markets

Revenue accrues in real-time as films are streamed. Claim earnings weekly via the Treasury smart contract using `/claim-earnings`.

---

## 6. Backer Complete Workflow

### 6.1 Discovering Active Shot Markets

MontageBot pings the `@Backer` role in a film's `#shot-markets` channel whenever a new Shot Market opens. Watch these notifications to find markets early — committing early, before the crowd, gives your backing higher marginal impact on the quadratic weight.

Browse active markets with `/film-status film:[id]` to see how many shots currently have open markets.

### 6.2 Evaluating Video Clips

Before committing, evaluate each clip systematically:

1. **Standard Library adherence (most important)**: Does the character look recognizably correct? Do lighting and style match the guide? Download the Standard Library and literally compare the clip against the LoRA reference: `/standard-library film:[id]`
2. **Technical quality**: Smooth motion, accurate lighting, no AI artifacts (melting faces, warping limbs, flickering textures)
3. **Creative execution**: How well does it interpret the shot description? Does it advance the narrative?

Watch all competing clips via the "Watch Video" links in the `#submissions` embeds before committing. The community tends to converge on clips with strong Standard Library adherence — this is the #1 predictor of market consensus.

### 6.3 Submitting a Hidden Valuation Menu (Commit Phase)

During the 5-day commit phase, submit a **valuation menu** covering ALL competing clips using either command:

```
/back-shot film:[film-id] shot:[shot-id] deposit:[USDC] rank:[clip1,clip2,...] salt:[your-secret]
```

or for explicit control:

```
/back-shot-menu film:[film-id] shot:[shot-id] deposit:[USDC] values:[clip1:amt1,clip2:amt2,...] salt:[your-secret]
```

- Your deposit (equal to your maximum valuation) is locked immediately
- You express a valuation for EVERY competing clip — not just one
- After resolution, you pay only your stated valuation for the winning clip; excess deposit is refunded in full
- During the commit phase, total commitment counts are visible on-chain, but **valuations** and **backer identities** remain hidden — preventing bandwagoning
- Choose a memorable salt — you must use it exactly in the reveal step

Each backer submits **one valuation menu per shot** — this is enforced at the smart contract level. The menu must cover all competing clips; omitting a clip is treated as a zero valuation for that clip.

### 6.4 Revealing Your Valuation Menu (Reveal Phase)

During the 2-day reveal phase:

```
/reveal-backing film:[film-id] shot:[shot-id] salt:[your-secret]
```

MontageBot verifies your full valuation menu against the stored commitment hash. If all clip IDs and valuations match, your menu is added to the on-chain leaderboard and each clip's SelectionWeight is updated.

**Do not forget to reveal.** Backers who fail to reveal during the 2-day window lose their entire deposit — there is no grace period and no refund for non-revealers.

### 6.5 Claiming Rewards or Refunds

After the Shot Market resolves:

```
/claim-market film:[film-id] shot:[shot-id]
```

The ShotMarket contract automatically calculates your payout. No manual calculation is required.

### 6.6 Backer Revenue Sources

Backers earn from two sources:

**Competition rewards (immediate):**
- You pay only your stated valuation for the winning clip; excess deposit (deposit minus winning-clip valuation) is refunded in full
- Proportional share of 50% of the Reward Pool (funded by losing Creator Bonds), split among winning-clip backers proportional to `√(valuation)`

**Ongoing streaming revenue:**
- 10% of all film streaming revenue is distributed to backers of merged clips, proportional to each backer's valuation share of merged-clip backing across the film
- This revenue persists as long as the film is streamed

**Example:** You valued the winning clip at 100 USDC (deposit was 150 USDC because your top-ranked clip got 150). You pay 100 USDC, get 50 USDC refunded, plus your share of the Reward Pool. You also earn ongoing streaming revenue from the 10% backer pool proportional to your valuation share.

**Note:** Unlike the old model, backers are never penalized for "backing a loser." You submit valuations for ALL clips. You always pay only your stated valuation for whichever clip wins — the mechanism encourages honest preference revelation.

---

## 7. Standard Library Best Practices

### 7.1 What Makes a Good Standard Library

Specificity beats generality. A style guide that says "cinematic lighting" is useless. A style guide that says "high-contrast noir, key light 45° camera-left, rim light from behind, deep shadows on right side of face, 4500K color temperature" produces consistent results across 150 independent contributors.

The Standard Library is a dependency package. Like software packages, it must be specific enough to constrain behavior while flexible enough to allow creative expression within its bounds.

### 7.2 Character LoRA Quality Guidelines

| Requirement | Minimum | Recommended |
|-------------|---------|-------------|
| Training images per character | 30 | 50-100+ |
| Image resolution | 512x512 | 1024x1024 |
| Angle coverage | Frontal, profile | Frontal, 3/4, profile, overhead, low angle |
| Expression coverage | Neutral | Neutral, smiling, angry, surprised |
| Lighting variety | 1 condition | 3+ conditions (indoor, outdoor, night) |
| Post-training test images | 10 | 20+ |

**Consistency in training data:** All reference images must depict the same character design. Mixed references (different hairstyles, outfits, or ages in training data) produce unstable LoRAs that fail under varied prompts.

**Versioning:** Start at v1.0.0. Use semantic versioning:
- Patch (v1.0.1): minor refinements that do not change character design
- Minor (v1.1.0): additions (new expressions, outfits) that are backward compatible
- Major (v2.0.0): redesigns; contributors using v1.x must regenerate

### 7.3 Prompt Template Format

```
[CHARACTER_LORA] [ACTION_DESCRIPTION], [LOCATION], [LIGHTING_STYLE],
[CAMERA_ANGLE], [STYLE_TOKENS], [NEGATIVE_PROMPT_TOKENS]
```

Templates should leave creative latitude in `[ACTION_DESCRIPTION]` and `[CAMERA_ANGLE]` while locking down style tokens and LoRA weights. Do not over-constrain the template — creators need room to interpret the shot.

**Dialogue shot example:**
```
[PROTAGONIST_LORA:0.85] speaking to camera, indoor office setting at night,
noir high-contrast lighting with teal accent, medium close-up at eye level,
cinematic 35mm film grain, shallow depth of field, anamorphic lens flare
Negative: anime, cartoon, illustration, extra fingers, duplicate faces
```

**Action shot example:**
```
[PROTAGONIST_LORA:0.85] running at full sprint through neon-lit alley,
rain-slicked pavement, sodium vapor orange streetlights with blue neon
reflections, tracking shot from behind low angle, motion blur,
cinematic color grade, 24fps
Negative: static, standing still, crowd, anime
```

### 7.4 Style Guide Essential Components

**Mandatory elements:**
- Color palette — 3–5 primary hex codes, 2–3 accent colors
- Lighting language — key light position, color temperature, shadow style, with reference images
- Camera language — preferred focal lengths (e.g., "50mm equivalent, avoid fisheye"), typical angles, depth of field preference
- Tone references — 3–5 links to reference films, frames, or images that capture the visual goal
- Aspect ratio — 16:9, 2.39:1, or other (affects composition framing)

**Optional but recommended:**
- Negative prompt bank — common AI artifacts to suppress
- Transition style preferences (relevant for the platform's post-processing)
- Sound design direction for creators adding audio
- Location/environment reference packs for recurring settings

### 7.5 Common Mistakes to Avoid

| Mistake | Consequence | Fix |
|---------|-------------|-----|
| Fewer than 30 training images per LoRA | Inconsistent character recognition | Gather more reference images before training |
| Vague style guide ("make it cinematic") | Inconsistent submissions, frustrated backers | Add hex codes, reference images, specific lighting specs |
| No negative prompts | AI artifacts proliferate across submissions | Add a negative prompt bank to the template dictionary |
| Not testing the library end-to-end | Issues discovered by contributors, not by you | Generate 5+ test clips before opening contributions |
| Forgetting voice models | Audio inconsistency in dialogue-heavy films | Train voice models for all speaking characters |
| Not versioning properly | Old submissions mixed with new ones | Follow semantic versioning; bump version on any change |

---

## 8. Quality Validation Pipeline

### 8.1 Automated Checks

When a creator runs `/submit`, MontageBot performs a series of automated checks before accepting the submission:

**1. Video Format**
- Container: MP4 required
- Codec: H.264 or H.265
- Minimum resolution: 1920x1080 (1080p)
- Frame rate: 24fps or 30fps
- Audio: AAC codec, or no audio (dialogue can be added in post-production)

**2. Duration**
- Must fall within the shot's specified range (e.g., 8–12 seconds)
- Tolerance: ±0.5 seconds

**3. Standard Library Version**
- Submission metadata must reference the current Standard Library version tag
- MontageBot cross-checks against the film's active library version
- Submissions referencing outdated library versions are rejected with upgrade instructions

**4. File Integrity**
- File must be downloadable and not corrupted
- File size within platform limit: maximum 500 MB per segment

### 8.2 Validation Pass/Fail Handling

**Pass:** MontageBot mints a Clip NFT, charges the 50 USDC Creator Bond, posts the submission embed to `#submissions`, and DMs the creator a confirmation with their NFT contract address.

**Fail:** MontageBot DMs the creator with specific error details and remediation steps. The shot lock remains active — creators can fix issues and resubmit within the original 72-hour window. Failed validations do not consume the shot lock.

**Example failure DM:**

```
+---------------------------------------------+
|  Submission Failed -- Shot #042             |
|---------------------------------------------|
|  Issues found:                              |
|                                             |
|  1. Duration: 14.3s (max allowed: 12.5s)   |
|     -> Trim to 8-12 seconds                 |
|                                             |
|  2. Standard Library: v0.9.0               |
|     (current required: v1.0.0)             |
|     -> Download latest:                    |
|        /standard-library film:space-odyssey |
|                                             |
|  Resubmit with:                            |
|  /submit film:space-odyssey shot:042        |
|    url:[fixed-video] notes:[desc]          |
|                                             |
|  Shot lock remains active until:           |
|  2026-03-06 14:00 UTC                      |
+---------------------------------------------+
```

### 8.3 Phase 2 AI Consistency Scoring

In Phase 2 (Q4 2026), MontageBot will add a pre-screen layer before submissions reach the Shot Market:

- **Face recognition**: Match character faces against LoRA reference images; flag submissions below a configurable similarity threshold
- **Style matching**: Compare color distribution, lighting patterns, and composition against the style guide reference images
- **Automated flagging**: Submissions that fail the AI screen are flagged for director review rather than automatically rejected — the market still makes the final call

This layer reduces the burden on backers by filtering out clearly non-compliant submissions before they consume backer attention.

---

## 9. Community Management

### 9.1 Role Hierarchy

| Role | Scope | Capabilities |
|------|-------|--------------|
| `@Admin` | Server-wide | Full server management, bot configuration, ban/kick, access to `#mod-queue` |
| `@Director` | Per-film channels | Pin notes in film channels, flag submissions, trigger Standard Library updates |
| `@Backer` | Per-film Shot Markets | Back clips with USDC, flag inappropriate content |
| `@Creator` | Per-film submissions | Claim shots (max 5 active), submit segments |
| `@Verified-Voter` | Platform-wide | Platform governance voting on `#governance-voting` |

Directors have authority within their film's channels but cannot delete community submissions or override Shot Market results. Film-level outcomes are community-driven; director authority is limited to operational management.

### 9.2 Spam Prevention Mechanisms

- **Rate limiting**: Maximum 3 submissions per shot per creator per 24-hour period
- **Claim limits**: Maximum 5 active shot claims per creator across all films simultaneously
- **Slowmode**: `#submissions` channels operate with 30-second slowmode to prevent discussion spam
- **Age restrictions**: Two separate restrictions apply: (1) **Wallet age**: wallets less than 7 days old cannot participate in Shot Markets (enforced at the smart contract level via `ShotMarket.sol`). (2) **Discord account age**: as an additional platform-level safeguard, Discord accounts less than 7 days old are rate-limited from claiming shots (enforced at the bot level).
- **Consecutive expiry cooldown**: 3+ expired claim locks in 30 days triggers 48-hour claim cooldown

### 9.3 Content Reporting Flow

Any user can flag a submission via two mechanisms:
1. Discord's built-in right-click report flow
2. The "[Report]" button on every submission embed

Reports are routed to a private `#mod-queue` channel visible only to `@Admin`. Three or more independent reports from different users automatically hide a submission pending admin review — it is removed from `#shot-markets` visibility but not deleted.

Admins review flagged content within 24 hours. Outcomes: clear (submission restored), remove (submission hidden), or escalate (further investigation required).

### 9.4 Copyright Infringement Handling

**Creator attestation:** Every `/submit` command requires the creator to attest that the submission is original AI-generated work and does not infringe third-party IP. This is recorded on-chain with the Clip NFT metadata.

**Director flags:** Directors can flag submissions in their own film that violate Standard Library terms or use unauthorized assets. Flagged submissions are temporarily hidden from Shot Markets and sent to `#mod-queue`.

**Confirmed violations:**
- Clip NFT burned
- Creator suspended from the film (loses `@Creator` role access to film channels)
- Repeat offenders lose the `@Creator` role server-wide

**DMCA takedowns:** Third-party DMCA requests are processed within 48 hours. The corresponding Clip NFT is suspended pending resolution. If the DMCA claim is valid, the NFT is burned and the creator's account is reviewed.

**Code of Conduct violations:**
- No coordinated backing (multiple wallets to inflate quadratic weight) — results in stake forfeiture and ban
- No NSFW content unless the film is explicitly tagged and age-gated
- No plagiarism — all submissions must be original AI-generated work
- Constructive feedback only in `#director-chat` — critique the work, not the person

---

## 10. Roadmap

### 10.1 Phase 1: Foundation (Q2-Q3 2026)

**Theme: Build the foundation, prove the concept**

Phase 1 validates the core thesis — that crowdsourced AI filmmaking with enforced visual consistency and economic incentives can produce a watchable, coherent film. Everything in this phase serves the Pilot Film.

#### Q2 2026 Goals

**Community Infrastructure**
- Launch OpenMontage Discord server (target: 1,000 members by end of Q2)
- Set up full server architecture with per-film channel system
- Deploy welcome flow with role selection (Creator, Backer, Director, Developer)
- Begin community building via AI filmmaking Twitter/X spaces, Reddit posts, and partnerships with AI art communities
- Recruit 10–15 community moderators

**Smart Contract Development**
- Deploy core smart contracts on Base testnet:
  - `ClipNFT.sol` — ERC-721 for segment ownership
  - `FilmNFT.sol` — Composite NFT with dynamic pointer array + Film Creation Bond management
  - `Treasury.sol` — Automated revenue distribution (75/10/5/10 split)
  - `ShotMarket.sol` — Shot Market: submission, backing, commit-reveal, resolution, payouts
  - `StandardLibrary.sol` — Standard Library asset registry and version management
- Internal testing and iteration on testnet
- Publish contract ABIs and developer documentation

**Discord Bot v0.1**
- Core commands: `/claim-shot`, `/submit`, `/back-shot`, `/reveal-backing`, `/claim-market`, `/film-status`, `/my-earnings`
- Basic wallet connection via Collab.Land
- Submission upload flow (video URL + metadata)
- Shot Market commit-reveal workflow

**Standard Library Specification**
- Publish Standard Library Specification v1.0
  - Required asset formats (LoRA .safetensors, ControlNet configs, voice model specs)
  - Versioning scheme (SemVer)
  - Compliance validation criteria
  - Distribution method (Git LFS + CDN)
- Create template repository for directors to bootstrap new film projects

**Pilot Film "Genesis" Kickoff**
- 20-minute sci-fi short film, 100 shots
- Open to 50 invite-only creators selected from community applications
- Director builds Standard Library v1.0: 4 character LoRAs, art style guide, 15 prompt templates, 2 ControlNet rigs, 2 voice clone models

#### Q3 2026 Goals

**Discord Bot v0.5**
- Wallet integration (MetaMask/Coinbase Wallet via Discord)
- Automated Shot Market resolution with transparent result posting
- Leaderboard: top creators by merged screen time, top backers by accuracy rate
- Notification system for new submissions, backing periods, and merge events
- `/compare` command to view side-by-side submissions for a shot

**Pilot Film Completion**
- All 100 shots receive at least one submission
- Shot Market backing selects the best version for each shot
- Final assembly into a watchable 20-minute film
- Public premiere on Discord with live watch party
- Publish detailed learnings: Standard Library findings, creator feedback survey, backing dynamics analysis, technical post-mortem

**Security and Partnerships**
- Smart contract security audit by external firm (OpenZeppelin, Trail of Bits, or Cyfrin)
- Address all critical/high findings before mainnet
- Establish partnerships with 2–3 AI tool providers (Runway, Pika, Kling)

#### Phase 1 Success Criteria

| Criteria | Target |
|----------|--------|
| Creators who submitted at least 1 clip | 50+ |
| Pilot film shots filled | 80%+ (80 of 100) |
| Smart contract security audit | Pass with no unresolved critical issues |
| Community sentiment | Net positive (survey) |
| Discord members | 5,000+ |
| Standard Library compliance rate | 90%+ of submissions pass validation |

---

### 10.2 Phase 2: Alpha Launch (Q4 2026)

**Theme: Open the floodgates**

Phase 2 transitions from a controlled pilot to a public platform. The invite-only gate comes down, mainnet contracts go live, and real economic value starts flowing.

**Mainnet Deployment**
- Deploy all smart contracts to Base mainnet
- Gas optimization pass (batch minting, efficient storage patterns)
- Deploy subgraph for indexing on-chain events (The Graph or custom indexer)

**Web Dashboard v1**
- Film browsing: discover active projects, browse shots, watch current cuts
- Submission management: upload clips, track submission status, view market results
- Revenue tracking: real-time earnings dashboard, claim revenue, transaction history
- Creator profiles: portfolio, backing stats, reputation score
- Wallet connection: MetaMask, Coinbase Wallet, WalletConnect

**Discord Bot v1.0 (Full Feature Set)**
- All v0.5 features plus: revenue claiming, director film creation wizard, automated Standard Library compliance checking, per-film private channels with role-gated access

**Film Ticket NFT Crowdfunding**
- Launch Film Ticket NFT system for inaugural public films
- Ticket holders receive: lifetime streaming access + 10% revenue share taken from the creator pool (creator pool reduces from 75% to 65%; Film NFT holder's 10%, Standard Library's 5%, and backers' 10% remain unchanged)
- Smart contract escrow with milestone-based release

**Public Launch**
- No invite required — any creator can browse films, claim shots, and submit
- Any director can create a new film (minimum Standard Library requirements + Film Creation Bond)
- $MONTAGE test token distribution to Phase 1 contributors

#### Phase 2 KPIs

| KPI | Target |
|-----|--------|
| Discord members | 20,000+ |
| Active film projects | 10 |
| Registered creators | 500+ |
| Film Ticket NFT crowdfunding raised | $50,000+ |
| Films with complete first cuts | 3+ |
| Average time to first submission per shot | < 7 days |

---

### 10.3 Phase 3: Ecosystem Growth (2027)

**Theme: Build the economy**

Phase 3 launches the $MONTAGE token, activates full governance, and builds marketplace infrastructure that transforms OpenMontage from a platform into an ecosystem.

**H1 2027: $MONTAGE Token Launch**
- Token Generation Event (TGE) via IDO on Base-native DEX
- Initial circulating supply: ~80M tokens (8% of total supply)
- DEX liquidity pool seeded with 30M tokens from Liquidity Provision allocation
- CEX listing target: 2–3 mid-tier exchanges within 3 months of TGE
- $MONTAGE governs the platform (fee adjustments, grant allocations, feature priorities) — NOT individual film clip selection, which remains USDC-based Shot Markets

**Full Governance Activation**
- Token-weighted voting replaces testnet governance
- DAO proposal system: any holder with 10,000+ $MONTAGE can submit proposals
- Delegate voting for passive holders

**Advanced Standard Library Tools**
- One-click LoRA training pipeline: directors upload reference images, receive trained LoRA
- Style transfer templates: pre-built style packages (noir, anime, photorealistic, retro)
- AI consistency scoring: automated face recognition and style matching pre-screen
- Standard Library versioning dashboard with migration guides

**Creator Marketplace**
- Secondary market for Clip NFTs (buy/sell merged and archived clips)
- Royalty enforcement: 10% perpetual royalty on resales to original creators
- Price discovery for different shot types, genres, and film popularity levels

**H2 2027: Cross-Film Standard Library Marketplace**
- Directors license Standard Library assets from other films
- Revenue sharing for Standard Library creators across films
- "Remix" capability: adapt characters/styles from one film to another with permission

**DeFi Integration**
- Clip NFT collateral: borrow against merged Clip NFTs (value derived from streaming revenue)
- $MONTAGE staking vaults with tiered rewards and Competition Tax discounts (5% → 2%)

**Mobile App**
- iOS and Android apps for film viewing, voting, and basic submission management
- Push notifications for voting periods, merge events, and revenue claims

**Festival Strategy**
- Submit top OpenMontage films to indie film festivals (Sundance Shorts, SXSW, Tribeca)
- Establish "Made with OpenMontage" branding

#### Phase 3 KPIs

| KPI | Target |
|-----|--------|
| Registered users (Discord + web) | 50,000+ |
| Active film projects | 100+ |
| Total creator earnings distributed | $1,000,000+ |
| $MONTAGE token holders | 10,000+ |
| Standard Library marketplace listings | 50+ |
| Films accepted to festivals | 1+ |

---

### 10.4 Phase 4: Scale and Legitimacy (2028+)

**Theme: Mainstream adoption and institutional credibility**

- Annual "OpenMontage Film Awards" — community-voted, on-chain, with $MONTAGE prize pool
- Studio licensing framework for distribution rights to OpenMontage films
- VR/AR immersive viewing (Meta Quest, Apple Vision Pro)
- Platform UI in 25+ languages with regional community managers
- Film school curriculum integration (USC, NYU, AFI, international equivalents)
- Revenue sustainability target: platform self-funding from fees by end of 2028

**Vision target by 2029:** 50,000+ active creators, 500+ completed films, $10M+ in annual creator earnings distributed transparently via smart contracts.

---

### 10.5 Technical Milestone Timeline

| Milestone | Target Date | Status |
|-----------|-------------|--------|
| Whitepaper and documentation | Q1 2026 | Complete |
| Testnet smart contracts | Q2 2026 | Upcoming |
| Discord bot v0.1 | Q2 2026 | Upcoming |
| Standard Library Spec v1.0 | Q2 2026 | Upcoming |
| Pilot film "Genesis" kickoff | Q2 2026 | Upcoming |
| Discord bot v0.5 | Q3 2026 | Planned |
| Pilot film "Genesis" complete | Q3 2026 | Planned |
| Security audit complete | Q3 2026 | Planned |
| Mainnet deployment (Base) | Q4 2026 | Planned |
| Web dashboard v1 | Q4 2026 | Planned |
| Discord bot v1.0 | Q4 2026 | Planned |
| $MONTAGE token launch (IDO) | Q1 2027 | Planned |
| Full DAO governance | Q2 2027 | Planned |
| Creator marketplace | Q2 2027 | Planned |
| Mobile app beta | Q3 2027 | Planned |
| Cross-film Standard Library marketplace | Q4 2027 | Planned |

---

### 10.6 Key Risks and Mitigations

| Risk | Mitigation |
|------|------------|
| **Chicken-and-egg**: No films without creators, no creators without films | Produce Pilot Film "Genesis" with 50 hand-selected invite-only creators before opening publicly. Phase 1 contributors receive $MONTAGE test token airdrops, creating early loyalty. |
| **AI tool costs**: Video generation is expensive ($5–$20/shot in API fees) | Start with community-provided compute (local GPU via ComfyUI). Negotiate subsidized API access with Runway, Pika, Kling. Explore compute credit system funded by Film Ticket NFT sales. |
| **Legal and IP uncertainty**: AI-generated content ownership is legally evolving | Contributor attestation system. Partner with IP attorneys specializing in AI content. Implement DMCA procedures. On-chain provenance trail provides stronger attribution than traditional filmmaking. |
| **Blockchain scalability**: Base congestion or ecosystem issues | Architecture designed for L2 portability. Arbitrum One designated as fallback chain. Multi-chain deployment a Phase 3+ option. |
| **Quality control at scale**: Public launch may flood platform with low-quality submissions | Automated quality floors filter non-compliant submissions. Phase 2 AI consistency scoring adds a pre-screen layer. Quadratic backing ensures broad consensus outweighs capital concentration. |
| **Token price volatility**: $MONTAGE volatility could destabilize incentives | Creator earnings denominated in USDC, not $MONTAGE. $MONTAGE governs the platform, not clip selection. Vesting schedules prevent early dump pressure (only ~8% circulating at TGE). |

---

## 11. Getting Started Guide

### 11.1 Joining Discord

1. Navigate to [discord.gg/SS8BdjYH6W](https://discord.gg/SS8BdjYH6W) and join the server
2. Read `#announcements` to understand the current platform status
3. Introduce yourself in `#introductions` — mention your background, tools you use, and what interests you about the project

### 11.2 Wallet Verification (Collab.Land)

1. Go to `#verify-wallet` in the COMMUNITY category
2. The Collab.Land bot will prompt you to connect your wallet
3. Supported wallets: MetaMask, WalletConnect, Coinbase Wallet, Phantom, and others
4. Sign a gasless message to prove wallet ownership (no gas fee required)
5. Collab.Land assigns you the `@Verified-Voter` role automatically
6. MontageBot reads this role to link your Discord identity to your wallet address

The bot never holds your private keys or funds. All on-chain transactions require your explicit wallet signature.

### 11.3 Getting Test Tokens

During Phase 1 (testnet), no real money is required:

1. Go to `#faucet` in the TOKENOMICS category
2. Request testnet USDC for Shot Market backing competition practice
3. Request testnet $MONTAGE for platform governance voting
4. Tokens arrive within a few minutes to your verified wallet

Keep testnet USDC for backing practice — real USDC is required at mainnet launch in Phase 2.

### 11.4 Browsing Active Films

1. Go to `#film-listings` to see all active film projects
2. Each listing shows: logline, genre, shots available, Standard Library quality, and revenue split
3. Click through to a film's `#film-overview` for the full script and style direction
4. Download a Standard Library for any film you want to explore: `/standard-library film:[id]`
5. Ask questions in the film's `#director-chat` before committing to work

### 11.5 Role-Specific Onboarding

**For Directors:**
1. Verify wallet and self-assign `@Director` in `#role-select`
2. Read Section 4 of this manual in full before creating a film
3. Review `#standard-library-workshop` to understand what makes a quality library
4. When ready, run `/create-film` and follow the wizard
5. Announce your project in `#film-listings` with a compelling pitch and sample generations

**For Creators (Video Segment Contributors):**
1. Verify wallet and self-assign `@Creator` in `#role-select`
2. Browse `#film-listings` and pick a film that matches your skills and tools
3. Download the Standard Library and read the style guide carefully
4. Claim a short, simple shot for your first submission to learn the workflow
5. Post your result in `#showcase` once it merges

**For Backers:**
1. Verify wallet and self-assign `@Backer` in `#role-select`
2. Load some testnet USDC from `#faucet`
3. Browse `#shot-markets` in any active film to find open markets
4. Watch all competing clips carefully before committing
5. Read Section 6 of this manual for full backer strategy

**For Developers:**
1. Verify wallet and self-assign `@Developer` in `#role-select`
2. Watch `#announcements` for developer calls and contributor onboarding posts
3. Review the [Operations Manual](operations.md) (Section 3: Bot Command Reference) for bot command specs and server architecture
4. Review the [Technical Specification](technical-spec.md) for complete smart contract interfaces and architecture
5. Join `#dev-chat` for technical discussion and coordination

---

## 12. Discord to Web Transition Plan

### 12.1 Transition Trigger Conditions

The Discord-first approach is designed for Phase 1 (Q2-Q3 2026). The web dashboard launches in Phase 2 (Q4 2026) when all three conditions are met:

1. The platform has validated core workflows with 3+ completed pilot films
2. User feedback consistently identifies Discord's limitations (complex dashboards, portfolio views, side-by-side video comparison, video playback quality)
3. The team has sufficient usage pattern data to design an optimal web UX

The transition is driven by user need, not timeline. Discord remains the primary community interface regardless of web dashboard status.

### 12.2 What Moves to Web

| Feature | Discord Phase 1 | Web Dashboard Phase 2+ |
|---------|-----------------|------------------------|
| Film browsing and discovery | `#film-listings` text embeds | Rich catalog with filters, search, thumbnails, preview clips |
| Shot claiming | `/claim-shot` command | Visual shot board (kanban-style) with shot preview |
| Video submission | `/submit` + URL | Drag-and-drop upload with inline video preview |
| Shot Market Menu Auction | `/back-shot`, `/back-shot-menu`, `/reveal-backing`, `/claim-market` | Side-by-side clip comparison viewer with integrated valuation menu UI |
| Earnings tracking | `/my-earnings` DM | Real-time dashboard with revenue charts and payout history |
| Film playback | External video links | Integrated player with segment-by-segment navigation and creator credits |
| Standard Library | File download links | Visual asset browser with version diffing and changelogs |
| NFT management | Basic text display | Full portfolio view with marketplace integration |

### 12.3 What Stays on Discord

Discord remains the community and notification layer permanently after the web dashboard launches:

- **Notifications**: Bot pings for new films, market results, earnings milestones, shot replacements
- **Quick actions**: `/claim-shot`, `/back-shot`, `/back-shot-menu`, `/my-earnings` continue working for convenience and mobile users
- **Community discussion**: `#general`, `#tools-help`, `#ai-tools-discussion`, `#director-chat` channels
- **Voice channels**: Creator calls, director AMAs, community events
- **Governance discussion**: Proposal debates before formal on-chain votes

No user is forced to migrate to the web dashboard. Power users can use web for complex tasks while continuing to receive notifications and take quick actions through Discord.

### 12.4 Technical Integration

The web dashboard and Discord bot share the same backend API and database. Actions taken on either interface are reflected on both in real time:

- Claim a shot on web → `#shots-available` updates on Discord
- Submit via Discord command → clip appears in web dashboard immediately
- Submit valuation menu on web → commitment count updates on the Discord embed
- Merge event on-chain → Discord notification + web dashboard update simultaneously

This dual-interface approach ensures continuity during the transition period and long-term for users who prefer Discord for quick actions.

The wallet linkage established during Phase 1 via Collab.Land carries forward — the same wallet address connects to both Discord and the web dashboard without re-verification.

---

**Version**: 2.0
**Last Updated**: 2026-03-05
**Authors**: OpenMontage Core Team
