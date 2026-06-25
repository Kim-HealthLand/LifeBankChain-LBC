# LifeID Public Privacy and Data Boundary

## 1. Purpose

This public document describes the high-level privacy and data-boundary principles for LifeID in LIFE BANK CHAIN.

Detailed backend permission rules, admin workflows, node operation records, sales performance models, settlement logic, audit schemas, and internal risk-control rules are internal documents and are not intended for public release.

## 2. Core Principle

LifeID is an on-chain identity anchor. It is not a container for private user data.

Public principle:

```text
On-chain records prove identity status, ownership, authorization references, and settlement references.
Off-chain systems protect private data, operational records, internal permissions, and detailed business logic.
```

## 3. Public On-Chain Boundary

LifeID and related contracts may publicly reference:

- token ID
- wallet address
- identity status
- metadata URI
- metadata hash
- permission status references
- credential status references
- consent or authorization event references
- settlement or reward event hashes

On-chain contracts must not directly store:

- real name
- phone number
- government ID
- KYC documents
- biometric data
- health data
- genomic data
- raw LifeVault data
- raw sales performance records
- customer records
- private business records

## 4. LifeID Boundary

LifeID represents a user's digital life identity credential.

Recommended public principles:

- LifeID should be non-transferable or transfer-restricted.
- LifeID should act as an identity anchor.
- LifeID should not expose private personal data.
- Sensitive identity verification should remain off-chain.
- Public metadata should contain only public, masked, referenced, or hashed information.

## 5. Node NFT Boundary

Node NFT may represent tradable node rights, node ownership, node operation rights, or node-related ecosystem value.

Recommended public principles:

- Node NFT may be transferable.
- Node NFT is separate from LifeID.
- Node NFT ownership does not automatically grant access to private user data.
- Node NFT ownership does not automatically expose customer, member, order, or sales records.
- Node settlement or reward references may be recorded on-chain as hashes or events.
- Detailed node operations remain protected by off-chain systems.

## 6. Credential and Permission Boundary

Life Credential and Life Permission have different purposes:

```text
Life Credential = qualification proof
Life Permission = authorization capability
```

Public principle:

```text
Credential does not grant data access by itself.
Permission does not override credential requirements.
User consent is required for user-owned data.
Auditability is required for sensitive access.
```

## 7. LifeVault Boundary

LifeVault is the protected off-chain data layer.

Public principle:

- LifeVault data should remain off-chain.
- Access should require identity, credential, permission, consent, and audit checks.
- On-chain records may reference authorization status or hashes.
- Raw private data must not be exposed through public blockchain records.

## 8. Public vs Internal Documentation

Public documentation may include:

- smart contract architecture
- on-chain/off-chain boundaries
- privacy principles
- high-level LifeID, Node NFT, credential, and permission concepts
- public event and interface design

Internal documentation should contain:

- admin permission details
- backend data schemas
- node member management rules
- sales performance models
- settlement calculation details
- user classification logic
- risk-control rules
- KYC and compliance workflows
- audit schema details
- internal dashboard permissions

## 9. Summary

LIFE BANK CHAIN should keep the public architecture transparent while protecting operational, privacy-sensitive, compliance-sensitive, and commercially sensitive backend logic.

Final principle:

```text
Public docs explain trust boundaries.
Internal docs control private operations.
```
