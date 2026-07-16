---
title: "Browser wallet preview and faucet announcement"
date: 2026-06-01
excerpt: "A corrected archive of the June browser-wallet preview. Verify current release, faucet, and network status before use."
cover: "/blog-covers/monolythium-012.png"
category: Ecosystem
tags: ["browser wallet", "development network", "faucet"]
author: Monolythium Foundation
archive: corrected-history
draft: false
---

> **Corrected status — 2026-07-16.** This post announced a June preview. It is not evidence that a particular store build, faucet, or development network is available today. The public RPC is currently reachable but stale. Verify the signed release and current service status before use.

## Current wallet boundary

The browser wallet is the intended identity and signing surface for browser applications such as Stele. It uses standard **BIP-39-to-ML-DSA-65 recovery** and an **Argon2id/XChaCha20-Poly1305 encrypted extension-local vault**. The retired PQM-1 derivation is not supported.

The wallet—not Stele, the Stele API, the hosted MCP, or an AI assistant—holds keys and presents transaction approvals. Never enter a mnemonic into a website, support message, MCP configuration, or AI conversation.

## Verify before installing or testing

- Inspect source, release notes, signatures, and currently published artifacts at [`monolythium/browser-wallet`](https://github.com/monolythium/browser-wallet).
- Treat chain id `69420` as a development identity, not economic settlement. Repeated public checks on 16 July 2026 remained at height 74,907 with a latest timestamp of 8 July 2026.
- Confirm any faucet through current official documentation. Test tokens have no economic value, and an old bot link is not proof that a service is still operated.
- Mainnet is not live.

The original installation and faucet instructions were removed because an archived article must not function as current operational guidance.
