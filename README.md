# OpenMontage

<p align="center">
  <img src="logo.jpg" alt="OpenMontage Logo" width="200"/>
</p>

**Git for Movies — Collaborative AI Filmmaking**

![Status](https://img.shields.io/badge/Status-Pre--Alpha-orange)
![License](https://img.shields.io/badge/License-Apache%202.0-blue)
[![Discord](https://img.shields.io/badge/Discord-Join%20Us-7289DA?logo=discord&logoColor=white)](https://discord.gg/SS8BdjYH6W)
[![Twitter](https://img.shields.io/badge/Twitter-@OpenMontage-1DA1F2?logo=twitter&logoColor=white)](https://twitter.com/openmontage)

> Anyone can contribute a scene to a film. The best version wins. Everyone earns.

---

## The Problem

AI video tools have made it possible for anyone to generate cinematic footage — but there's no way to combine work from hundreds of creators into a single, coherent film. Different creators produce incompatible styles, there's no economic incentive to contribute, and once a film ships, it can never improve. The result: incredible individual clips, but no collaborative films.

## Our Solution

- **Standard Library** — Every film ships a dependency package (character LoRAs, style guides, voice models) so all contributors produce visually consistent output
- **Pull Request workflow** — Creators claim shots, generate segments with AI tools, and submit for community review
- **Shot Market Menu Auctions** — Backers submit sealed valuation menus across all competing clips per shot; highest quadratic-weighted SelectionWeight wins — broad community support beats concentrated capital
- **Merit-based earnings** — 75% of revenue goes to creators, split by screen time. Your clip earns as long as it's in the final cut
- **Living films** — Better segments can replace older ones. Films improve as AI technology advances

## How It Works

1. **Director** uploads a script, decomposes it into individual shots, and builds the Standard Library
2. **Creators** claim shots and generate video segments using AI tools (Sora, Runway, Kling, ComfyUI) with the film's Standard Library
3. **Community** backs clips in Shot Market Menu Auctions (sealed-bid commit-reveal) to select the best version of each shot
4. **Platform** assembles the winning segments into a seamless film
5. **Revenue** splits automatically: 75% creators / 10% Film NFT holder / 5% Standard Library contributors / 10% backers

Platform launches on Discord — [join to start contributing](https://discord.gg/SS8BdjYH6W).

## Current Status

**Pre-Alpha** | Phase 1 Planning | Q2 2026 Launch Target

We're actively building: smart contracts on Base L2, Discord bot (MontageBot), and recruiting pilot film creators for "Genesis" — a 100-shot sci-fi short film.

---

## Quick Start

### As a Director
Join Discord → `/create-film` → upload your script → build your Standard Library → launch

### As a Creator (AI Video Artist)
Join Discord → browse `#film-listings` → `/claim-shot` → generate with Standard Library → `/submit` → earn revenue

### As a Backer
Join Discord → browse Shot Markets → `/back-shot` (rank-based) or `/back-shot-menu` (explicit valuations) → `/reveal-backing` → earn from competition rewards and 10% of film streaming revenue

---

## Documentation

Start with the **Whitepaper** for the full vision, then dive into the specialized documents for your area of interest.

| Document | Description |
|----------|-------------|
| [Whitepaper](doc/whitepaper.md) | Full vision, architecture, economic model, governance — the authoritative overview |
| [Operations Manual](doc/operations.md) | Discord server, bot commands, director/creator/backer workflows, roadmap, onboarding |
| [Technical Specification](doc/technical-spec.md) | Smart contracts, storage architecture, API design, security, for developers |
| [Economics & Governance](doc/economics-governance.md) | NFT economics, Shot Markets, revenue distribution, $MONTAGE token, governance framework |

## Community

- **Discord**: [discord.gg/SS8BdjYH6W](https://discord.gg/SS8BdjYH6W)
- **Twitter**: [@OpenMontage](https://twitter.com/openmontage)
- **GitHub**: [github.com/digimads-lab/OpenMontage](https://github.com/digimads-lab/OpenMontage)

New to OpenMontage? Read the [Operations Manual](doc/operations.md) for step-by-step onboarding.

## License

Apache 2.0 — see [LICENSE](LICENSE) for details.
