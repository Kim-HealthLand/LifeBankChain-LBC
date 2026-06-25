# Public Database Boundary

Detailed off-chain database schemas, LifeID data flows, LifeGene data flows, privacy data models, and protected backend data structures are maintained in the private internal repository.

This public directory should only describe high-level data-boundary principles.

## Public Principle

```text
Public documentation may describe what data must not be placed on-chain.
Internal documentation defines how off-chain data is stored, processed, protected, audited, and synchronized.
```

## Public Scope

Public documentation may include:

- high-level on-chain/off-chain data boundary
- public privacy principles
- public trust model
- public hash/reference model

Public documentation should not include:

- backend database schemas
- LifeID private data flow details
- LifeGene private data flow details
- KYC or identity verification workflow
- health, genomic, biometric, or private data processing details
- internal authorization or audit models

## Internal Source

The complete database and data-flow design belongs in the private internal repository.
