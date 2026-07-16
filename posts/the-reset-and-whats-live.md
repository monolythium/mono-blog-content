---
title: "The reset, and what was proposed in May 2026"
date: 2026-05-26
excerpt: "A corrected archive of the May 2026 reset announcement. Use current status and diligence pages for present capabilities."
cover: "/blog-covers/monolythium-007.png"
category: Ecosystem
author: Monolythium Foundation
archive: corrected-history
draft: false
---

> **ARCHIVED · CORRECTED 2026-07-16.** The original article mixed intended architecture with current capability and used time-sensitive “live this week” language. Its body has been elided so search engines and agents do not treat a May snapshot as present network, wallet, bridge, privacy, economics, or agent-commerce state.

## What remains true about the reset

The predecessor v1 app-chain was decommissioned on 28 April 2026. The replacement project targets a Rust/RISC-V-native Layer 1, Starfish-C DAG-BFT, ML-DSA-65 protocol signatures, DVT operator clusters, MRC assets, and capability-by-capability agent commerce. Those are architectural and product targets unless a current source, release, runtime probe, or activation record establishes otherwise.

The registered development identity is chain id `69420`. The public RPC is reachable but stale: repeated checks on 16 July 2026 remained at height 74,907 with a latest timestamp of 8 July 2026. It does not currently demonstrate advancement, cadence, finality, operator participation, or economic readiness. Mainnet chain id `69422` is reserved; no mainnet is live.

## Stele's corrected boundary

Stele's product boundary is a standalone web application. Browser-wallet connection supplies user identity and is the only intended hosted signing boundary. Application publication, non-economic discovery, private draft workflows, listing writes, escrow, arbitration, reviews, agent spending policy, and mainnet execution remain independently release-gated.

The hosted MCP is keyless and exposes exactly two non-economic tools: `stele_search_services` and `stele_create_booking_draft`. It cannot sign or hold wallets. The separately installed local Stele MCP exposes exactly three read/status tools. Wallet creation and transaction construction belong to explicitly installed wallet/SDK tooling, with user-visible approval before signing.

Desktop embedding is being retired; an older or unreconciled desktop build may still show a disabled legacy Stele surface until removal ships. No recovery phrase belongs in Stele, MCP configuration, a hosted service, or an AI conversation.

## Current sources of truth

- [Stele product and status](/stele)
- [Network diligence](/diligence)
- [Current whitepaper](/whitepaper)
- [Documentation](https://docs.monolythium.com/)
- [Public repositories](https://github.com/monolythium)

This URL is preserved for historical links. The removed body is not a current specification or release statement.
