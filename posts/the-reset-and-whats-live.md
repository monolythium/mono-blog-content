---
title: "The reset, and what's live this week"
date: 2026-05-26
excerpt: "Quiet weeks. Here is what we tore down, what we rebuilt, what goes live to the public this week — testnet, wallets, explorer, SDK, docs — and what we are explicitly not doing, across consensus, execution, cryptography, clusters, privacy, liquidity, and agent commerce."
cover: "/blog-covers/monolythium-007.png"
category: Ecosystem
author: Monolythium Foundation
draft: false
---

It has been about six weeks since the last post here. That silence was not abandonment — it was rebuilding. We have torn down the previous Monolythium app-chain and built a different protocol from the ground up, on different consensus, different execution, and different cryptography. This post is the long answer to a question we have heard several times in DMs: *what is Monolythium now, and what is actually live?*

The short version: a Rust/RISC-V-native settlement layer for the autonomous economy, with deterministic finality, post-quantum signatures at the wire, operator clusters that work like a marketplace rather than a stake-weighted auction, a plaintext mempool with deterministic ordering, native MRC asset standards, native agent-commerce primitives, and a hardware sovereignty story we genuinely believe is the right floor for institutional-grade nodes. The public testnet is opening, the browser wallet and explorer are public, and the signed desktop/operator release tracks are gated by the evidence described below. Fifteen repositories are public today. The whitepaper is published. The docs site is open. The Genesis Liquidity Program is running.

The long version follows. Skip ahead to whichever pillar you care about.

## What we tore down (and what we kept)

On 2026-04-28 we decommissioned the predecessor app-chain — Sprintnet, the previous EVM-bridged surface, the launchpad, the old explorer, the previous validator software. Frozen, archived, replaced. Anyone who participated in the v1 genesis program kept their LYTH allocation at their original terms: 32,781,503.90 LYTH carries forward into the v2 launch, locked to the exact addresses and amounts the v1 cohort earned. That ledger sits in a public reference and does not move. We do not get to revisit it. Existing buyers are existing buyers.

What replaced it is genuinely different. Not an upgrade, not a fork — a rebuild. The new architecture's binding constraints came out of two years of operating the predecessor and noticing where the design hit a ceiling: execution throughput capped by the EVM gas model; cryptographic agility blocked by the absence of post-quantum primitives in the consensus path; validator economics that rewarded capital concentration rather than operator skill; governance theatre that produced very little real governance. The v1 architecture could have been patched indefinitely. We chose not to.

## The thesis

Monolythium is a **settlement layer for the autonomous economy**. The premise: in the next decade, a significant share of transactions will be initiated by software agents acting on behalf of humans and other agents. Those agents need a chain whose primitives — identity, consent, escrow, reputation, spending policy, attestation — are native to the protocol rather than reimplemented as application contracts on every chain that wants to host them.

Three structural commitments fall out of that thesis, and they explain almost everything that follows.

**Post-quantum by default.** When an autonomous agent signs a transaction with a key whose corresponding public key is recorded on a chain that will exist for decades, a future quantum break turns that signature into a strict liability for the entire chain history. We settled this now rather than promise a future migration. Every signature accepted at the wire is **ML-DSA-65 (NIST FIPS-204)**. Classical signatures (Ed25519, secp256k1) are rejected by the mempool. SLH-DSA (FIPS-205) is the pre-registered break-glass backup if the lattice family is ever broken.

**Determinism over throughput.** Consensus is **Starfish-C**, an uncertified DAG-BFT design from the IOTA research group (paper #44, 2025) with formally proven 20× Byzantine resilience over Mysticeti and linear O(Mn) payload — meaning the protocol's bandwidth cost grows linearly with the number of validators, not quadratically. Finality is **3-second deterministic**: not "probabilistic with checkpoints", not "two-block finality with reorgs possible", just *final*. We salvaged about half of an earlier consensus implementation effort and ported the rest to Starfish-C when the paper landed.

**Operator marketplace, not stake-weighted auction.** The active validator set is **100 clusters of 7–10 operators each, advancing with a 7-of-10 quorum** — roughly 700+ operator slots. Operators do not bid stake to enter; they join clusters through a swap mechanism with bounded slot mobility (one move per operator per seven days, 3-epoch notice). The self-bond is purely slashable skin-in-the-game (5,000 LYTH on the floor), not a competitive auction price. This is the "Avengers assembly" model — clusters form around complementary operators, and the protocol enforces the quorum rather than the wealth ranking.

## Consensus: Starfish-C, in plain English

DAG consensus protocols let validators commit transactions in parallel rather than serially through a single proposer. Most production DAG-BFT designs require **certificates** — a validator's payload only enters the DAG once a quorum signs off on it — which dramatically increases bandwidth and latency. Starfish-C is the most rigorously proven *uncertified* DAG, meaning payloads enter the DAG immediately and certification happens on a separate pipeline. The result is lower latency under Byzantine conditions and substantially lower bandwidth per validator.

In practice, this means: an anchor (Monolythium's term for a finality unit; equivalent to a block in legacy chain terminology) commits every three seconds, deterministically, and the operator cluster behind that anchor signed it with a 7-of-10 ML-DSA-65 signature bitmap. There is no probabilistic confirmation; once an anchor is committed, it is final. We retire the term "block" only at user-facing surfaces; the JSON-RPC `eth_blockNumber` remains as a compatibility alias.

Reed-Solomon shard dissemination distributes payloads across the validator set without requiring every validator to download every shard. Each operator signs independently with its own ML-DSA-65 key; an anchor commits once a 7-of-10 bitmap of operator signatures is collected.

## Execution: Rust to RISC-V, no EVM at the wire

Monolythium does not execute EVM bytecode at launch. Contracts are written in Rust and compiled to deterministic RISC-V artifacts with cycle metering at execution time. There is no per-block gas ceiling that scales with bytecode-vs-storage tradeoffs the way Ethereum's does; there is a cycle budget and a state-I/O budget, each priced separately and visible to the contract author.

Hot paths — token standards, NFT standards, multi-token, vault, spot order book, name registry, agent-commerce primitives — live as audited **native modules** rather than as user-deployed contracts. Native modules sit behind precompile addresses and use the same calling conventions as user contracts; the difference is that the protocol owns and audits them.

This gives us native standards: **MRC-20** (fungible), **MRC-721** (non-fungible), **MRC-1155** (multi-token), **MRC-4626** (vault), plus smart-account and policy-account interfaces. A new token is a registration into the MRC-20 module, not a freshly deployed contract — one ABI for all tokens, one address space to track, no per-contract security surface to audit individually.

EVM liquidity reaches Monolythium through external interop providers, not through native execution. The chain accepts Ethereum-tooling-compatible RPC reads (`eth_chainId`, `eth_blockNumber`) so that exchanges with existing integrations can list LYTH without retooling; the actual settlement happens in the native lane.

## Cryptography: post-quantum at the wire, typed addresses, BLAKE3

Every transaction signature is **ML-DSA-65**. The wallet generates ML-DSA-65 keys, the mempool rejects classical signatures, the receipt includes the ML-DSA-65 verification step. Production HSMs from Thales (Luna firmware 7.9+), Entrust (nShield 13.8.0+), and AWS KMS have shipped FIPS-204 support since mid-2025, so institutional custodians who want to integrate Monolythium directly already have the hardware. For custody platforms that route through Fireblocks/BitGo/Anchorage MPC stacks, the per-platform PQ adapter is the pacing item; we ship a Mesh/Rosetta server with a secp256k1 boundary as a documented bridge until those platforms are ready.

Addresses are **20-byte BLAKE3(public_key)** rendered as **bech32m** with a per-type HRP discriminator. A user address starts with `mono`, a smart-account starts with `monos`, a contract starts with `monoc`, a cluster identity starts with `monok`, a multi-sig starts with `monom`, an externally-bridged asset starts with `monox`. The per-type HRP is not cosmetic — the chain rejects a transfer that targets a `monoc` address from a wallet sending to a user-EOA, because the address class does not match. Address-class confusion attacks are structurally impossible.

Bech32m has built-in checksum-level error detection. Typos in addresses fail at the wallet, not silently in the mempool. There is no hex `0x…` form at any user-facing surface; that representation is reserved for legacy ETH-tooling compat reads and not promoted in any product UI.

Human-readable names sit on top: register `you.mono` or `your-org.mono` once, send to it forever. The name registry is a native module, not an application-layer ENS clone, with hierarchical sub-name delegation and a U-curve pricing model that discourages domain squatting.

## Operator clusters and the home-operator path

A Monolythium validator slot is a *cluster seat*, not a single-key validator. The 100 clusters that run mainnet each host 7–10 operators; consensus advances when 7 of those operators sign. A single operator going offline does not stall the cluster; a 100% slash for double-signing burns the operator's self-bond and exiles the operator identity permanently — but the cluster keeps going with the remaining 6–9 members.

This unlocks two things that ordinary single-key validators cannot offer.

First, **distributed validator technology**: operators in a cluster do not share a single private key — each operator holds its own independent ML-DSA-65 key, and an anchor only commits when 7 of the 10 operators in the cluster sign it. Compromising one operator does not compromise the cluster. Coordinated attacks require coordinating across 7+ independent operators in 7+ independent infrastructure environments.

Second, **the operator marketplace**. Clusters form around complementary operators rather than the highest stake-bidders. Operators advertise capabilities — geographic region, hardware profile, archive depth, RPC service tiers — and clusters select operators whose mix gives the cluster the diversity bonus that the protocol rewards. Operators move between clusters through bounded slot swaps. There is one active slot per operator at a time, plus up to two standby seats.

**Home operators are explicit in scope.** Our hardware reference is the Hetzner CCX33 class for foundation-grade infrastructure, but the protocol does not require institutional-grade hardware. The **full home-operator runbook** lands in the coming weeks: hardware shopping list, ISP/network considerations, key-management posture, the cluster-application flow, the bonding-and-grant mechanics for operators who want to participate but cannot front the self-bond themselves. We mean it when we say a serious individual operator with the right setup is a valid validator on this chain. Foundation grants cover bonding for promising operators who lack the up-front capital. The bond is slashable skin-in-the-game, not a paywall.

## Privacy: bifurcated denomination, plaintext mempool

Two parallel surfaces live on the chain: the **public denomination** (the normal LYTH everyone is used to) and the **private denomination** (opt-in non-fungible private tokens). The two denominations are not interchangeable. A user moves value from public to private explicitly, and once value is in the private denomination it can **only transfer or burn** — it cannot bridge to other chains, cannot be wrapped, cannot leak to a public surface. Cross-chain leakage of private balances is impossible by construction, not by policy.

The mempool is **plaintext**. The earlier LythiumSeal encrypted-mempool design was removed in v2 — transactions are submitted and gossiped in the clear, and inclusion order is fixed deterministically by the committed consensus DAG (a linearizer plus a greedy-tip bundle builder), not by a sealed-until-inclusion lane. Dedicated fair-ordering refinements against front-running and sandwich extraction are tracked as a later surface, not live at genesis.

## Liquidity: external interop, risk-visible

External liquidity reaches Monolythium through external interop providers, cross-chain swaps, and issuer-supported integrations rather than through native compatibility execution. Monolythium does not ship an in-tree bridge module — interop is an external provider integration, and the provider runs the route and carries its verification. The posture is deliberately vendor-neutral: a route's verifier model, drain cap, circuit-breaker state, insurance pool, and last incident date are surfaced to every wallet and indexer as advisory trust-disclosure metadata before the user signs.

The route-safety expectations come out of a careful read of historical bridge incidents across the industry: drain caps, circuit breakers, no post-deploy admin-key config path, trust-disclosure metadata, an insurance pool, and SDK-native route selection. We treat those incidents as shared lessons rather than as competitive material, and surface the same disclosure fields uniformly for every route so integrators can compare providers on a like-for-like basis.

Route cooldowns are provider-agnostic and reported per route by finality, verifier maturity, drain cap, circuit-breaker posture, and audit evidence. None of this is a comment on any single provider; the public promise is that route risk is made explicit and comparable through the disclosure surface.

## Agent commerce: eight native primitives

This is the part of the chain that exists nowhere else, and the reason we treat the agent-native framing as the binding constraint.

Eight native modules implement the primitives that an autonomous agent needs to transact with another agent or with a human:

1. **Attestation** — a verifiable claim about an identity, signed by a registered issuer
2. **Consent** — a structured "yes" that survives across calls and binds the consenting party
3. **Issuer** — the registry of who can issue what kind of attestation, with reputation
4. **Discovery** — how agents find services, with capability advertisement and rate cards
5. **Availability** — booking primitive, slot reservation with bounded cancellation
6. **Reputation** — outcome-based reputation that survives across counterparties
7. **Escrow-arbiter** — automatic dispute escalation to a registered arbiter
8. **Spending policy** — per-wallet enforceable spending limits, consensus-critical

The eighth — spending policy — is consensus-critical. It is what makes "give an agent a wallet" safe. Limits are enforced at the protocol layer; an agent cannot drain a wallet by ignoring its policy. The mainnet activation order is: attestation → consent → issuer → discovery → reputation → availability → escrow-arbiter → spending-policy (last, mainnet gate).

A counter-offer flow lets agents negotiate at the protocol level rather than off-chain.

## What goes live this week

We have been deep in the testing phase, running the chain on a closed foundation testnet and burning down the wallet, explorer, and SDK punch lists against it. That phase is done. **This week the public testnet `69420` opens to anyone who wants to connect.**

- **Public testnet `69420`** — a foundation-operated operator cluster, four-second deterministic finality, ML-DSA-65 at the wire, a plaintext mempool with deterministic DAG ordering. The TLS-fronted public RPC is at `rpc.monolythium.com`. Chain identity, peers, and explorer endpoints are in the [`chain-registry`](https://github.com/monolythium/chain-registry).
- **The Monolythium Wallet — browser live, desktop and mobile on signed preview tracks.** The browser extension is the public entry point for the current testnet, with PQM-1 24-word mnemonic support, ML-DSA-65 account handling, bech32m address display, native MRC asset support, an EIP-1193 provider, and popup approval flow. Desktop and mobile builds remain release-track surfaces: they are published only as signed artifacts when their vault, hardware-signer, update, and platform-keychain gates pass.
- **Monoscan** — the canonical block explorer at [`monoscan.xyz`](https://monoscan.xyz). Anchors, transactions, accounts, MRC tokens, the name registry, operator clusters — the DAG visualization, native CLOB depth, native module deltas, the agent forensics surface. Self-hostable in about an hour over the same indexer API the official endpoint exposes.
- **Mono Core SDK** — [`mono-core-sdk`](https://github.com/monolythium/mono-core-sdk) in Rust and TypeScript, generated from the same Rust types the chain itself uses. Address helpers, ML-DSA-65 signing, fee formatting (`formatLyth`/`parseLyth` with 18 decimals; the atomic unit is "lythoshi"), MRC token helpers, RPC client, bridge-route disclosure, REST `/api/v1` client, Mesh-interop helpers. The crate ships on crates.io and the TypeScript package ships on npm.
- **Mono Studio** — [`mono-studio`](https://github.com/monolythium/mono-studio), the native builder shell for MRV contracts, MRC assets, wallet approval plans, simulation, and developer workflows. Hosted by the desktop wallet's Studio tab as a sidecar.
- **Stele Desktop** — services marketplace surface integrated into the desktop wallet. Book, escrow, complete, rate; settled on-chain through the agent-commerce primitives. The first concrete consumer application of the agent-commerce stack.

There is no MetaMask compatibility shim and there will not be one — the wire format is post-quantum, the address derivation is different, and a classical-signature wallet has no key material that can sign a valid transaction on this chain. The Monolythium Wallet is the path in.

## Hardware sovereignty: Monarch OS

For the institutional-grade validator posture, we have built **Monarch OS** — a Talos Linux–based, signed-ISO operating system designed exclusively to host Monolythium validator infrastructure. No SSH. No user shell. No multi-user model. No package manager. The kernel is configured to disable subsystems an L1 validator does not need: `AF_ALG=n` (kernel-CVE class evasion for the Copy Fail family), KVM disabled, audio disabled, wireless disabled, USB storage paths locked down. Chain crypto runs in-process Rust against audited PQ libraries, never through kernel crypto sockets.

The threat model is explicit: ordinary Linux validator hosts inherit the entire kernel-CVE surface — `AF_ALG`, KVM-escape, audio-driver, wireless-driver, USB-storage. A serious operator running Monolythium institutionally on a generic Ubuntu host accepts that inheritance. Monarch OS structurally evades that surface by minimizing the kernel and removing the userspace foothold.

The operator console track is **Monarch Desktop** — Tauri 2, native Talos API mTLS client, cluster monitoring, signed operation receipts, and explicit approvals for privileged actions. Its signed release path is gated on Talos identity pinning, live Protocore RPC readiness, operation receipts, and verified two-party chat evidence against a booted Monarch OS artifact. **Monarch Mobile** remains the planned companion for pocket cluster health, alerts, and out-of-band approvals.

Monarch OS is the intended production substrate for tier-1 exchange-operated validators. That path is gated on signed OS artifacts, TPM-bound enrollment, release evidence, and operator runbooks. For home operators, the upcoming runbook covers both the Monarch OS install and the simpler "operator on a hardened Linux host" path — the chain can accept both; the security posture differs.

## License and structure

Mono-core ships under **BSL-1.1** with a 4-year commercial-use restriction converting to a permissive license thereafter. The pattern is the one HashiCorp, CockroachDB, and Elastic adopted for the same structural reason: protect against AWS-style appropriation while the protocol is establishing economic value, then convert to fully permissive once the network effects are durable.

Source for the consensus core ships in the [`protocore`](https://github.com/monolythium/protocore) repository as signed binary releases for public testnet; the source itself opens at mainnet, per the BSL-1.1 commitment. Every surrounding component — SDK, wallets, explorer, MCP server, studio, Monarch tooling, OS, chain-registry — is open source today.

The whitepaper is **CC BY-SA 4.0**. Quote it, adapt it, redistribute it with attribution. Read it at [`monolythium.com/whitepaper`](https://monolythium.com/whitepaper).

Two legal entities run the public surface area: **Mono Labs R&D LLC** (California, San Francisco — US operations and the LYTH sale of record) and the **Monolythium Foundation** (Cayman, the protocol steward, credits issuer, shield-and-bounty steward, and activation oracle for mainnet milestones).

## Economics

The genesis supply is **100,000,000 LYTH**, fixed at genesis. Annual reward issuance is capped at **8%**. Base fees burn. The treasury draws from a genesis reserve rather than an inflation tap; there is no holder-adjustable inflation lever. LYTH precision is **18 decimals**, with the atomic unit named "lythoshi" — `formatLyth` and `parseLyth` from the SDK handle the conversion.

The economic model intentionally avoids the levers that on-chain governance tends to capture: there is no holder vote that can change the inflation rate, no parameter mutation through proposals, no treasury that can be voted-on into the pockets of the most-staked addresses. Treasury is a Foundation multi-sig; fork is the legitimate exit if the community disagrees with treasury decisions.

The **Genesis Liquidity Program** is live at [`monolythium.com/get-lyth`](https://monolythium.com/get-lyth) — the community access offering for LYTH. It is the only sale-of-record path we are running. The v1 cohort's 32,781,503.90 LYTH obligation carries forward into v2 at the original terms; that ledger is settled and does not move. Anything else you see elsewhere is not us.

## Governance

There is no on-chain governance vote. No token-weighted poll. No on-chain parameter mutation by holders. We watched this movie on the previous chain and on every other chain that has tried it. On-chain governance turns into plutocratic theatre, where the loudest staked holders win procedural skirmishes that the Foundation could have just decided in an afternoon with better judgment.

Signal-only off-chain channels exist for community input. The Foundation decides. **Fork is the legitimate exit.** Anyone who disagrees with a Foundation decision strongly enough can take the BSL fork rights, run a different version, and let the market decide which version users prefer. The Linux model — Linus decides, the kernel ships, dissenters fork — has produced the most successful piece of software in history. We think it works for L1s too.

## What we are explicitly not doing

- **No perpetual futures or margin trading**, ever. Spot CLOB is the native market primitive. Perps amplify systemic risk in the foundational phase of a chain's life; they can come later as a separate venue but they will not anchor mainnet.
- **No third-party security audit firms** engaged for the current phase. The internal red-team plus integration coverage is the binding mainnet gate for now. External engagement is on the table when we engage one; it is not a current operational item.
- **No mainnet date promise.** Mainnet ships when the binding rules close: live no-EVM testnet evidence, wallet/explorer downstream proofs, live multi-validator state-root evidence on the production execution layer. Each gate is tracked publicly. The chain ships when it ships.
- **No holder-adjustable inflation, no on-chain protocol vote, no on-chain governance.**
- **No MetaMask compatibility shim.** Wallet is post-quantum native or it is not a Monolythium wallet.

## What is open today

Fifteen public repositories at [`github.com/monolythium`](https://github.com/monolythium):

| Repository | What it is |
|---|---|
| `public-whitepaper` | Source-of-truth whitepaper releases (CC BY-SA 4.0) |
| `chain-registry` | Canonical network metadata, RPC endpoints, peers, explorers |
| `mono-core-sdk` | Official Rust + TypeScript SDK, generated from Rust types |
| `protocore` | Signed binary releases for the consensus core (BSL-1.1; source opens at mainnet) |
| `monoscan` | The canonical explorer |
| `mono-studio` | Native builder shell for MRV contracts, MRC assets, simulation |
| `desktop-wallet` | Monolythium Wallet — Tauri 2 desktop, OS-keychain vault, Ledger signer |
| `browser-wallet` | Monolythium Wallet — MV3 browser extension (Chrome / Firefox / Brave) |
| `mobile-wallet` | Monolythium Wallet — Tauri 2 mobile (iOS + Android) |
| `monarch-desktop` | Operator console for nodes and clusters (Tauri 2 + native Talos mTLS) |
| `monarch-mobile` | Phone companion for operators — pocket cluster view, approvals |
| `monarch-os-talos` | Talos-based immutable node OS for Monolythium operator nodes |
| `lyth_mcp` | MCP server for live-chain reads, agent runbooks, local agent wallets |
| `mono-blog-content` | Markdown source for this blog |
| `.github` | Organization profile and engineering activity dashboard |

The whitepaper is published two ways: rendered at [`monolythium.com/whitepaper`](https://monolythium.com/whitepaper), and served straight from the public source repo at [`monolythium.github.io/public-whitepaper`](https://monolythium.github.io/public-whitepaper/) — same canonical text, different surface. The docs site — operator guides, SDK reference, architecture-decision pages, diligence pages with reproducible `curl` recipes against the live network — is at [`docs.monolythium.com`](https://docs.monolythium.com). The explorer is at [`monoscan.xyz`](https://monoscan.xyz). The Genesis Liquidity Program is at [`monolythium.com/get-lyth`](https://monolythium.com/get-lyth).

## What is opening next

- The **home-operator runbook** — the practical guide to running a Monolythium operator from home, with hardware shopping list, network posture, key management, cluster application flow, and bonding/grant mechanics.
- Continued **wallet, explorer, and SDK iteration** as testnet traffic grows. A public testnet is a stress test of every surface at once, and the issues that show up are the issues we want to find now.
- The **protocore source open** at mainnet, per the BSL-1.1 commitment.

We are not putting calendar dates on the publication slot for any of this. We are putting it out as it ships.

## How to verify everything in this post

Every claim above is independently checkable.

- **Diligence pages** at [`docs.monolythium.com/diligence/`](https://docs.monolythium.com/diligence/) — canonical-facts page, reproducible benchmarks with `curl` recipes, architecture-decisions page with rationale for each call, license-and-IP page, public-repositories index.
- **Live chain identity** — `curl https://rpc.monolythium.com -d '{"jsonrpc":"2.0","id":1,"method":"lyth_chainIdentity","params":[]}'` returns the chain id and genesis hash.
- **Live pool status** for the Genesis Liquidity Program — verifiable directly against the public API.
- **Public engineering activity** — aggregate commit-counts across every repository, updated nightly, at [`monolythium.com/github`](https://monolythium.com/github).
- **Source-of-truth whitepaper** at [`monolythium.com/whitepaper`](https://monolythium.com/whitepaper) — the full protocol specification.

## Closing

We did not pivot for fashion. We did not rebuild because the previous architecture was broken. We rebuilt because the architecture had a ceiling and we wanted to build past it. Settlement infrastructure for the autonomous economy is a different design problem than "build another general-purpose L1," and a chain that wants to host autonomous agents needs primitives that exist nowhere else today.

That work takes time. Most of it is now visible. The rest is shipping in the coming weeks.

If you are an engineer, the SDK is open. The docs are open. The testnet is live. Build something.

If you are a user, install the wallet, request testnet LYTH from the faucet, send a transaction, browse it on Monoscan. Five minutes end-to-end.

If you are an operator, the home-operator runbook is days away. Get the hardware. Apply.

If you want a position before launch, the Genesis Liquidity Program is at [`/get-lyth`](https://monolythium.com/get-lyth).

If you are a researcher, the whitepaper is the full reference. The decisions and their rationale are on the diligence pages. Read it.

A word of thanks. To everyone who has supported us through the rebuild, to the operators who kept Sprintnet validators running while the new chain was still being assembled in the background, and to the community that waited through the quiet weeks without an explanation — thank you. The patience was load-bearing. The reset only worked because you let us take the time to do it properly, and we do not take that for granted.

— Monolythium Foundation

---

**Find us**

- Website — [monolythium.com](https://monolythium.com)
- Whitepaper — [monolythium.com/whitepaper](https://monolythium.com/whitepaper) · source at [monolythium.github.io/public-whitepaper](https://monolythium.github.io/public-whitepaper/)
- Docs — [docs.monolythium.com](https://docs.monolythium.com)
- Explorer — [monoscan.xyz](https://monoscan.xyz)
- Genesis Liquidity Program — [monolythium.com/get-lyth](https://monolythium.com/get-lyth)
- GitHub — [github.com/monolythium](https://github.com/monolythium)

**Community**

- X / Twitter — [@monolythium](https://x.com/monolythium)
- Discord — [discord.gg/monolythium](https://discord.gg/monolythium)
- Telegram announcements — [t.me/mono_announcements](https://t.me/mono_announcements)
- Telegram community — [t.me/monolythium](https://t.me/monolythium)

[**→ Share this on X**](https://twitter.com/intent/tweet?text=The%20reset%2C%20and%20what%27s%20live%20this%20week%20%E2%80%94%20testnet%2C%20wallets%2C%20explorer%2C%20SDK%2C%20docs%20from%20%40monolythium&url=https%3A%2F%2Fmonolythium.com%2Fblog%2Fthe-reset-and-whats-live%2F)
