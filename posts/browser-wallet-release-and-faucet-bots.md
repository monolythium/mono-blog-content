---
title: "Browser wallet release and faucet bots"
date: 2026-06-01
excerpt: "The Monolythium Browser Wallet is live for Chromium browsers. Testnet LYTH is now available through the Discord faucet bot and Telegram @monolythium_bot."
cover: "/blog-covers/monolythium-012.png"
category: Ecosystem
tags: ["browser wallet", "testnet", "faucet"]
author: Monolythium Foundation
draft: false
---

The Monolythium Browser Wallet is live.

The first public browser-extension build for the current Monolythium testnet is now available through the Chrome Web Store for Chrome, Brave, Edge, and other Chromium-based browsers:

[Install the Monolythium Browser Wallet](https://chromewebstore.google.com/detail/hendlkmpghhmhmggjebkpbedncpepkgj)

The source release is available on GitHub at [`monolythium/browser-wallet`](https://github.com/monolythium/browser-wallet/releases/tag/v0.1.5).

This is a preview build for testnet `69420`. Mainnet has not launched. Treat it as real wallet software, because it holds real keys, but use it for testnet activity until mainnet activation.

## Faucet access

Testnet LYTH is now easier to get.

There is a faucet bot in the Monolythium Discord. Join the server, open the faucet channel, paste your Monolythium address, and the bot will fund it with testnet LYTH.

The faucet also works on Telegram through [`@monolythium_bot`](https://t.me/monolythium_bot). Start the bot, send your wallet address, and it will return testnet LYTH for network fees and basic testing.

These are testnet tokens. They exist so users can create wallets, send transactions, test dapps, and inspect activity on Monoscan without needing a paid allocation.

## What the browser wallet does

The browser wallet is the fastest way to use Monolythium from a normal browser.

It can create a new Monolythium wallet, import an existing recovery phrase, display your `mono...` address, show balances and activity, receive testnet LYTH, and send transactions through the live testnet RPC. It links transaction receipts out to Monoscan so a user can verify what the network accepted.

For dapps, the wallet exposes an EIP-1193 provider in the page. That gives websites a standard way to request account access and ask the wallet to prepare a transaction, while the wallet keeps signing inside the extension boundary. A dapp does not get the recovery phrase. A dapp does not get silent signing. Connection and transaction requests go through explicit wallet UI.

The wallet also includes the first user-facing surfaces for native Monolythium features: ML-DSA-65 accounts, bech32m addresses, MRC asset support, staking and delegation flows, connected-site management, and security settings for each vault. More surfaces will continue to land as the public testnet hardens.

## Security posture

Monolythium is not an EVM chain with a MetaMask skin. The wallet is built for the current Monolythium protocol surface.

Every Monolythium account uses a PQM-1 recovery phrase and an **ML-DSA-65** signing key. ML-DSA-65 is the post-quantum signature path accepted by the chain. The wallet renders user-facing addresses as bech32m `mono...` addresses rather than asking users to copy raw `0x...` strings.

The vault is encrypted locally in browser extension storage. The password-derived key protects the encrypted vault, and destructive operations route through the popup approval flow. Recovery-phrase reveal requires a fresh password check and a hold-to-reveal interaction. Wrong-password attempts enter a lockout ladder rather than allowing unlimited online guessing.

The extension architecture is split the way Manifest V3 expects:

- the service worker owns keystore state, network configuration, signing, balances, and transaction history;
- the popup owns the approval UI and wallet settings;
- the content scripts bridge dapp requests into the extension without exposing the vault to the page.

That separation matters. A website can request a connection or submit a transaction request, but the wallet boundary decides what is shown, what is signed, and what is rejected.

## How to try it

1. Install the wallet from the Chrome Web Store.
2. Create a new wallet or import an existing Monolythium recovery phrase.
3. Copy your `mono...` address.
4. Request testnet LYTH from the Discord faucet bot or Telegram [`@monolythium_bot`](https://t.me/monolythium_bot).
5. Send a small transaction and inspect it on [Monoscan](https://monoscan.xyz).

This is the shape of the user loop we want testnet to make boring: install wallet, get testnet LYTH, sign something, verify it publicly, report anything that feels wrong.

## What to report

If the wallet shows the wrong network, cannot connect to the public RPC, fails to display a balance, or signs a transaction that does not appear on Monoscan, tell us in Discord with the wallet version, browser, operating system, and transaction hash if one exists.

For security issues, do not post details publicly. Send reports to `security@monolythium.com`.

## Closing

The testnet is useful only if people can touch it. A browser wallet and faucet bots are the two simplest entry points: one gives users a key and signing surface; the other gives them enough testnet LYTH to use it.

Install the wallet. Fund it from Discord or Telegram. Send a transaction. Break something small now so mainnet gets stronger later.

— Monolythium Foundation

---

**Find us**

- Website — [monolythium.com](https://monolythium.com)
- Browser wallet — [Chrome Web Store](https://chromewebstore.google.com/detail/hendlkmpghhmhmggjebkpbedncpepkgj)
- Explorer — [monoscan.xyz](https://monoscan.xyz)
- Docs — [docs.monolythium.com](https://docs.monolythium.com)
- GitHub — [github.com/monolythium](https://github.com/monolythium)

**Community**

- Discord — [discord.gg/monolythium](https://discord.gg/monolythium)
- Telegram faucet — [@monolythium_bot](https://t.me/monolythium_bot)
- Telegram community — [t.me/monolythium](https://t.me/monolythium)
- X / Twitter — [@monolythium](https://x.com/monolythium)
