# LBC Smart Contract Architecture

Last updated: 2026-06-24

## Positioning

LIFEBANK CHAIN is currently designed to not necessarily launch its own native chain at the beginning. This does not rule out the possibility of launching a dedicated LIFEBANK CHAIN network in the future.

At the current stage, LBC Token has already been issued on BNB Chain. BNB Chain is EVM-compatible and supports ERC-compatible smart contract standards.

The smart contract system should be built around LBC Token, LifeID, permissions, credentials, LifeVault authorization, governance, and related ecosystem modules.

This architecture should remain chain-portable. Early contracts can be deployed on BNB Chain, while future versions may migrate, bridge, or extend to a dedicated LIFEBANK CHAIN network if the ecosystem requires it.

The smart contract layer should connect LifeID, LBC Token, data permissions, credentials, LifeVault authorization, and ecosystem governance while keeping sensitive life data off-chain.

## Recommended Contract Stack

```text
LBC Token              Existing BNB Chain token / ERC-20 compatible
LifeID                ERC-721 / ERC-5192 Soulbound NFT
Life Permissions       ERC-1155
Life Credentials       ERC-1155
LifeID Registry       Custom registry contract
LifeVault Permission   Custom authorization contract
Governance             ERC-20Votes / Governor-compatible model
```

## Core Relationship Model

```text
Wallet Address
  -> owns one LifeID
  -> holds LBC Token
  -> holds permission tokens
  -> holds credential tokens
  -> grants or revokes LifeVault access
  -> participates in ecosystem governance
```

Example:

```text
Wallet A
  -> LifeID #1001
  -> 10,000 LBC
  -> Permission token: GENE_READ
  -> Credential token: KYC_VERIFIED
  -> Authorized access to selected LifeVault data
```

## Contract Modules

### 1. LBC Token Contract

Status: Already issued on BNB Chain.

Standard: ERC-20 compatible token on an EVM-compatible network.

Purpose:

- Ecosystem utility token
- Incentive distribution
- Service payment
- Governance participation
- Ecosystem reward settlement

Possible extensions:

- `ERC20Permit` for gasless approvals
- `ERC20Votes` for governance voting power
- Vesting or treasury contracts for controlled release

Token metadata to confirm:

```text
Network: BNB Chain
Contract Address: TBD
Token Symbol: LBC
Decimals: TBD
Total Supply: TBD
Official Explorer URL: TBD
```

Integration note:

Future LifeID, permission, credential, and governance contracts should reference the existing LBC Token contract address instead of deploying a new LBC token.

### 2. LifeID Contract

Recommended standard: `ERC-721` with soulbound behavior.

Possible standard:

- `ERC-5192` for locked soulbound NFT behavior.

Purpose:

- Represents a user's decentralized life identity.
- Connects a wallet address to a LifeID.
- Acts as the identity anchor for permissions, credentials, and authorization.

Design note:

LifeID is planned as an identity NFT using an ERC-721 / ERC-5192 Soulbound NFT design. It should usually be non-transferable. A user's life identity should not be freely traded like a normal NFT.

### 3. Life Permission Contract

Recommended standard: `ERC-1155`

Purpose:

- Represents reusable permission types.
- Supports granular data access authorization.
- Allows different permission scopes under one contract.

Example permission token types:

```text
GENE_READ
HEALTH_PROFILE_READ
LIFECELL_RECORD_READ
AI_ANALYSIS_ACCESS
RESEARCH_CONSENT
INSURANCE_UNDERWRITING_CONSENT
```

### 4. Life Credential Contract

Recommended standard: `ERC-1155`

Purpose:

- Represents verifiable credentials or ecosystem badges.
- Can be issued by approved institutions.
- Can support KYC, lab verification, professional role verification, and partner certification.

Example credential token types:

```text
KYC_VERIFIED
LAB_VERIFIED
HEALTH_PROVIDER
RESEARCH_PARTNER
ECOSYSTEM_MEMBER
```

### 5. LifeID Registry Contract

Standard: custom contract.

Purpose:

- Maps wallet addresses to LifeID token IDs.
- Tracks LifeID status.
- Links LifeID to permission and credential contracts.
- Provides lookup functions for other contracts and applications.

Example data:

```text
wallet -> lifeId
lifeId -> status
lifeId -> metadataURI
lifeId -> recoveryController
```

### 6. LifeVault Permission Contract

Standard: custom authorization contract.

Purpose:

- Stores data access authorization records.
- Verifies whether a grantee can access a specific data scope.
- Records grant, revoke, expiration, and audit events.
- Links access authorization to LifeID and permission token ownership.

Important:

This contract should not store real personal data, health data, genomic data, biometric data, or medical records.

## On-Chain vs Off-Chain Boundary

### On-Chain

Store only:

- DID or LifeID references
- Token ownership
- Permission status
- Credential status
- Data hashes
- Consent records
- Authorization timestamps
- Revocation records
- Audit event references

### Off-Chain

Store sensitive data outside the blockchain:

- Personal identity information
- Phone numbers
- Government IDs
- Biometric data
- Genomic data
- Health records
- Medical imaging
- Lab reports
- AI analysis outputs

Off-chain storage can use:

- Encrypted database
- LifeVault
- IPFS / Filecoin
- Cloud object storage
- Institution-managed secure storage

## Access Flow Example

```text
1. User creates LifeID.
2. User receives or binds a LifeID token.
3. User grants a data permission to a service provider.
4. Permission is recorded through the LifeVault Permission contract.
5. Service provider requests data access.
6. Backend verifies LifeID, permission token, consent status, and expiration.
7. Encrypted off-chain data is accessed only if authorization is valid.
8. Access event is recorded on-chain or linked through an audit hash.
```

## BNB Chain Deployment Notes

BNB Chain is EVM-compatible, so Solidity contracts based on ERC standards can run on BNB Chain with minimal changes.

BNB Chain should be treated as the initial deployment environment, not as a permanent limitation. LIFEBANK CHAIN may later launch its own dedicated chain or support a multi-chain identity and permission system.

Benefits:

- Lower transaction cost than Ethereum mainnet.
- EVM tooling compatibility.
- Broad wallet support.
- Easier early-stage deployment.

Considerations:

- Security audits are still required.
- Contract upgradeability must be handled carefully.
- Private life data must remain off-chain.
- Compliance requirements may vary by jurisdiction.
- Future native-chain or cross-chain migration paths should be considered in contract interface design.

## Security Principles

- Do not store sensitive life data on-chain.
- Make LifeID non-transferable unless there is a strong recovery design.
- Separate identity, token, permission, and data access responsibilities.
- Use role-based access control for issuers and administrators.
- Emit clear events for grants, revocations, and credential issuance.
- Add pause and emergency controls only where justified.
- Avoid centralized admin control over user-owned identity and data permissions.

## Future Contract Roadmap

```text
Phase 1:
  - Existing LBC Token integration
  - LifeID
  - LifeID Registry

Phase 2:
  - Life Permission ERC-1155
  - Life Credential ERC-1155
  - LifeVault Permission Contract

Phase 3:
  - Governance contract
  - Treasury contract
  - Ecosystem incentive contracts

Phase 4:
  - Cross-chain identity bridge
  - Partner institution issuer framework
  - Advanced privacy and zero-knowledge verification

Phase 5:
  - Dedicated LIFEBANK CHAIN network feasibility study
  - Native-chain migration or multi-chain expansion plan
  - Cross-chain LifeID and permission interoperability
```

## Next Step

The next design step is to define the exact contract interfaces, events, roles, and permission scopes before writing Solidity code.
