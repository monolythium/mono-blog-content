---
title: "Putting the network to work"
date: 2026-09-17
excerpt: "Monolythium's replacement public testnet and Adalysta are planned for September as we bring the rebuilt network and its first broader marketplace into public view."
cover: "/blog-covers/putting-the-network-to-work.png"
coverAlt: "Abstract Monolythium network core connecting people, commerce, and products."
category: Ecosystem
tags:
  - Monolythium
  - testnet
  - Adalysta
  - Stele
  - AI commerce
author: Monolythium Foundation
draft: false
---

Monolythium’s next phase is about more than launching a network. It is about putting that network to work.

This month, we are preparing for two releases: Monolythium’s replacement public testnet and the official launch of Adalysta, an AI-native marketplace we have been developing alongside the core chain.

These are distinct milestones with a shared direction: building an AI settlement layer together with products that give people and businesses a reason to use it.

Here is where the core engineering stands, what remains before the testnet goes public, and how Stele and Adalysta fit into the broader ecosystem.

## What’s been going on at Monolythium

### TL;DR

- Monolythium’s replacement public testnet is planned for September 2026, alongside the official release of Adalysta.
- The core network rebuild is largely implemented: Rust-native, Move-free, and designed around post-quantum cryptography, with strict validator agreement rules and reproducible deployment artifacts.
- The remaining network milestones are formal specification and release qualification, followed by private rehearsals and one approved public testnet genesis.
- We are building an AI settlement layer—and the products that give it a purpose. Stele addresses services and agent-assisted agreements. Adalysta opens the door to a much broader ecosystem of everyday commerce.
- This is a testnet milestone, not mainnet. Product releases and blockchain settlement integrations will progress on their own release schedules.

## The network and the ecosystem

Monolythium is being developed on two fronts: the core infrastructure for AI-native commerce, and the products that make that infrastructure useful.

The network rebuild has reached its final specification and qualification stages. Alongside it, we have been developing a broader consumer-facing product: Adalysta.

September brings both workstreams into public view. Here is what has been completed, what remains before the testnet opens, and how the pieces fit together.

## Where the core network stands

Monolythium’s new foundation is Rust-native and Move-free, with fixed validator clusters, strict agreement thresholds, and explicit rules for authority and state changes.

Post-quantum cryptography is central to the design—not an optional feature added to an otherwise unchanged architecture. The objective is to remove classical asymmetric cryptography from security-critical protocol paths, while validating the surrounding system rather than treating the choice of cryptographic primitives as proof of security.

Most of that foundation is now implemented.

The legacy production path has been removed. The old Move-based and classical-cryptography dependency paths are no longer part of the production build, preventing an accidental fallback to the architecture we are replacing.

The network’s operational controls are in place. This includes native fees, protocol governance and authority checks, halt-and-resume controls, and hardened recovery and restart behaviour.

Deployment is reproducible. We have defined a small private engineering configuration and a four-cluster public-style configuration. Genesis and deployment artifacts produce the same bytes from the same inputs, allowing independent verification of what was built.

In four-cluster testing, the network has demonstrated continued progress with one entire cluster unavailable or misbehaving. Invalid configurations—including incorrect keys, network identities, and genesis data—are rejected rather than silently accommodated.

That gives us an implemented foundation to qualify, not just an architecture to describe.

## What remains before the testnet opens

Two substantial steps remain.

### Formal specification

We are completing an executable specification in Lean and checking it against the implementation.

It covers protocol authority, governance, state transitions, and state roots—the commitments that identify the resulting state. Shared test vectors check that the specification and the code agree on both valid and invalid cases.

The important part is keeping them aligned. A specification that describes yesterday’s implementation is not enough. When the code changes, the specification and its checks must move with it.

### Release qualification

One integrated release candidate then goes through the complete qualification process: reproducible binaries, software dependency inventories, documented genesis preparation, and private rehearsals on one cluster and then four.

Once those checks pass, we will approve and publish one replacement public testnet genesis.

Our August update described the ability to upgrade a network while preserving its identity. This release is a separate transition: replacing the earlier development network with the rebuilt foundation, rather than presenting it as an in-place upgrade of that network.

After the replacement testnet’s public genesis is published, the rule is clear: no quiet re-genesis and no silently discarded network history.

## An AI settlement layer needs something to settle

Monolythium is being built as an AI settlement layer: infrastructure intended to support transactions and agreements involving people, businesses, and AI agents, with explicit authority and verifiable outcomes.

But a settlement layer without useful products or economic activity has little purpose.

The opportunity is not simply to make agents capable of submitting transactions. It is to connect that capability to things people actually need: finding a service, agreeing on work, purchasing something, or coordinating a transaction with clear conditions.

That is why product development has continued alongside the chain.

Stele is one part of that direction. It is Monolythium’s standalone services marketplace, designed around services offered by people, businesses, and agents. Its intended workflow connects discovery and agreement preparation with wallet-approved settlement, while keeping signing authority outside the hosted service.

Its published application and its settlement capabilities are separate milestones. The current public experience does not enable economic actions such as escrow and settlement; those remain subject to their own activation requirements.

Stele addresses services and agreements. Adalysta takes the product vision into a broader market.

## Introducing Adalysta

We have been building Adalysta, an AI-native marketplace for goods and services.

Adalysta is designed to make it easier to discover what you need, offer what you have, and connect with the right people and businesses. Its assistant, Ada, is being built into the experience from the beginning, helping users navigate the marketplace and reducing the effort involved in buying, selling, and finding services.

The audience extends well beyond crypto.

We are building for everyday buyers and sellers, independent professionals, local businesses, and people whose interests naturally lead them to discover, collect, create, and trade. That places Adalysta within a much larger commerce ecosystem than a blockchain-specific marketplace alone.

The ambition is not to reproduce a conventional marketplace and place a chatbot beside it. We are developing a more connected experience around discovery, commerce, and AI assistance, with several distinctive capabilities that we will introduce at launch.

We are deliberately keeping the details of those features under wraps for now. The official release will be the first opportunity to see how they come together.

One principle matters throughout: Adalysta must be useful on its own merits. People should not need to understand a blockchain—or arrive with an interest in crypto—to benefit from it.

Over time, the goal is to connect that everyday activity with Monolythium’s trust and settlement infrastructure as the network and its integrations become ready. Product adoption should create a reason to use the infrastructure, rather than the infrastructure being the only reason to use the product.

## Coming this month

Monolythium’s replacement public testnet and Adalysta’s official release are both planned for September 2026. The testnet release remains conditional on completing the specification and qualification work described above.

The network milestone is a public replacement testnet that participants can synchronize with, exercise, and test against the new core implementation. It is not a mainnet launch or a claim that the entire wallet, application, and settlement stack is complete. Test assets have no economic value.

Adalysta’s release is a separate product milestone. Its launch does not mean every planned blockchain integration or agent capability becomes available on day one.

We will publish the next network update when the specification work closes, followed by testnet participation details and Adalysta’s launch announcement.

The next step is to put both the network and the product into people’s hands.
