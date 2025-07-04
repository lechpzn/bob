---
sidebar_position: 2
sidebar_label: The Hybrid Stack
---

# The Hybrid Stack

## Introduction

BOB represents a new paradigm in blockchain architecture: a [Hybrid Layer 2](/learn/introduction/roadmap) that unifies the security and liquidity of Bitcoin with the innovation and activity of Ethereum's vibrant DeFi ecosystem.

This page outlines the _BOB Stack_, the infrastructure choices we've made and are currently researching to achieve our vision of a hybrid network that is greater than the sum of its parts. From underlying data availability and consensus mechanisms to user-facing bridges and account abstraction, we are proud of the progress we've made on [our roadmap](/learn/introduction/roadmap/) to uniting Bitcoin and Ethereum.

## BOB Core

### Hybrid Data Availability

_A Hybrid L2 should have censorship-resistant, unilateral exit to multiple L1s._

#### Ethereum DA: Blobs

BOB currently uses Ethereum's EIP-4844 blob transactions for cost-effective data availability (DA). All transaction data is periodically committed to Ethereum L1, ensuring verifiability and censorship resistance aligned with Ethereum's security model.

Users can already force withdraw their assets from BOB to Ethereum, even if BOB's sequencer goes offline. An Ethereum transaction can directly call the [Native Ethereum Bridge](/learn/reference/contracts/#ethereum-l1) that BOB has as an OP Stack rollup.

#### Bitcoin DA: Forced Withdrawal with Bitcoin Transactions

BOB is researching a [hybrid data availability](https://blog.gobob.xyz/posts/hybrid-data-availability-enforcing-bitvm-withdrawals-on-bob) architecture that combines Ethereum's cost efficiency with Bitcoin's censorship resistance. We are exploring modifying our `op-node` to add a check for forced withdrawal transactions on Bitcoin as part of the derivation pipeline.

This hybrid DA model offers:

- **Censorship Resistance:** Leverages Bitcoin's battle-tested security as fallback storage
- **Forced Transaction Inclusion:** Enables forced withdrawals back to Bitcoin through the [BitVM bridge](/learn/introduction/bitvm/#bitvm-bridge-summary), even if BOB's Sequencer is offline.

### Hybrid Consensus

_A Hybrid L2 should inherit security from multiple L1s._

#### Ethereum Consensus: OP Stack Rollup

BOB launched on the [OP stack](https://docs.optimism.io/) as an optimistic rollup and will soon transition to a validity rollup model. This involves using SNARKs to create validity proofs that cryptographically verify the correctness of BOB state proposals, as described below. These ZK proofs enable immediate finalization of state and faster withdrawals to Ethereum, reducing withdrawal times from 7 days to just a few hours.

#### Bitcoin Consensus: Soft Finality via BTC-Staking

BOB will soon transition to [phase two of our roadmap](/learn/introduction/roadmap/#-phase-2-bitcoin-soft-finality), introducing BTC-staked Finality Providers (FPs) to our consensus mechanism. These FPs sign BOB state proposals and receive sequencer fees in exchange for providing economic security through their staked BTC. If FPs sign competing chains, their staked BTC will be slashed, creating strong economic incentives against chain forks - a critical feature for maintaining consistency across BOB's native bridges.

The consensus process combines two key building blocks:

- Validity proofs using SNARKs to cryptographically guarantee the correctness of all BOB state transitions
- BTC-staked FPs that sign state proposals, with ⅔ stake required for finalization

This dual-consensus model provides both mathematical certainty of transaction validity through SNARKs and economic security backed by native BTC. The combination enables secure bridging to both Bitcoin and Ethereum while preventing safety failures and double-spend attacks.

[BOB's ultimate goal](/learn/introduction/roadmap/#phase-3-full-bitcoin-security) is to settle directly on Bitcoin. While direct ZK verification on Bitcoin is unlikely in the near future, BOB's innovative combination of BitVM and BTC-staked finality creates a robust security model that doesn't require any Bitcoin forks.

### Native Bridges

_A Hybrid L2 should have secure asset transfers across its L1s._

#### OP Standard Bridge

BOB utilizes the [OP Stack's native bridge contracts](https://docs.optimism.io/app-developers/bridging/standard-bridge) for secure and free asset transfers between Ethereum and BOB. This allows users to deposit and withdraw assets directly through L1 smart contracts, inheriting Ethereum's security guarantees and censorship-resistance. The bridge supports 1-click onboarding of ERC-20 tokens, stablecoins, and ETH, with over $250M in TVL secured by Ethereum's validator network.

#### BitVM Bridge

[BOB has prototyped](https://blog.gobob.xyz/posts/bob-announces-trust-minimized-bitcoin-bridge-prototype-powered-by-bitvm/) a two-way light-client BTC bridge powered by [BitVM](/learn/introduction/bitvm/) in partnership with Fiamma. Withdrawals of bridged BTC from BOB to Bitcoin through this BitVM bridge are verified by a ZK SNARK fraud-proof mechanism that relies on a 1-of-N trust assumption for operation. A bridged version of BTC secured by BitVM is a massive improvement in BTC safety because it unlocks unilateral exit back to Bitcoin.

In combination with our [hybrid Bitcoin consensus model](#hybrid-consensus) described above, BOB's native bridges to Bitcoin and Ethereum will both be secured by Bitcoin Finality Providers.

## EVM Ecosystem

_A Hybrid L2 should make Bitcoin DeFi smooth and safe for users and builders._

[Intent-based bridging](/learn/introduction/gateway/), [smart accounts](/learn/reference/tools/account-abstraction/) controlled by Bitcoin wallets, [paying fees with BTC](/learn/builder-guides/bridged-btc-gas-fee/), and [unilateral exit back to Bitcoin](/learn/introduction/bitvm/) and Ethereum - BOB is singularly focused on bringing this UX and security to Bitcoin DeFi by providing these tools to teams building on BOB.

### Smart Contracts Reading and Writing to Bitcoin

BOB uses the [Ethereum Virtual Machine](https://ethereum.org/en/developers/docs/evm/) (EVM) to execute smart contracts developed using the [Solidity](https://soliditylang.org/) programming language. BOB makes it easy for your smart contract to verify Bitcoin transactional data with our [BTC light client](/learn/builder-guides/relay/), a low-level interface for parsing Bitcoin blocks in real-time. Trustlessly reading Bitcoin data within EVM smart contract logic unlocks a broad design space, such as P2P native BTC swaps, Ordinal auctions, and hashrate tokenization.

While the [BTC light client](/learn/builder-guides/relay/) and [oracles](/learn/reference/tools/oracles/) make it easy to dynamically _read_ Bitcoin state, we imagine a world where smart contracts _write_ to Bitcoin as well. For example, a DAO that pools its funds to pay for a batch inscription of ordinals to airdrop its members.

The majority of Bitcoin's stack and applications built around it are implemented in Rust, including core [SDKs](https://github.com/rust-bitcoin/rust-bitcoin/), [Lightning](https://github.com/lightningdevkit/rust-lightning/), and [Ordinals](https://github.com/ordinals/ord/). BOB can support Bitcoin's Rust libraries via the [RISC Zero zkVM](/learn/reference/tools/rust-zkvm/), which allows off-chain execution of Rust programs while using [ZK proofs](https://ethereum.org/en/zero-knowledge-proofs/) to verify correct execution in EVM smart contracts. In the future, we see this as a path to scaling as a [ZK rollup](https://vitalik.eth.limo/general/2021/01/05/rollup.html) directly on Bitcoin where BOB itself can be proven in the zkVM and verified by Bitcoin consensus.

### Interop and Bridging

<!-- TODO: Add link around "programmable" to upcoming Gateway Strategy Creation page. -->

[BOB Bridge](/learn/user-guides/onboard-to-bob/bob-gateway/) is our intent-based Bitcoin bridge built on the light client verification mentioned above. It is optimized to be fast, inexpensive, and programmable. As of this writing, 30,000 users have swapped a total of 75 BTC in a trust-minimized, peer-to-peer way.

[BOB Stake](/learn/user-guides/stake-btc/) extends this idea by executing users' "intents" to stake, restake, or lend their BTC automatically during the bridge process. There are [more than a dozen options available](https://app.gobob.xyz/en/stake) at the moment, some offering five different sources of yield. All options are BTC-denominated and have no risk of impermanent loss.

We built the [BOB Gateway SDK](/learn/builder-guides/gateway/) to make it possible for any frontend to offer its users these options by plugging into our infrastructure. Using the BOB Gateway SDK with [Dynamic's](https://www.dynamic.xyz/) wallet abstraction, you can even send native BTC to someone's email address via [BOB Pay](/learn/user-guides/bob-pay/#what-is-bob-pay).

BOB also supports [third-party bridges](https://app.gobob.xyz/bridge/) to many chains. Developers working on cross-chain protocols can leverage [Chainlink CCIP](/learn/reference/tools/cross-chain#chainlink-ccip), [LayerZero](/learn/reference/tools/cross-chain#layerzero), and [Hyperlane](https://docs.hyperlane.xyz/docs/reference/contract-addresses/) on BOB.

### Hybrid Tooling

By using the same EVM as Ethereum, teams building on BOB have access to familiar and modern tooling:

- [Data Analytics](/learn/reference/tools/data-indexers/) and [Node Providers](/learn/reference/tools/node-providers/)
- [Cross-chain Messaging](/learn/reference/tools/cross-chain/) and [Oracles](/learn/reference/tools/oracles/)
- [Wallets](/learn/user-guides/getting-started/wallet-guide/), including [Account Abstraction](/learn/reference/tools/account-abstraction/) (AA), [Social Login](/learn/reference/tools/social-login/), and [our SDK for connecting to Bitcoin wallets](/learn/builder-guides/sats-wagmi/)

We are particularly excited about AA providers like [Safe](/learn/reference/tools/account-abstraction#safe-wallet) and [BTC Connect](/learn/reference/tools/account-abstraction#btc-connect) that make it possible to control funds on EVM chains from a Bitcoin wallet.

## Conclusion

BOB is committed to improving the security and UX for Bitcoin DeFi at every layer: inheriting security and finality from Bitcoin, building a better UX for self-custody when staking BTC, and giving all users' unilateral exit to bridge their assets back to their original L1s.

Our Hybrid L2 has already made inroads on all of these goals, with major upgrades to each of them in line of sight.
