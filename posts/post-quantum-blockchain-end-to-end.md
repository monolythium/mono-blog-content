---
title: "Post-Quantum Blockchain, End to End"
date: 2026-06-04
excerpt: "A corrected, dated engineering snapshot of Monolythium's post-quantum design and the evidence still required before launch."
cover: "/blog-covers/post-quantum-end-to-end.png"
coverAlt: "Status-caveated Monolythium engineering cover: ML-DSA-65 profile, ML-KEM-768 evaluation, transport under review, and no whole-stack claim."
category: Engineering
author: Monolythium Foundation
tags:
  - post-quantum cryptography
  - quantum-resistant blockchain
  - ML-DSA-65
  - ML-KEM-768
  - lattice cryptography
draft: false
---

> **Corrected status — 2026-07-16.** The original June article overstated deployed capability and described the retired PQM-1 wallet derivation. It has been replaced because recovery instructions and network-status claims must never remain ambiguous. This page is a dated engineering note, not launch or audit evidence.

## What the project targets

Monolythium's protocol design uses **ML-DSA-65 (FIPS 204)** for account and operator signatures and evaluates **ML-KEM-768 (FIPS 203)** for transport key establishment. The design goal is to remove classical asymmetric cryptography from security-critical protocol paths without pretending that naming standardized primitives proves a secure composition.

The browser wallet uses a standard **BIP-39 mnemonic-to-ML-DSA-65** recovery path and an **Argon2id/XChaCha20-Poly1305 encrypted extension-local vault**. PQM-1 is removed. Never paste a recovery phrase into Stele, a hosted service, a support chat, or an AI tool.

## What is not established by this page

- The public development RPC is reachable, but repeated checks on 16 July 2026 remained at height 74,907 with a latest timestamp of 8 July 2026. Reachability is not liveness, cadence, or finality evidence.
- No mainnet is live.
- No external protocol or cryptographic audit is claimed.
- A post-quantum primitive does not by itself prove end-to-end post-quantum security. Consensus, transaction admission, wallet derivation, transport, dependencies, recovery, and upgrade behavior require separate evidence.
- Browser-wallet release status must be verified from its signed release channel; source availability is not the same as an installed, reviewed release.

## Verify the current state

- [Diligence and observed network status](/diligence)
- [Current whitepaper](/whitepaper)
- [Browser wallet source and releases](https://github.com/monolythium/browser-wallet)
- [Chain registry](https://github.com/monolythium/chain-registry)

This correction intentionally removes the former implementation tour. Detailed cryptographic claims belong in versioned specifications and reproducible tests, not an undated marketing assertion.
