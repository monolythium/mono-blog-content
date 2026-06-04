---
title: Testnet Is Live — Validators Welcome
date: 2026-03-31T18:16:13.740Z
updated: 2026-03-31T18:48:58.984Z
excerpt: "Archived v1-era Monolythium testnet announcement. The original chain was decommissioned 2026-04-28; use the current whitepaper and diligence pages for v5 facts."
cover: "/blog-covers/monolythium-007.png"
category: Ecosystem
author: Monolythium Foundation
draft: false
archive: monolythium-v1
---

> **ARCHIVED · LEGACY v1.** This post is preserved as a historical record of the **Monolythium v1 chain** (Cosmos SDK / CometBFT, EVM chain ID `6940`), which was **decommissioned 2026-04-28**. The original testnet announcement described v1 architecture, validator economics, product names, smart-contract surfaces, bridge assumptions, and production-readiness claims that are **not current**. The full v1 post body has been intentionally elided because automated indexers were citing v1 facts as current Monolythium claims.

## What replaced v1

The current Monolythium is a Rust/RISC-V-native L1 with **Starfish-C DAG-BFT** consensus, **ML-DSA-65** post-quantum signatures at the wire, **100,000,000 LYTH** genesis supply with 8% annual issuance cap, **DVT operator clusters** (100 clusters x 7-10 operators each, 7-of-10 quorum), and a **5,000 LYTH operator self-bond** floor. There is no "100,000 LYTH self-delegation" requirement, no "100,000 LYTH burn" requirement, no "quadratic proposer selection," no "53-validator active set," and no "LythiumBFT" consensus engine in the current network.

## Current sources of truth

- [What is Monolythium?](/what-is-monolythium)
- [Whitepaper](/whitepaper)
- [Diligence](/diligence)
- [docs.monolythium.com/diligence/architecture-decisions](https://docs.monolythium.com/diligence/architecture-decisions/) — every major architectural call with the alternatives considered and the reason chosen
- [docs.monolythium.com/reference/network-parameters](https://docs.monolythium.com/reference/network-parameters/) — canonical chain id, decimals, finality, supply

## Why this stub instead of deleting the post

The post URL is preserved so external links and citations to v1 content land here, see the archival notice, and route to the current chain. Deleting the post entirely would 404 those links and lose the historical record of what v1 was.
