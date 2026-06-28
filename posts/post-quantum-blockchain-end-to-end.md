---
title: "Post-Quantum Blockchain, End to End"
date: 2026-06-04
excerpt: "Post-quantum cryptography on every layer of a live blockchain: ML-DSA-65 consensus, post-quantum account keys, and ML-KEM-768 transport, explained."
cover: "/blog-covers/post-quantum-end-to-end.png"
coverAlt: "Monolythium post-quantum stack: lattice signatures and lattice transport, behind a shield enclosing the Monolythium mark."
category: Engineering
author: Monolythium Foundation
tags:
  - post-quantum cryptography
  - quantum-resistant blockchain
  - ML-DSA-65
  - ML-KEM-768
  - Dilithium
  - Kyber
  - lattice cryptography
draft: false
---

Most chains that talk about "quantum resistance" mean one of two things: a line on a roadmap, or a single signature scheme swapped in somewhere and called done. We took a different approach. We built post-quantum cryptography into the protocol from the signing surface down, swapped consensus itself over to a lattice signature, and put it on a public testnet. This post walks the whole stack, surface by surface, and it is just as explicit about where the work is finished as about where it is not.

The short version: every cryptographic operation in the protocol is post-quantum. Every signature that decides consensus, finality, account ownership, and operator identity, and the transport key exchange. There is no classical signature anywhere, not on the consensus path and not at the gossip layer, and no classical key exchange or asymmetric fallback either. The one classical artifact left in the whole stack is a libp2p network identifier that performs no cryptographic function, and we name it plainly in the second half, because the honest version of this claim is the stronger one.

## The threat, concretely

There are two different quantum threats, and they break different things.

**Shor's algorithm breaks the signatures.** A cryptographically relevant quantum computer running Shor's algorithm recovers a private key from a public key for every signature scheme in production use today: ECDSA (Bitcoin, Ethereum), Ed25519 (most of the libp2p world), and the pairing-based aggregate signatures most modern BFT chains use for consensus votes and quorum certificates. If a chain's consensus votes and account keys rest on those classical schemes, a Shor-capable adversary can forge a validator's vote, fabricate a quorum certificate, rewrite finalized history, or sign a transaction draining any account. This is not a confidentiality problem. It is a "someone forges finality and steals funds" problem.

**"Harvest now, decrypt later" breaks recorded ciphertext.** Classical key-establishment (X25519, RSA, ElGamal over an elliptic curve) is also Shor-breakable. An adversary who cannot break it today can record encrypted traffic now and decrypt it once a quantum computer exists. Anything you encrypt with a classical KEM and an attacker bothers to store is on a clock.

Two things make "just add post-quantum later" genuinely hard. First, the standardized lattice signatures are bigger and slower — an ML-DSA-65 signature is about 3,309 bytes — and they do not aggregate, so you cannot collapse thousands of votes into one. Swapping a lattice signature into a consensus hot path is not a find-and-replace. Second, the part that is still genuinely unsolved — post-quantum zero-knowledge proof verification on chain — has no production library to pull off a shelf. So "later" tends to mean "never, quietly," unless you treat it as a real engineering project up front.

We treated it as one. Here is the result.

## The stack, surface by surface

### Consensus and finality signatures: ML-DSA-65 (live)

This is the piece most chains defer, and it is the one we are proudest to have shipped. Consensus on Monolythium is a DAG-BFT protocol. Every vertex, every vote, every operator's contribution to a round, and the finality certificate are signed with **ML-DSA-65**, the NIST-standardized lattice signature scheme from FIPS 204 (the standardized form of Dilithium). It is believed resistant to Shor's algorithm: its security rests on the hardness of lattice problems, not on the discrete-log or factoring problems Shor demolishes.

The cluster's round certificate is a 7-of-N threshold bitmap multi-signature — a 7-of-10 quorum, in our testnet topology. Every operator in the cluster signs the cluster's vertex with its **own** independent ML-DSA-65 key, any seven of the cluster's members sign a round individually, and the certificate's operator bitmap records which members signed — bit *i* marks operator *i*. There is no shared signing key and no aggregated signature; the certificate is just the set of individual lattice signatures the bitmap points at. An anchor is final once that 7-of-N bitmap multi-signature is present across a stake-weighted 2f+1 set of clusters. Because only seven signatures are needed, up to N−7 members can be offline and the online members fill the threshold automatically — no promotion ceremony, no key handover, nothing manual. The per-anchor leader-seed beacon that drives randomness is a domain-separated, chain-id-bound BLAKE3 hash of the ML-DSA-65 quorum certificate that finalizes each anchor, so the beacon is post-quantum for the same reason the certificate is. A quantum adversary cannot forge a vote, cannot fabricate a quorum certificate, and cannot rewrite finalized history by breaking the consensus signature, because the certificate is a set of independent lattice signatures with no classical component anywhere in it.

### Account and transaction keys: PQM-1 (live)

Wallet keys never depended on classical cryptography on this chain. Addresses derive through a scheme we call **PQM-1**: a 24-word BIP-39 mnemonic feeds a SHAKE256 key-derivation function, which seeds ML-DSA-65 key generation, which produces the keypair, and the address is a 20-byte hash of the public key. Every transaction is signed with ML-DSA-65, and the transaction-admission path rejects any classical signature algorithm outright. There are no classical ECDSA account keys at any layer. The thing every user actually cares about, "can someone forge a signature and move my funds," has a post-quantum answer here and always has.

(One honest footnote on addresses: a 20-byte address gives roughly 2^80 of margin against a Grover-accelerated second-preimage search. That is the same margin Bitcoin and Ethereum accept, and the binding public key is ML-DSA-65 regardless, so the address is a lookup, not the thing that authorizes a spend. We treat it as an accepted residual, identical to the rest of the industry.)

### The wire: post-quantum transport, chain-bound identity (live)

Peer-to-peer transport uses a custom Noise-style handshake with the classical stages removed. Key exchange is **ML-KEM-768** (FIPS 203, the standardized form of Kyber) double-encapsulation; there is no X25519 stage, no hybrid, no classical fallback. Mutual authentication is KEM-implicit: each side proves possession of its long-lived ML-KEM decapsulation key, so there is no classical signature or Diffie-Hellman in the auth path either. Record encryption is ChaCha20-Poly1305 with keys derived from the post-quantum handshake. A harvest-now-decrypt-later adversary recording our peer traffic gets nothing it can ever open, because the session keys come from a lattice KEM.

Operator identity is bound to the chain with an ML-DSA-65 signature over a domain-separated message tying the peer's network identity to its on-chain key. The protocol's design rule forbids classical signatures on the in-protocol signing surface, and the signing surface honors that.

## The honest edges

A post that only listed wins would be exactly the kind of post we do not trust. Here is everything that is not finished or not post-quantum, stated as plainly as we can.

**It is testnet, research-stage, and unaudited.** Everything above runs on a public testnet, not a mainnet. The chain is value-less and can be wiped without notice. No part of this has cleared an external cryptographic audit; an audit is on the roadmap, not behind us. Do not rely on this chain where a failure has real consequences. The standardized primitives we use are individually well-analyzed; the way we compose them on the protocol surface is not yet reviewed by anyone outside the project.

**Every cryptographic operation is post-quantum. The one classical thing left is not a cryptographic operation at all.** Consensus and finality, account ownership, operator identity, and the transport key exchange are post-quantum, and we enforce it mechanically: an internal lint bans classical asymmetric cryptography on the protocol surface, and it passes with an empty allow-list. We went past what that line strictly required. There is no classical signature anywhere in the protocol, not even at the gossip layer. Peer-to-peer messages used to carry a libp2p Ed25519 signature on top of their post-quantum application-layer authentication; we removed it, because it was pure redundancy. Every consensus message already authenticates itself with ML-DSA-65, and all of it rides an ML-KEM-authenticated transport, so the classical signature added nothing and now it is gone. The one classical artifact that remains is the libp2p PeerId, an Ed25519-format node identifier. It performs no cryptographic function. It signs nothing and authenticates nothing: transport security is the ML-KEM handshake, message security is the ML-DSA signatures. It is a name, the way an IP address is a name, and libp2p derives that name from a classical keypair because the library offers no post-quantum option. Replacing the identifier format means forking libp2p's identity layer, which is on our roadmap and changes no security property the day it lands. So the honest claim is the strong one. Every cryptographic operation in this protocol is post-quantum, and the only classical bytes left are a network identifier that secures nothing.

**The proof systems are not post-quantum end to end.** Zero-knowledge machine-learning verification and cross-chain bridge verification, as designed, would use SP1 with a Groth16 proof over the BN254 curve. That is classical, pairing-based cryptography, and a quantum computer running Shor could forge such a proof. The honest reason is that no vendor ships post-quantum on-chain STARK verification today; it is unsolved across the industry, not just for us. Two things keep this from being a live hole. First, these features are **gated off** on the testnet, so the running chain does not actually expose this gap. Second, finality is anchored by ML-DSA-65 checkpoints: a quantum attacker could forge a zkML or a bridge proof, but could not forge the post-quantum anchor that finalizes the chain, so the blast radius is bounded by what those features touch, not by finality itself. The post-quantum replacements (a hash-based FRI verifier, a STARK-based light client) are real research work on our roadmap, not a crate we can swap in. We are naming the gap rather than hiding behind the gate.

**A bridge to a classical chain inherits that chain's assumptions.** We cannot make Ethereum or any other classical network post-quantum. If and when we bridge, the far side carries its own cryptographic assumptions, and we will say so explicitly rather than imply otherwise.

**Symmetric and hash primitives are Grover-weakened, and that is fine.** ChaCha20-Poly1305, SHAKE256, and BLAKE3 are not Shor-broken; the relevant quantum attack is Grover's algorithm, which gives only a quadratic speedup, not an exponential one. A 256-bit key under Grover still has roughly 128 bits of post-quantum security, which is the standard, comfortable margin. We use 256-bit keys and 256-bit hashes throughout for exactly this reason. We mention it so you can see we accounted for it, not because it is a concern.

## Why this matters, and what is next

The reason to do the hard part (swapping consensus itself to a lattice signature, rather than just the account keys) is that finality is the one thing a chain cannot walk back. If a quantum adversary can forge finality, every other protection is downstream of a broken root. We moved that root to post-quantum cryptography and shipped it, and we are not asking anyone to take "quantum-resistant" on faith: it is in the code, it is on a public testnet, and the parts that are not done yet are listed above by name.

The roadmap from here is concrete:

- **A post-quantum peer identifier**, by forking the libp2p identity layer so even the PeerId stops being Ed25519-format. This is the last classical artifact in the stack, it secures nothing today, and removing it changes no security property, but we want the zero.
- **An external cryptographic audit** of the protocol's cryptographic surface.
- **Post-quantum proof systems** (FRI-based verification) as the gated-off features approach activation, so they never ship on quantum-vulnerable cryptography.
- **Mainnet**, once the above is real and reviewed.

We are careful with the word "unbreakable," because anyone who reaches for it is one pointed question away from losing the room. So here is the claim, stated exactly, with nothing rounded up: every cryptographic operation in this protocol is post-quantum. There is no classical signature or classical key exchange anywhere in the stack, and no quantum-vulnerable asymmetric primitive on the live protocol path. The only classical bytes left are a network identifier that secures nothing, and a proof-system feature that is gated off and named in full above. We are not claiming the universe cannot surprise us. We are claiming we did the work, all of it, on the layers that decide who owns what and which history is real, and that we will keep saying exactly how far it goes and no further. The discipline to not round it up is the point.

## See for yourself

Everything above is in the code and running on a public testnet: the lattice consensus signatures, the post-quantum account keys, and the lattice transport handshake. Inspect it, break it, tell us what we missed. The whole point of being this specific is that you do not have to take our word for any of it.
