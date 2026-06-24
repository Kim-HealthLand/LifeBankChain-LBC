# Smart Contract Design

Last updated: 2026-06-24

This directory stores LIFEBANK CHAIN smart contract architecture, token standards, contract boundaries, and on-chain/off-chain integration notes.

LIFEBANK CHAIN is currently designed to not necessarily launch its own native chain at the beginning, while not ruling out the possibility of launching a dedicated LIFEBANK CHAIN network in the future.

The current smart contract system should be built around the existing LBC Token, LifeID, permissions, credentials, LifeVault authorization, governance, and related ecosystem modules.

Current confirmed direction:

- LBC Token has already been issued on BNB Chain.
- LifeID is planned as an identity NFT.
- LifeID is expected to use an ERC-721 / ERC-5192 Soulbound NFT design.
- Sensitive user data must remain off-chain.

## Current Documents

- [LBC Smart Contract Architecture](./lbc-smart-contract-architecture.md)

## Suggested Content

- Token standard selection
- Contract responsibility boundaries
- LifeID identity model
- Permission and credential model
- LifeVault authorization model
- Governance and incentive model
- Security and compliance notes
