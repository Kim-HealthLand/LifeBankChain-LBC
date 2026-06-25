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
Node NFT               ERC-721 transferable NFT
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
  -> may own or trade Node NFTs
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
  -> Node NFT #2001
  -> 10,000 LBC
  -> Permission token: GENE_READ
  -> Credential token: IDENTITY_VERIFIED
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
- Ecosystem reward reference

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

### 3. Node NFT Contract

Recommended standard: `ERC-721`

Purpose:

- Represents tradable ecosystem node ownership, node rights, or node participation status.
- Supports node-based ecosystem expansion, community operation, regional attribution, partner participation, and incentive design.
- Provides an on-chain asset layer that is separate from the non-transferable LifeID identity layer.
- May reference protected off-chain operational records without exposing them on-chain.

Design note:

Node NFT is different from LifeID. LifeID is a soulbound identity credential and should not be freely traded. Node NFT can be transferable and may represent ecosystem participation rights, node operation rights, or node-related value rights.

Possible node categories:

```text
GLOBAL_NODE
COUNTRY_NODE
REGIONAL_NODE
CITY_NODE
COMMUNITY_NODE
PARTNER_NODE
```

Possible metadata fields:

```text
nodeId
nodeCategory
nodeRegion
nodeCountry
nodeLevel
nodeOwner
metadataURI
metadataHash
status
```

Important boundary:

- Owning a Node NFT does not automatically grant access to private user data.
- Owning a Node NFT does not replace LifeID verification.
- Node NFT ownership may influence ecosystem rights, incentives, governance, or operational attribution.
- User node attribution can be stored off-chain and may reference a Node NFT, but should not expose private user data on-chain.
- Detailed node operations should remain protected off-chain.
- Node NFT ownership may define public node rights, ecosystem participation, or reward-reference eligibility.

Public node operation relationship:

```text
Node NFT
  -> represents node ownership or node operation rights
  -> may link to protected off-chain operational records
  -> may receive on-chain reward or settlement reference events
```

On-chain records should be limited to:

```text
nodeId
nodeNftTokenId
owner
metadataURI
metadataHash
status
rewardEventHash
referenceHash
```

This keeps Node NFT tradable while keeping protected operational records off-chain.

### 4. Life Permission Contract

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

### 5. Life Credential Contract

Recommended standard: `ERC-1155`

Purpose:

- Represents verifiable credentials or ecosystem badges.
- Can be issued by approved institutions.
- Can support KYC, lab verification, professional role verification, and partner certification.

Example credential token types:

```text
IDENTITY_VERIFIED
LAB_VERIFIED
HEALTH_PROVIDER
RESEARCH_PARTNER
ECOSYSTEM_MEMBER
```

### 6. LifeID Registry Contract

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

### 7. LifeVault Permission Contract

Standard: custom authorization contract.

Purpose:

- Stores data access authorization records.
- Verifies whether a grantee can access a specific data scope.
- Records grant, revoke, expiration, and audit events.
- Links access authorization to LifeID and permission token ownership.

Important:

This contract should not store real personal data, health data, genomic data, biometric data, or medical records.

## Life Credential and Life Permission Relationship

Life Credential and Life Permission are different but related smart contract modules in LIFEBANK CHAIN.

The simplest distinction is:

```text
Life Credential = Qualification Layer
Life Permission = Authorization Layer
```

Life Credential answers:

```text
Who is this subject, and what qualification does it have?
```

Life Permission answers:

```text
Is this subject allowed to access a specific type of data or perform a specific action?
```

Credential does not automatically grant access. Permission does not override credential requirements. For user-owned data, consent and audit are also required.

### Relationship with LifeID

LifeID is the identity anchor of the system. It is the base identity NFT that connects a wallet address to a user identity, identity status, credentials, permissions, and LifeVault data references.

Conceptual relationship:

```text
Wallet
  -> owns LifeID
       -> has identity status
       -> may hold Life Credentials
       -> may grant Life Permissions
       -> controls LifeVault data
```

LifeID is the subject anchor. Credentials and permissions are built around LifeID or around ecosystem participants that interact with a LifeID.

### Life Credential

Life Credential proves that a subject has a certain qualification, identity status, verification result, or ecosystem role.

Credentials may be issued to users, institutions, service providers, or ecosystem participants.

User credential examples:

```text
IDENTITY_VERIFIED
GENOME_OWNER
HEALTH_PROFILE_CREATED
LIFECELL_DONOR
```

Institution or service provider credential examples:

```text
LAB_VERIFIED
HOSPITAL_VERIFIED
RESEARCH_PARTNER
INSURANCE_PARTNER
AI_SERVICE_PROVIDER
```

Ecosystem participant credential examples:

```text
ECOSYSTEM_MEMBER
DATA_VALIDATOR
GOVERNANCE_PARTICIPANT
```

The purpose of a credential is qualification proof. It should not be treated as direct data access authorization.

### Life Permission

Life Permission records authorization for data access, service access, or operational capability.

A permission should normally include:

```text
ownerLifeId
grantee
dataScope
purpose
expiresAt
status
```

Example:

```text
LifeID #1001 grants Research Partner A:
  - Scope: GENE_READ
  - Purpose: Research
  - Expiration: 2027-01-01
```

Life Permission answers whether a user allows a specific grantee to access a specific data scope for a specific purpose within a specific time window.

### Combined Access Logic

Data access should not be based only on credential checks or only on permission checks. A valid access decision should combine identity, credential, permission, consent, scope, purpose, policy checks, and auditability.

Example request:

```text
Research Partner A requests genomic data from LifeID #1001.
```

Required checks:

```text
1. LifeID #1001 is active.
2. The user granted GENE_READ permission to Research Partner A.
3. The permission is active and not expired.
4. Research Partner A has the RESEARCH_PARTNER credential.
5. Research Partner A has the COMPLIANCE_VERIFIED credential when required.
6. The requested data scope matches the permission.
7. The requested purpose matches the authorized purpose.
8. Required policy checks pass.
9. An audit event is recorded.
```

Recommended access formula:

```text
CanAccess =
  Active LifeID
  + Active Permission
  + Required Credential
  + Valid Scope
  + Valid Purpose
  + Active Consent
  + Compliance Pass
  + Audit Record
```

### Why Credential Must Not Equal Permission

This is a core security boundary.

Example:

```text
Hospital A has HEALTH_PROVIDER credential.
```

This only proves that Hospital A is a recognized healthcare provider. It does not mean Hospital A can automatically read any user's health data.

The user must still grant an explicit permission:

```text
User grants HEALTH_PROFILE_READ permission to Hospital A.
```

Correct logic:

```text
Credential proves eligibility.
Permission grants access.
```

Incorrect logic:

```text
Credential alone grants access.
```

LIFEBANK CHAIN should avoid any design where credentials automatically expose user-owned data.

### Relationship with LifeVault

LifeVault is the secure off-chain data storage and access-control layer.

LifeVault should not open data simply because a requester is a privileged actor or a verified institution. It should evaluate access based on:

```text
LifeID status
Consent status
Permission status
Credential requirement
Data scope
Purpose
Expiration
Audit requirement
Policy control
```

When a service provider requests LifeVault data, the authorized verification layer should verify:

```text
- Which LifeID owns the data.
- Who the requester is.
- Whether the requester has the required credential.
- Whether the user granted the required permission.
- Whether the permission is still valid.
- Whether the requested purpose matches the authorized purpose.
- Whether an audit event must be recorded.
```

### Relationship with Service Providers

A Service Provider is a data requester or service executor.

Examples:

```text
Research Institution
Hospital
Lab
Insurance Provider
AI Agent
Wellness Service
DApp
```

A Service Provider should first hold the required credential before it can request or receive certain permissions.

Examples:

```text
Research Institution
  -> must have RESEARCH_PARTNER credential
  -> may request GENE_READ permission

Hospital
  -> must have HEALTH_PROVIDER credential
  -> may request HEALTH_PROFILE_READ permission

AI Agent
  -> must be an approved AI_SERVICE_PROVIDER
  -> may access authorized AI_ANALYSIS_ACCESS data
```

Credential determines what permissions a provider may request. Permission determines what data the provider may actually access.

### Recommended Contract Relationship

Recommended relationship:

```text
LifeID Contract
  -> identity anchor

Life Credential Contract
  -> issues credentials to users, institutions, providers, and ecosystem participants

Life Permission Contract
  -> defines permission types or permission tokens

LifeVault Permission Contract
  -> records actual user consent and access grants

LifeID Registry
  -> maps wallet, LifeID, status, credentials, and permission references
```

Design distinction:

```text
Life Permission Type:
  GENE_READ

Actual LifeVault Grant:
  LifeID #1001 grants GENE_READ to Research Partner A until 2027-01-01
```

This separates permission types from concrete access grants and avoids mixing general authorization categories with user-specific consent records.

### Recommended Data Models

Credential model:

```text
Credential {
  credentialType
  holder
  issuer
  issuedAt
  expiresAt
  status
  metadataURI
}
```

Permission type model:

```text
PermissionType {
  permissionCode
  dataCategory
  allowedPurpose
  requiredCredential
  defaultExpiration
}
```

Access grant model:

```text
AccessGrant {
  ownerLifeId
  grantee
  permissionCode
  dataScope
  purpose
  issuedAt
  expiresAt
  status
  auditHash
}
```

### Recommended Access Check

```text
function canAccess(ownerLifeId, grantee, permissionCode, purpose) {
  check LifeID is active
  check grant exists
  check grant is active
  check grant is not expired
  check purpose matches
  check grantee has required credential
  check requested scope is allowed
  write audit event
  return allow or deny
}
```

### Summary

```text
LifeID = identity anchor
Life Credential = qualification proof
Life Permission = permission type or authorization capability
LifeVault Permission = concrete user data access grant
LifeVault = off-chain data vault
Service Provider = data requester
Audit = access record and accountability layer
```

Final boundary:

```text
Credential does not grant access by itself.
Permission does not override credential requirements.
Consent is required for user-owned data.
LifeVault enforces access based on identity, credential, permission, consent, and audit.
```

## Node NFT Contract Interface Design

Node NFT is the tradable node-right asset layer of LIFEBANK CHAIN.

It is separate from LifeID:

```text
LifeID = non-transferable identity credential
Node NFT = transferable node ownership or node operation asset
```

Node NFT may represent node ownership, node operation rights, node-level ecosystem participation, regional expansion rights, partner node rights, or node-related reward and settlement eligibility.

### Design Positioning

Node NFT connects:

```text
node ownership
node category
node status
node metadata
node operation rights
protected off-chain reference hashes
reward or ecosystem reference hashes
governance or incentive references
```

Node NFT must not directly store:

```text
private user data
private operational data
private business records
private compliance records
LifeVault raw data
```

Node NFT is a node-right asset, not an operational database.

### Recommended Standard

Recommended standard:

```text
ERC-721 transferable NFT
```

Rationale:

- Node NFT may represent unique node rights.
- Node rights may be transferable under ecosystem rules.
- ERC-721 provides wallet, marketplace, and metadata compatibility.
- Node NFT should remain separate from the soulbound LifeID identity layer.

### Node Categories

Recommended node categories:

```text
GLOBAL_NODE
COUNTRY_NODE
REGIONAL_NODE
CITY_NODE
COMMUNITY_NODE
PARTNER_NODE
```

These categories can support regional expansion, partner participation, community development, and future incentive design.

### Node Status Model

Recommended status values:

```text
PENDING
ACTIVE
SUSPENDED
REVOKED
EXPIRED
```

Status meaning:

- `PENDING`: Minted or reserved, but not fully activated.
- `ACTIVE`: Valid node asset with active node rights.
- `SUSPENDED`: Temporarily frozen due to governance, policy, or ecosystem review.
- `REVOKED`: Permanently invalidated under governance or compliance rules.
- `EXPIRED`: Node right has reached a predefined expiration condition, if applicable.

### Recommended Core Record

Recommended conceptual record:

```solidity
struct NodeRecord {
    uint256 tokenId;
    bytes32 nodeId;
    NodeCategory category;
    NodeStatus status;
    address owner;
    string metadataURI;
    bytes32 metadataHash;
    bytes32 referenceHash;
    bytes32 rewardEventHash;
    uint256 issuedAt;
    uint256 updatedAt;
}
```

Notes:

- `nodeId` should be a stable internal node identifier.
- `metadataURI` should describe the node asset without exposing private business data.
- `metadataHash` validates metadata consistency.
- `referenceHash` may reference a protected off-chain operational result.
- `rewardEventHash` may reference a public reward or ecosystem event.

### Recommended Roles

Recommended role constants:

```solidity
bytes32 public constant NODE_ISSUER_ROLE = keccak256("NODE_ISSUER_ROLE");
bytes32 public constant NODE_ADMIN_ROLE = keccak256("NODE_ADMIN_ROLE");
bytes32 public constant NODE_COMPLIANCE_ROLE = keccak256("NODE_COMPLIANCE_ROLE");
bytes32 public constant NODE_METADATA_ROLE = keccak256("NODE_METADATA_ROLE");
bytes32 public constant NODE_REFERENCE_ROLE = keccak256("NODE_REFERENCE_ROLE");
bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
```

Role responsibilities:

- `NODE_ISSUER_ROLE`: Mint Node NFTs.
- `NODE_ADMIN_ROLE`: Manage node status and operational parameters.
- `NODE_COMPLIANCE_ROLE`: Suspend or revoke node rights after review.
- `NODE_METADATA_ROLE`: Update node metadata references.
- `NODE_REFERENCE_ROLE`: Record public-safe reference hashes or reward event references.
- `PAUSER_ROLE`: Pause critical operations in emergency cases.

### Recommended Functions

#### mintNodeNFT

```solidity
function mintNodeNFT(
    address to,
    bytes32 nodeId,
    NodeCategory category,
    string calldata metadataURI,
    bytes32 metadataHash
) external returns (uint256 tokenId);
```

Rules:

- Callable only by `NODE_ISSUER_ROLE`.
- `to` must not be the zero address.
- `nodeId` should be unique.
- Initial status may be `PENDING` or `ACTIVE`.
- Must emit `NodeNFTMinted`.

#### activateNode

```solidity
function activateNode(uint256 tokenId) external;
```

Rules:

- Callable by `NODE_ADMIN_ROLE`.
- Usually changes status from `PENDING` to `ACTIVE`.
- Must emit `NodeStatusChanged`.

#### suspendNode

```solidity
function suspendNode(
    uint256 tokenId,
    bytes32 reasonCode
) external;
```

Rules:

- Callable by `NODE_COMPLIANCE_ROLE`.
- Used for governance, policy, or ecosystem review.
- Must emit `NodeStatusChanged`.

#### restoreNode

```solidity
function restoreNode(uint256 tokenId) external;
```

Rules:

- Callable by `NODE_COMPLIANCE_ROLE` or `NODE_ADMIN_ROLE`.
- Restores a suspended node to `ACTIVE` when the issue is resolved.
- Must emit `NodeStatusChanged`.

#### revokeNode

```solidity
function revokeNode(
    uint256 tokenId,
    bytes32 reasonCode
) external;
```

Rules:

- Callable by `NODE_COMPLIANCE_ROLE`.
- Restricted operation.
- Should follow the project's governance or policy process before execution.
- `REVOKED` node rights should not be restored without a formal governance process.
- Must emit `NodeRevoked`.

#### updateNodeMetadata

```solidity
function updateNodeMetadata(
    uint256 tokenId,
    string calldata metadataURI,
    bytes32 metadataHash
) external;
```

Rules:

- Callable by `NODE_METADATA_ROLE` or another approved governance path.
- Metadata must not expose private operational data.
- Must emit `NodeMetadataUpdated`.

#### recordNodeReference

```solidity
function recordNodeReference(
    uint256 tokenId,
    bytes32 referenceHash,
    bytes32 rewardEventHash,
    bytes32 referenceType
) external;
```

Rules:

- Callable by `NODE_REFERENCE_ROLE`.
- Records a public-safe hash reference to protected off-chain results.
- Does not store private operational or business data.
- Must emit `NodeReferenceRecorded`.

#### setNodeTransferPolicy

```solidity
function setNodeTransferPolicy(
    bool transferable
) external;
```

Rules:

- Callable by governance or `NODE_ADMIN_ROLE`, depending on final governance design.
- Allows the ecosystem to pause or restrict Node NFT transfers when required.
- Transfer policy changes must emit an event.

### Transfer and Management Rights

Node NFT can be transferable, but transfer may affect node management rights.

Recommended rule:

```text
Node NFT owner = default node-right holder
```

When Node NFT is transferred:

```text
1. Token ownership changes on-chain.
2. Node ownership reference changes on-chain.
3. Protected off-chain node-right mappings may be updated.
4. Protected operational history remains off-chain.
5. Historical protected records should not be overwritten by token transfer.
6. The ownership transition should be traceable through public events and internal audit.
```

Important:

Transfer of Node NFT should not automatically expose protected off-chain operational data.

### Node NFT Data Boundary

Node NFT represents node rights, ownership, and tradable ecosystem value.

Node operation data remains off-chain.

On-chain Node NFT may store:

```text
nodeId
nodeNftTokenId
owner
category
status
metadataURI
metadataHash
referenceHash
rewardEventHash
```

Off-chain node systems should store:

```text
protected operational records
protected operational result records
protected audit records
```

Boundary rules:

- Node NFT ownership does not automatically grant access to private user data.
- Node NFT ownership does not automatically grant access to protected business data.
- Node NFT ownership may grant access to public node rights or public-safe ecosystem references.
- Protected operational visibility must follow internal access-control rules.
- On-chain node records should use hashes or event references.

### Relationship with LifeID

Node NFT and LifeID should interact, but they should not be merged.

Recommended relationship:

```text
LifeID
  -> identifies a user or ecosystem participant

Node NFT
  -> represents node ownership or node operation rights

Off-chain node operation records
  -> may link LifeID to a node under protected internal rules
```

Examples:

```text
LifeID #1001 owns Node NFT #2001.
LifeID #1002 may participate in an off-chain node operation role.
LifeID #1003 may participate in an off-chain node operation role.
```

Node NFT ownership may be associated with a LifeID, but Node NFT should remain transferable while LifeID remains soulbound.

### Relationship with Protected Off-Chain References

Node NFT may define the node asset under which protected off-chain operational records are organized.

Recommended model:

```text
Node NFT
  -> nodeId
  -> protected off-chain operational records
  -> on-chain referenceHash or rewardEventHash
```

Protected operational records should be calculated and managed off-chain.

The chain should only record public-safe proof or reward reference:

```text
referenceHash
rewardEventHash
referenceType
```

This allows future reward or governance logic without exposing protected operational records.

### Recommended Events

```solidity
event NodeNFTMinted(
    uint256 indexed tokenId,
    bytes32 indexed nodeId,
    address indexed owner,
    NodeCategory category,
    bytes32 metadataHash
);

event NodeStatusChanged(
    uint256 indexed tokenId,
    NodeStatus oldStatus,
    NodeStatus newStatus,
    bytes32 reasonCode
);

event NodeMetadataUpdated(
    uint256 indexed tokenId,
    bytes32 oldMetadataHash,
    bytes32 newMetadataHash
);

event NodeReferenceRecorded(
    uint256 indexed tokenId,
    bytes32 referenceHash,
    bytes32 rewardEventHash,
    bytes32 referenceType
);

event NodeTransferPolicyUpdated(
    bool transferable
);

event NodeRevoked(
    uint256 indexed tokenId,
    bytes32 reasonCode
);
```

ERC-721 transfer events should also be emitted through the standard transfer mechanism.

### Recommended Query Functions

```solidity
function getNodeByTokenId(uint256 tokenId) external view returns (NodeRecord memory);

function getNodeStatus(uint256 tokenId) external view returns (NodeStatus status);

function getNodeOwner(uint256 tokenId) external view returns (address owner);

function getNodeId(uint256 tokenId) external view returns (bytes32 nodeId);

function isActiveNode(uint256 tokenId) external view returns (bool);

function getNodeReferenceHash(uint256 tokenId) external view returns (bytes32 referenceHash);
```

Recommended validation rule:

```text
isActiveNode = token exists + status is ACTIVE + owner is valid
```

### Security Principles

- Node NFT can be transferable; LifeID should remain soulbound.
- Node NFT metadata must not expose private business data.
- Node NFT ownership must not automatically expose protected off-chain records.
- Node transfers should be traceable and may update protected off-chain mappings.
- Reference hashes should point to verified protected off-chain records.
- Node revocation and suspension should require governance or compliance review.
- All critical node operations must emit events.
- On-chain node events should be linked to off-chain audit logs.

### Summary

Node NFT should answer:

```text
Who owns this node-right asset?
What node category does it represent?
Is this node active?
What public-safe reference or reward event is associated with it?
Can this node participate in ecosystem rights, incentives, or governance?
```

Node NFT should not answer:

```text
What are the protected operational records under this node?
Who are the private participants under this node?
What are the detailed protected business records?
What private data does the node manage?
```

Final principle:

```text
Node NFT is a tradable node-right asset.
Protected operations are managed off-chain.
On-chain records hold ownership, status, and public-safe references.
```

## LifeID Contract Interface Design

LifeID is the core on-chain identity anchor of LIFEBANK CHAIN.

It should follow the logic of:

```text
ERC-721 + ERC-5192 Soulbound NFT
```

LifeID should provide a standard NFT-compatible identity credential while remaining locked by default. It should not behave like a freely tradable NFT.

### Design Positioning

LifeID connects:

```text
wallet
identity token
identity status
metadata reference
DID reference
credential reference
permission reference
LifeVault authorization
```

LifeID must not store:

```text
real name
phone number
government ID
KYC file
health data
genomic data
biometric data
LifeVault raw data
```

LifeID is an identity anchor, not a private data container.

### Standards Reference

Recommended standards and patterns:

- `ERC-721` for NFT identity structure, token ownership, `ownerOf`, `balanceOf`, and metadata compatibility.
- `ERC-5192` for locked or soulbound NFT behavior.
- Role-based access control for issuer, compliance, recovery, metadata, and pause operations.
- SIWE-style wallet proof for wallet binding and ownership verification in off-chain flows.
- DID and Verifiable Credential references should be stored as references or hashes, not as raw private identity data.

### Status Model

Recommended LifeID status values:

```text
PENDING
ACTIVE
SUSPENDED
REVOKED
REPLACED
```

Status meaning:

- `PENDING`: Created but not fully verified or activated.
- `ACTIVE`: Valid and usable identity.
- `SUSPENDED`: Temporarily frozen due to policy review, user request, or recovery process.
- `REVOKED`: Permanently invalidated.
- `REPLACED`: Replaced by another LifeID token through a controlled recovery or migration process.

Design note:

Recovery should not rely on normal NFT transfer. For a soulbound identity, controlled replacement is safer than direct transfer.

### Soulbound and Recovery Logic

LifeID should be locked by default.

```text
Normal transfer = blocked
Recovery = controlled replacement
```

Recommended recovery flow:

```text
1. User loses access to the original wallet.
2. Off-chain identity recovery, evidence review, and approval are completed.
3. The old LifeID token is marked as REPLACED or REVOKED.
4. A new LifeID token is minted to the verified new wallet.
5. The registry records oldTokenId -> newTokenId.
6. The off-chain identity record remains continuous.
```

This keeps the LifeID soulbound while still allowing account recovery and future migration.

### Wallet and Identity Uniqueness

Recommended rules:

```text
One wallet should not hold multiple active LifeIDs.
One real identity should not have multiple active LifeIDs.
```

Wallet uniqueness can be enforced on-chain through:

```text
wallet -> active LifeID tokenId
```

Real-person uniqueness should not expose personal data on-chain. It should be verified off-chain through KYC, DID, VC, World ID, zero-knowledge proof, or other privacy-preserving methods.

The chain may store only:

```text
uniquenessProofHash
verificationStatus
identityStatus
```

### Recommended Core Record

Recommended conceptual record:

```solidity
struct LifeIDRecord {
    uint256 tokenId;
    address owner;
    LifeIDStatus status;
    string metadataURI;
    bytes32 metadataHash;
    bytes32 didHash;
    uint256 issuedAt;
    uint256 updatedAt;
    uint256 replacedBy;
}
```

Notes:

- `metadataURI` must not expose private user data.
- `metadataHash` verifies metadata consistency.
- `didHash` avoids storing a full DID string if privacy or indexing concerns exist.
- `replacedBy` links an old identity token to a replacement token.

### Recommended Roles

Recommended role constants:

```solidity
bytes32 public constant ISSUER_ROLE = keccak256("ISSUER_ROLE");
bytes32 public constant COMPLIANCE_ROLE = keccak256("COMPLIANCE_ROLE");
bytes32 public constant RECOVERY_ROLE = keccak256("RECOVERY_ROLE");
bytes32 public constant METADATA_ROLE = keccak256("METADATA_ROLE");
bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
```

Role responsibilities:

- `ISSUER_ROLE`: Mint LifeID tokens.
- `COMPLIANCE_ROLE`: Suspend, restore, or revoke LifeID tokens after policy review.
- `RECOVERY_ROLE`: Execute controlled replacement or recovery actions.
- `METADATA_ROLE`: Update metadata references or DID references.
- `PAUSER_ROLE`: Pause critical operations in emergency cases.

Important:

`DEFAULT_ADMIN_ROLE` should manage roles, but should not automatically perform every restricted identity operation.

### Recommended Functions

#### mintLifeID

```solidity
function mintLifeID(
    address to,
    string calldata metadataURI,
    bytes32 metadataHash
) external returns (uint256 tokenId);
```

Rules:

- Callable only by `ISSUER_ROLE`.
- `to` must not be the zero address.
- `to` should not already hold an active LifeID.
- Initial status may be `PENDING` or `ACTIVE`, depending on the verification model.
- Must emit `LifeIDMinted`.

#### activateLifeID

```solidity
function activateLifeID(uint256 tokenId) external;
```

Rules:

- Callable by `ISSUER_ROLE` or `COMPLIANCE_ROLE`.
- Only `PENDING` LifeID should be activated.
- Must emit `LifeIDStatusChanged`.

#### suspendLifeID

```solidity
function suspendLifeID(
    uint256 tokenId,
    bytes32 reasonCode
) external;
```

Rules:

- Callable by `COMPLIANCE_ROLE`.
- Usually changes status from `ACTIVE` to `SUSPENDED`.
- Used for account compromise, policy review, or user-requested freeze.
- Must emit `LifeIDStatusChanged`.

#### restoreLifeIDStatus

```solidity
function restoreLifeIDStatus(uint256 tokenId) external;
```

Rules:

- Callable by `COMPLIANCE_ROLE`.
- Changes status from `SUSPENDED` back to `ACTIVE`.
- Must emit `LifeIDStatusChanged`.

#### revokeLifeID

```solidity
function revokeLifeID(
    uint256 tokenId,
    bytes32 reasonCode
) external;
```

Rules:

- Callable by `COMPLIANCE_ROLE`.
- Restricted operation.
- Should require off-chain approval before execution.
- `REVOKED` LifeID should not be reactivated.
- Must emit `LifeIDRevoked`.

#### replaceLifeID

```solidity
function replaceLifeID(
    uint256 oldTokenId,
    address newOwner,
    string calldata newMetadataURI,
    bytes32 newMetadataHash,
    bytes32 reasonCode
) external returns (uint256 newTokenId);
```

Rules:

- Callable by `RECOVERY_ROLE`.
- Old token status becomes `REPLACED`.
- New token is minted to the verified new wallet.
- Registry should record `oldTokenId -> newTokenId`.
- Off-chain identity record should remain continuous.
- Must emit `LifeIDReplaced`.

#### bindDID

```solidity
function bindDID(
    uint256 tokenId,
    bytes32 didHash
) external;
```

Rules:

- Callable by token owner or `METADATA_ROLE`, depending on governance policy.
- DID should not expose private data.
- Prefer storing `didHash` or minimal DID reference rather than full private identity data.
- Must emit `LifeIDDIDUpdated`.

#### updateMetadata

```solidity
function updateMetadata(
    uint256 tokenId,
    string calldata metadataURI,
    bytes32 metadataHash
) external;
```

Rules:

- Callable by token owner or `METADATA_ROLE`, depending on governance policy.
- Metadata must not expose private data.
- Must emit `LifeIDMetadataUpdated`.

#### locked

ERC-5192 standard function:

```solidity
function locked(uint256 tokenId) external view returns (bool);
```

Recommended rule:

```text
LifeID locked(tokenId) should normally return true.
```

### Transfer Restrictions

LifeID should block normal transfers:

```solidity
transferFrom
safeTransferFrom
```

Recommended behavior:

```text
If the operation is not a controlled recovery replacement, transfer should revert.
```

LifeID is an identity credential, not a trading asset.

### Recommended Events

```solidity
event LifeIDMinted(
    uint256 indexed tokenId,
    address indexed owner,
    bytes32 metadataHash
);

event LifeIDActivated(
    uint256 indexed tokenId
);

event LifeIDStatusChanged(
    uint256 indexed tokenId,
    LifeIDStatus oldStatus,
    LifeIDStatus newStatus,
    bytes32 reasonCode
);

event LifeIDMetadataUpdated(
    uint256 indexed tokenId,
    bytes32 oldMetadataHash,
    bytes32 newMetadataHash
);

event LifeIDDIDUpdated(
    uint256 indexed tokenId,
    bytes32 oldDIDHash,
    bytes32 newDIDHash
);

event LifeIDRevoked(
    uint256 indexed tokenId,
    bytes32 reasonCode
);

event LifeIDReplaced(
    uint256 indexed oldTokenId,
    uint256 indexed newTokenId,
    address indexed newOwner,
    bytes32 reasonCode
);
```

### Recommended Query Functions

```solidity
function getLifeIDByWallet(address wallet) external view returns (uint256 tokenId);

function getLifeIDStatus(uint256 tokenId) external view returns (LifeIDStatus status);

function hasActiveLifeID(address wallet) external view returns (bool);

function getReplacement(uint256 oldTokenId) external view returns (uint256 newTokenId);

function isValidLifeID(uint256 tokenId) external view returns (bool);
```

Recommended validation rule:

```text
isValidLifeID = token exists + status is ACTIVE + locked is true
```

### Relationship with LifeID Registry

LifeID Contract manages token ownership and token status.

LifeID Registry should manage mappings and indexes:

```text
wallet -> tokenId
tokenId -> status
oldTokenId -> newTokenId
didHash -> tokenId
metadataHash -> tokenId
```

For future cross-chain or dedicated LIFEBANK CHAIN migration, the registry may also record:

```text
chainId
contractAddress
originTokenId
currentTokenId
bridgeStatus
```

### Relationship with Protected Off-Chain Systems

Protected off-chain systems should manage sensitive operational processes.

```text
privacy-preserving verification
protected approvals
protected recovery evidence
internal audit records
consent records
```

Before executing restricted on-chain actions, the protected off-chain system should complete the required internal process.

```text
internal review
approval record
evidence reference
audit reference
```

The blockchain should execute final state changes and emit verifiable events.

### Security Principles

- LifeID must not store private data.
- LifeID should be locked by default.
- Normal transfer should be blocked.
- Recovery should use controlled replacement, not normal transfer.
- One wallet should not have multiple active LifeIDs.
- Revocation is a restricted action and should follow the required governance or policy process.
- Metadata should store only public data, masked data, references, or hashes.
- DID should be stored as a hash or minimal reference when possible.
- All critical operations must emit events.
- On-chain events should be linked to off-chain audit logs.

### Summary

LifeID should answer:

```text
Who owns this identity credential?
Is this identity active?
Is this identity locked?
Can this identity participate in permissions and credentials?
Has this identity been replaced or revoked?
```

LifeID should not answer:

```text
Who is the user in real life?
What private data does the user have?
What health or genomic data does the user have?
What is inside the user's LifeVault?
```

Final principle:

```text
LifeID is an identity anchor, not a private data container.
LifeID is soulbound by default.
Recovery should be handled by controlled replacement.
Sensitive data remains off-chain.
```

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

- Protected off-chain storage
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
6. Authorized verification layer verifies LifeID, permission token, consent status, and expiration.
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
- Use role-based access control for issuers and authorized operators.
- Emit clear events for grants, revocations, and credential issuance.
- Add pause and emergency controls only where justified.
- Avoid centralized privileged control over user-owned identity and data permissions.

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
