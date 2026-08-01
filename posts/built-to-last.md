---
title: "Built to last"
date: 2026-08-01
excerpt: "Why Monolythium is taking its time on security, and the change that means the network no longer has to start over every time it improves."
cover: "/blog-covers/built-to-last.png"
coverAlt: "Monolythium cover reading Built to last, with the subtitle: security is the foundation, not a step before launch. Decorative; it carries no capability, status, or performance claim."
category: Ecosystem
tags:
  - security
  - signed upgrades
  - post-quantum cryptography
  - development network
author: Monolythium Foundation
draft: false
---

**TL;DR**

- Security is the reason we move deliberately. We treat it as the product, not a step before launch.
- The threat landscape changed fast. Powerful tooling is now in everyone's hands, including the people trying to break things.
- We attack our own work before anyone else gets the chance, and fix what we find long before it reaches users.
- The network can now upgrade itself in place. No more rebuilding from scratch every time something improves.
- Wallets, developer tools and the chain now move together, in step.

---

## Why we are taking our time

A blockchain is not an app. If an app has a bug, you patch it and users move on. If a blockchain has a bug in the wrong place, the damage is permanent and it belongs to everyone holding the asset. There is no undo.

That gap has widened over the past year. The same advances that make software easier to build also make attacks easier to find and cheaper to run. Techniques that used to require a specialist team are now within reach of anyone motivated enough to try. Anything that goes out into the world should expect to be probed by tooling that did not exist eighteen months ago.

So we made a decision early: security is not a phase we pass through on the way to launch. It is the thing we are building. Everything else is downstream of it.

In practice that means we assume our own work is flawed until we have genuinely tried to break it. We do not sign off on our own security code. We hand it to an independent review pass with one instruction — attack this, do not validate it.

Every piece of security-critical work goes through that review before it ships, and nothing reaches production until it has come through the other side. It is slower than shipping on our own sign-off. It is also the difference between a system that has been tested and one that has merely been trusted.

We are also building for a threat that has not fully arrived. Monolythium's core signatures are post-quantum by design — built to withstand the class of computers expected to break the cryptography most of the industry runs on today. Retrofitting that later is close to impossible. We chose to carry the cost up front.

None of this is fast. It is the part that is worth waiting for.

## What has changed

For most of this project's life, improving the network meant restarting it. A change to how it was configured, or a new version of the software, meant beginning again from a new starting point — and everything built on top had to be rebuilt too: wallets, developer tools, the block explorer, the documentation.

That is now solved, and it is the most significant thing we have built.

The network can now accept changes to both its settings and its own software while remaining the same network. Updates arrive as signed, verified instructions that the chain checks against a key committed at its very foundation. Nothing can be slipped in, and nothing can be quietly removed. The network keeps its identity, and everything built on top keeps working.

The practical effect is already visible. Our developer kit, all three wallets — browser, desktop and mobile — and our agent-facing tooling were all updated and released together, in step with the chain. That kind of coordinated release was structurally impossible before. It is now routine.

What comes next is the first full exercise of that upgrade path: improving the network in place, without restarting it. After that, the road to mainnet, and then opening participation to operators beyond the Foundation.

We would rather arrive with something that holds. The foundation is poured, and everything from here builds on it instead of replacing it.

*The current network is a development network. Test tokens have no economic value, and no mainnet is live.*
