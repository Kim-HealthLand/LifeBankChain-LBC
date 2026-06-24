# LifeID Admin Data Permission Design

## 1. Purpose

LIFE BANK CHAIN uses `LifeID` as the user's digital life identity. `LifeID` is planned to be issued as an NFT-style identity credential, while real user data and privacy-sensitive information remain off-chain in the project database.

This document defines how off-chain LifeID data, privacy data, backend data visualization, admin operations, AI access, and audit controls should be designed.

Core principle:

> The backend may manage identity status and data permissions, but it must not become a place where user privacy can be casually viewed.

## 2. LifeID NFT and Off-Chain Data Boundary

### 2.1 On-chain LifeID NFT

The LifeID NFT should act as an identity anchor and credential, not as a container for private user data.

Recommended on-chain fields:

- `tokenId`
- `ownerWallet`
- `metadataURI`
- `metadataHash`
- `identityStatus`
- `issuedAt`
- `revokedAt`

Recommended status values:

- `active`
- `suspended`
- `revoked`

Recommended design:

- LifeID should be non-transferable or transfer-restricted.
- One wallet should not freely mint multiple active LifeID credentials.
- Sensitive personal, health, genomic, biometric, or KYC data must not be stored directly on-chain.
- On-chain records should prove identity existence, ownership, validity, and permission state only.

### 2.2 Off-chain project database

The project database stores the operational and privacy-sensitive data behind LifeID.

Recommended off-chain records:

- User account
- LifeID internal record
- Wallet binding
- DID binding
- KYC status
- Consent records
- Privacy data
- Authorization records
- AI Agent access records
- Admin audit logs

Principle:

> On-chain proves that the identity credential exists and is valid. Off-chain systems manage real data, privacy, authorization, and operational state.

## 3. Data Classification

### 3.1 Account data

Examples:

- User ID
- Email
- Username
- Avatar
- Registration time
- Login status
- Account status

Purpose:

- User login
- Account management
- Notification
- Basic user identification

Sensitivity level: medium.

### 3.2 LifeID identity data

Examples:

- LifeID internal ID
- LifeID NFT token ID
- Wallet address
- DID
- Chain ID
- Mint status
- Identity status
- Metadata URI
- Metadata hash
- Issued time
- Revoked time

Purpose:

- Identity anchoring
- Wallet binding
- NFT credential management
- DID and verifiable credential extension
- Permission verification

Sensitivity level: high.

### 3.3 Privacy-sensitive identity data

Examples:

- Real name
- Phone number
- Government ID
- Passport
- KYC documents
- Biometric information
- Health data
- Genomic data
- Payment-sensitive data
- Private uploaded files

Purpose:

- KYC
- Compliance verification
- Account recovery
- High-trust service authorization

Sensitivity level: highest.

Default rules:

- Backend tables must not show raw sensitive values by default.
- Sensitive values must be masked in list views.
- Plaintext access requires secondary verification.
- Plaintext access must generate audit logs.
- High-risk operations require approval.

### 3.4 Life data and business data

Examples:

- LifeVault data references
- LifeGene records
- LifeCell records
- Authorization scopes
- AI analysis outputs
- User service history
- Ecosystem participation records

Purpose:

- LIFE BANK CHAIN ecosystem services
- Bio-AI analysis
- User-owned data authorization
- Data asset management
- Governance and value circulation

Sensitivity level: high to highest, depending on content.

## 4. Backend Roles

| Role | Scope | Default Permission |
|---|---|---|
| User | Own account, LifeID, consent, and data records | View and manage own data |
| Support | Basic account state and support tickets | Help with account issues, no raw privacy access |
| Compliance | KYC status, risk status, compliance review | Review and mark identity/compliance state |
| Data Admin | Masked data and aggregate analytics | View operational reports, no raw privacy access |
| Security Officer | Security logs, suspicious activity, access events | Audit, investigate, and trigger security actions |
| Super Admin | System settings, role management, policy management | Manage system permissions, no default raw privacy access |
| AI Agent | User-authorized data only | Read or write only within authorized scope |

Important rule:

> Super Admin may manage permissions, but should not automatically have direct plaintext access to all user privacy data.

## 5. Permission Principles

### 5.1 Least privilege

Each role should only access the data needed to complete its task.

Examples:

- Support can view account status, but not KYC documents.
- Data Admin can view aggregate dashboards, but not raw user privacy data.
- AI Agent can use authorized LifeVault context, but not hidden compliance notes.

### 5.2 Masked display by default

Sensitive fields should be masked in backend lists and detail pages unless plaintext access is explicitly approved.

Examples:

```txt
Email: li***@mail.com
Phone: +1 *** *** 1234
Wallet: 0x12...89ab
ID Number: **** **** 9876
```

### 5.3 Plaintext access control

Viewing plaintext sensitive data requires:

- Secondary authentication
- Access reason
- Time-limited session
- Field-level audit log
- Optional approval for highest-risk data

### 5.4 High-risk operation approval

The following operations should require approval, preferably two-person approval:

- Export sensitive data
- Delete privacy data
- Freeze LifeID
- Revoke LifeID NFT
- Modify KYC result
- Unbind wallet
- Modify DID binding
- Bulk download user records
- Change admin role permissions

## 6. Backend Modules

Recommended admin backend modules:

```txt
LIFE BANK CHAIN Admin
├─ Users
├─ LifeID Identity
├─ Wallet Binding
├─ DID Binding
├─ KYC / Compliance
├─ Privacy Vault
├─ Consent Management
├─ AI Agent Access
├─ Data Export
├─ Audit Logs
├─ Risk Alerts
└─ Admin Roles
```

## 7. LifeID Backend Management

The LifeID backend should manage identity state without exposing sensitive identity content by default.

Recommended visible fields:

- LifeID internal ID
- Token ID
- Wallet address, masked by default
- DID, masked by default
- Chain ID
- Mint status
- Identity status
- KYC status
- Created time
- Last updated time

Recommended operations:

- View LifeID status
- View NFT mint state
- View wallet binding state
- View DID binding state
- Suspend LifeID
- Restore LifeID
- Revoke LifeID, approval required
- Trigger re-verification
- View related audit logs

Restricted operations:

- Directly editing token ownership
- Manually changing KYC results without audit
- Deleting identity records without approval
- Exposing raw KYC files in default views

## 8. Privacy Vault

Privacy Vault is the restricted backend area for highest-sensitivity data.

It may store or reference:

- KYC documents
- Identity files
- Biometric data references
- Health data references
- Genomic data references
- Encrypted user private files

Design rules:

- Data should be encrypted at rest.
- Raw values should not appear in standard admin dashboards.
- Access should be time-limited.
- Access reason is mandatory.
- Every read must be logged.
- Export should be disabled by default.

## 9. Consent Management

User consent is central to LIFE BANK CHAIN.

Consent records should define:

- Data owner
- Data type
- Authorized service
- Authorized actor
- Scope
- Purpose
- Start time
- Expiry time
- Revocation state

Example consent record:

```ts
ConsentRecord {
  id: string
  userId: string
  lifeId: string
  granteeType: "service" | "agent" | "admin" | "partner"
  granteeId: string
  dataScope: string[]
  purpose: string
  status: "active" | "revoked" | "expired"
  startsAt: Date
  expiresAt: Date
  revokedAt?: Date
}
```

Backend rules:

- Admins can inspect consent state.
- Admins should not silently create user consent.
- Users should be able to revoke consent.
- AI Agent access must be bound to explicit consent or product-level user authorization.

## 10. AI Agent Data Access

AI Agents must not have unlimited access to all off-chain data.

### 10.1 Default accessible data

If authorized by the user or product flow, AI Agents may access:

- User-provided context
- Authorized LifeVault data
- Authorized project/service records
- Authorized historical analysis
- User goals and preferences
- Prior AI conversation memory

### 10.2 Default restricted data

AI Agents should not access by default:

- KYC documents
- Government ID
- Raw health data
- Raw genomic data
- Biometric data
- Payment-sensitive data
- Admin-only compliance notes
- Internal risk investigation notes

### 10.3 Agent access log

Every AI data access should generate a record:

```ts
AgentAccessLog {
  id: string
  agentName: string
  userId: string
  lifeId: string
  dataTypes: string[]
  purpose: string
  action: "read" | "write" | "summarize" | "analyze"
  outputType: string
  memoryWritten: boolean
  consentRecordId?: string
  createdAt: Date
}
```

## 11. Audit Logs

Audit logs are mandatory for all sensitive operations.

Recommended audit log schema:

```ts
AuditLog {
  id: string
  actorId: string
  actorRole: string
  action: string
  targetUserId?: string
  targetLifeId?: string
  targetResourceType: string
  targetResourceId: string
  fieldsAccessed?: string[]
  reason?: string
  approvalId?: string
  ipAddress?: string
  deviceInfo?: string
  createdAt: Date
}
```

Events that must be audited:

- Viewing plaintext sensitive data
- Viewing KYC files
- Modifying KYC results
- Freezing or revoking LifeID
- Changing wallet binding
- Changing DID binding
- Exporting data
- Deleting data
- Modifying admin roles
- AI Agent accessing user data
- Failed attempts to access restricted data

## 12. Data Export Rules

Data export is more sensitive than data viewing.

Rules:

- Standard admins cannot export raw sensitive data.
- Default export should be masked or aggregated.
- Sensitive export requires approval.
- Export files should expire automatically.
- Export links should be time-limited.
- Every export must generate audit logs.
- Bulk export should trigger risk alerts.

## 13. User Control Panel

Users should have a LifeID data control panel.

Users should be able to view:

- LifeID status
- NFT mint status
- Wallet binding
- DID binding
- Data stored by the system
- Active consents
- Revoked consents
- AI Agent data usage history
- Major account security events

Users should be able to operate:

- Update account profile
- Bind wallet
- Request wallet change
- Revoke consent
- Request data export
- Request data deletion
- Request LifeID freeze
- Request identity recovery

## 14. MVP Scope

Recommended MVP implementation:

- Admin role definitions
- LifeID status management
- Wallet binding state view
- Masked sensitive fields
- Basic audit logs
- Consent records
- AI Agent access records
- User LifeID control panel

Recommended V2 implementation:

- Privacy Vault
- KYC document review workflow
- Two-person approval
- Sensitive data export approval
- DID / VC backend management
- Chain-based LifeID freeze or revoke operations
- Advanced risk alerts
- Field-level encryption

## 15. Summary

LifeID is the identity anchor of LIFE BANK CHAIN. Issuing LifeID as an NFT creates a trusted on-chain credential, but privacy and real user data must remain off-chain.

The backend must therefore be designed as a controlled, auditable, privacy-preserving management system.

Key principles:

- Identity credential on-chain
- Private data off-chain
- Backend management without default privacy exposure
- Role-based access control
- Masking by default
- Plaintext access by approval
- High-risk operation review
- Full auditability
- User consent and user control
- AI access only within authorized boundaries
