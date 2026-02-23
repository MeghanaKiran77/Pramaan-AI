# Design Document: Pramaan-Health

## Overview

Pramaan-Health implements a confidential AI training protocol that enables hospitals to contribute sensitive healthcare data for AI model training without exposing raw patient information. The system provides cryptographically verifiable trust through hardware-isolated execution, differential privacy enforcement, symmetric confidentiality protection (for both hospital data and developer IP), and comprehensive attestation mechanisms.

The architecture follows a four-layer design: Policy Layer (The Handshake), Execution Layer (The Vault), Incentive Layer (The Settlement), and Trust & Compliance Layer (The Proof). Each layer provides specific guarantees while maintaining end-to-end security and regulatory compliance.

**Core Governance Principles:**
- **Social Credits** are non-monetary, non-transferable, auditable units of verified impact representing institutional contribution (not individual patient tracking)
- **Deterministic Credit Minting** based on normalized improvement scores with protocol-defined caps to prevent gaming
- **Standardized Redemption** through capped, time-bound API/inference credits for public-sector usage (no negotiations, no IP conflicts)
- **Template-Driven Manifests** with structural constraints (not AI-generated free-form contracts)
- **Symmetric Confidentiality** protecting both hospital data and developer IP through enclave isolation
- **Institutional Verification** via DigiLocker document validation (not Aadhaar-based)

## Architecture

The Pramaan-Health system employs a four-layer architecture designed for protocol-first infrastructure with minimal interfaces for demonstration:

### Layer 1: Core Protocol Engine (Backend Services)
The main product - backend microservices implementing the protocol logic:
- Manifest validation service
- Request validation service
- Enclave orchestration service
- DP-SGD enforcement logic
- Evaluation engine
- Credit computation engine
- Attestation generator
- Settlement service
- Audit logger

**Implementation:** Python microservices, containerized (Docker), deployed on AWS, exposed via REST/GraphQL APIs

### Layer 2: Secure Execution Layer
The trust boundary where confidential training occurs:
- AWS EC2 with Nitro Enclaves
- .eif image submission and validation
- KMS-based key release (attestation-gated)
- In-memory decryption only
- Network egress denied by default

**Implementation:** AWS Nitro Enclaves with hardware attestation

### Layer 3: API Gateway
Public-facing endpoints enabling integration:
- `POST /manifest` - Create Protocol Manifest
- `POST /training-request` - Submit training job
- `GET /receipt/{id}` - Retrieve attestation receipt
- `GET /credits/{hospital-id}` - View institutional credits
- `GET /manifests` - Browse available manifests
- `POST /verify-receipt` - Third-party attestation verification

**Consumers:** Web UI, CLI tools, third-party systems, future mobile apps

### Layer 4: Minimal Web Interfaces (Pilot Only)
Thin demonstration interfaces (React/Next.js):

**Hospital Portal:**
- Create Manifest (template dropdown)
- View training jobs
- View attestation receipts
- View earned Social Credits

**Developer Portal:**
- Browse available manifests
- Accept manifest terms
- Upload .eif files
- View job status
- View credits owed
- Download attestation receipts

**Note:** These are minimal functional interfaces for demonstration, not production-grade applications. The core value is in Layers 1-3 (protocol infrastructure).

### System Architecture Diagram

The Pramaan-Health system employs a distributed architecture with secure enclaves as the core trust boundary:

```mermaid
graph TB
    subgraph "Hospital Environment"
        HDS[Healthcare Data Systems]
        HKM[Hospital Key Management]
        HAM[Hospital Admin Portal]
    end
    
    subgraph "Pramaan Controller"
        PMV[Protocol Manifest Validator]
        REQ[Request Processor]
        ATT[Attestation Verifier]
        MON[System Monitor]
    end
    
    subgraph "Secure Execution Environment"
        subgraph "AWS Nitro Enclave"
            DEC[Data Decryption]
            TRN[DP-SGD Training Engine]
            EVL[Model Evaluator]
            ATG[Attestation Generator]
        end
    end
    
    subgraph "Settlement & Compliance"
        LED[Settlement Ledger]
        AUD[Audit Repository]
        REG[Regulatory Interface]
    end
    
    subgraph "AI Developer Environment"
        DEV[Developer Portal]
        MOD[Model Repository]
    end
    
    HDS --> PMV
    HAM --> PMV
    DEV --> REQ
    REQ --> ATT
    ATT --> DEC
    DEC --> TRN
    TRN --> EVL
    EVL --> ATG
    ATG --> LED
    ATG --> AUD
    AUD --> REG
    HKM --> DEC
    MON --> ATT
```

## Components and Interfaces

### Policy Layer Components

**Protocol Manifest Validator**
- Validates machine-readable policy documents against template-defined schemas
- Enforces purpose limitation, dataset scope, and privacy constraints
- Supports versioning with immutability guarantees after job start
- Validates manifest hash to prevent retroactive edits
- Interface: REST API for manifest CRUD operations

**Manifest Template Manager**
- Provides healthcare-specific templates: diagnostic classification (public health), diagnostic classification (research), segmentation, risk scoring
- Predefines allowed metrics, normalization methods, credit mapping, caps, and redemption mechanics
- Prevents AI-generated free-form contracts through structural constraints
- Interface: Template selection and instantiation API

**Request Processor**
- Processes AI developer training requests with binary acceptance model
- Validates requests against approved Protocol Manifests
- Manages request lifecycle and status tracking
- Enforces no post-hoc negotiation policy
- Interface: GraphQL API for request submission and tracking

**DigiLocker Verification Service**
- Verifies hospital institutional authority via document validation
- Validates registration certificates, government affiliation documents, authorization letters
- Checks issuer authenticity and document validity
- Does NOT store Aadhaar numbers or process biometric data
- Interface: DigiLocker API integration for institutional verification

### Execution Layer Components

**Secure Enclave Manager**
- Manages AWS Nitro Enclave lifecycle
- Handles enclave attestation and integrity verification
- Controls data ingress/egress policies with network isolation
- Validates Enclave Image File (.eif) hash without inspecting internals
- Interface: Internal service with hardware attestation APIs

**Data Decryption Service**
- Decrypts healthcare data within enclave memory only
- Implements zero-persistence data handling
- Manages hospital encryption keys securely via AWS KMS
- Interface: Internal enclave service with key management integration

**DP-SGD Training Engine**
- Implements differentially private stochastic gradient descent
- Tracks privacy budget consumption (epsilon, delta parameters)
- Enforces training termination on budget exhaustion
- Executes developer-submitted .eif code without external inspection
- Interface: Internal training API with privacy accounting

**Model Evaluator**
- Evaluates trained models against success metrics within trusted boundary
- Computes baseline metrics and new model metrics inside enclave
- Calculates Normalized Improvement Score (NIS) using protocol-defined formula
- Prevents metric manipulation by external parties
- Interface: Internal evaluation service with metric computation APIs

**Symmetric Confidentiality Enforcer**
- Ensures hospital data never leaves enclave in raw form
- Ensures developer IP (code, weights, evaluation logic) never leaves enclave
- Allows only aggregated outputs: verified metrics, NIS, credits, signed receipts
- Prevents persistence of proprietary artifacts outside enclave
- Interface: Internal enclave boundary control

### Incentive Layer Components

**Normalized Improvement Score (NIS) Calculator**
- Computes NIS using formula: NIS = (NewMetric - BaselineMetric) / (1 - BaselineMetric)
- Accounts for diminishing returns at high baseline performance
- Ensures consistent improvement quantification across different baseline levels
- Interface: Internal calculation service

**Credit Minting Engine**
- Mints Social Credits deterministically based on protocol-defined credit bands
- Maps NIS ranges to specific credit amounts (no hospital discretion)
- Enforces hard caps: max credits per run (e.g., 500 SC), max per hospital per model per year (e.g., 5,000 SC)
- Prevents credit inflation through protocol-level constraints
- Allocates credits to institutional recipients (Hospital/Data Fiduciary), not individuals
- Interface: Internal service with settlement integration

**Settlement Ledger**
- Records credit transactions on permissioned blockchain (Amazon Managed Blockchain - Hyperledger Fabric)
- Maintains immutable transaction history with institutional recipients
- Does NOT track individual patient-level credit allocation
- Interface: Blockchain RPC with smart contract integration

**Redemption Manager**
- Manages standardized Model Access redemption: capped, time-bound API/inference credits
- Enforces public-sector usage restrictions
- Prevents lifetime free access or unlimited licensing
- Supports optional institutional allocation records without monetary transfer
- Interface: Redemption API with usage tracking

### Trust & Compliance Layer Components

**Attestation Generator**
- Creates cryptographically signed attestation receipts with hardware-backed keys
- Includes proofs of policy enforcement and privacy compliance
- Generates merkle proofs of execution integrity
- Embeds compliance statements in receipts
- References manifest hash, model hash, baseline hash, dataset version hash to prevent retroactive edits and duplicate minting
- Interface: Internal service with hardware signing keys

**Visibility Control Manager**
- Enforces role-based visibility: Hospital sees credits + receipts, Developer sees own job outcomes, Regulator sees audit trails (if granted), Public sees aggregate stats only
- Prevents exposure of credits as public social media-style metrics
- Ensures no dataset or patient identifiers in public data
- Interface: Access control API with role-based permissions

**Audit Repository**
- Stores compliance artifacts and audit trails
- Provides tamper-evident logging capabilities
- Supports regulatory reporting requirements
- Maintains immutable logs for forensic analysis
- Provides compliance report export API: GET /audit/export/{job-id}
- Interface: REST API for audit data retrieval

**Regulatory Interface**
- Provides standardized compliance reporting in structured JSON format
- Enables third-party attestation verification
- Supports DPDP Act reporting requirements
- Delivers judge-proof compliance statements
- Supports batch export for multiple training jobs
- Interface: REST API with regulatory data formats

**Dispute Resolution Service**
- Provides manual audit trigger API endpoint
- Freezes job state for investigation
- Enables re-verification from logs and attestation receipts
- Supports escalation to governance committee
- Maintains dispute resolution audit trail
- Interface: REST API for dispute management

### Authentication and Authorization Components

**Identity Provider (AWS Cognito)**
- Manages user authentication with OAuth 2.0 and JWT tokens
- Issues short-lived API tokens for programmatic access
- Handles session management for web portals
- Interface: OAuth 2.0 endpoints

**Authorization Service**
- Implements role-based access control (RBAC)
- Enforces roles: hospital_admin, hospital_compliance, ai_developer, regulator_readonly, system_operator
- Validates permissions on all API endpoints
- Uses IAM roles for internal service-to-service communication
- Interface: Internal authorization middleware

**API Gateway with Rate Limiting**
- Exposes public REST/GraphQL endpoints
- Implements API rate limiting per organization
- Enforces request quotas per AI_Developer organization
- Limits maximum concurrent training jobs per organization
- Deploys Web Application Firewall (WAF) for DDoS protection
- Logs and alerts on suspicious request patterns
- Interface: AWS API Gateway with WAF integration

### Data Management Components

**Hospital Data Manager**
- References hospital-controlled S3 buckets (Pramaan never accepts raw data uploads)
- Validates data availability and encryption before training
- Supports server-side encryption with hospital KMS key (SSE-KMS)
- Generates pre-signed URLs for facilitated upload (optional)
- Validates dataset hash against manifest specification
- Interface: S3 integration API

**Dataset Version Manager**
- Tracks dataset versions with cryptographic hashes
- Enforces dataset immutability per manifest version
- References dataset hash, temporal range, sample size metadata in manifests
- Requires new manifest version for dataset updates
- Interface: Internal versioning service

**Model Version Manager**
- Generates model version hash for each trained model
- Generates baseline model hash for each training job
- Ties credit minting to unique combination: (AI_Developer, Model Hash, Dataset Version Hash)
- Prevents duplicate credit minting for identical model+dataset combinations
- Includes version hashes in attestation receipts
- Interface: Internal versioning service

### Cost Management Components

**Cost Estimator**
- Provides cost estimation API before job submission
- Calculates AWS compute costs based on instance type and training time
- Interface: REST API for cost estimation

**Cost Tracker**
- Tracks actual compute costs per training job
- Includes compute cost in attestation receipt for transparency
- Enforces developer payment responsibility for all training compute costs
- Interface: Internal cost tracking service integrated with AWS billing

## Data Models

### Protocol Manifest Schema

```json
{
  "manifestId": "string (UUID)",
  "version": "string (semver)",
  "manifestHash": "string (SHA-256 hash for immutability)",
  "templateType": "enum (diagnostic-classification-public-health, diagnostic-classification-research, segmentation, risk-scoring)",
  "hospital": {
    "id": "string",
    "name": "string",
    "publicKey": "string (PEM)",
    "digiLockerVerification": {
      "registrationCertificate": "string (document ID)",
      "governmentAffiliation": "string (document ID)",
      "authorizationLetter": "string (document ID)",
      "verificationStatus": "enum (verified, pending, failed)",
      "verificationTimestamp": "timestamp"
    }
  },
  "purposeLimitation": {
    "allowedPurposes": ["string"],
    "prohibitedUses": ["string"],
    "dataRetentionPeriod": "duration"
  },
  "datasetScope": {
    "dataTypes": ["string"],
    "patientCriteria": "object",
    "temporalRange": {
      "startDate": "date",
      "endDate": "date"
    },
    "sampleSize": "integer",
    "datasetVersionHash": "string (SHA-256)",
    "s3BucketLocation": "string (hospital-controlled S3 bucket URI)",
    "encryptionMethod": "enum (SSE-KMS)",
    "hospitalKmsKeyId": "string (AWS KMS key ID)"
  },
  "privacyGuardrails": {
    "epsilonBudget": "number",
    "deltaBudget": "number",
    "minimumGroupSize": "integer",
    "suppressionThreshold": "integer"
  },
  "successMetrics": {
    "primaryMetric": "string",
    "baselineMetric": "number",
    "threshold": "number",
    "evaluationMethod": "string",
    "normalizationFormula": "string (e.g., NIS = (NewMetric - BaselineMetric) / (1 - BaselineMetric))"
  },
  "incentiveLogic": {
    "creditBands": [
      {
        "nisRange": {"min": "number", "max": "number"},
        "creditsAwarded": "integer"
      }
    ],
    "maxCreditsPerRun": "integer (e.g., 500)",
    "maxCreditsPerHospitalPerModelPerYear": "integer (e.g., 5000)",
    "institutionalRecipient": "string (Hospital/Data Fiduciary ID)",
    "preventDuplicateMinting": {
      "trackingKey": "string (AI_Developer + Model Hash + Dataset Version Hash)",
      "duplicatePolicy": "enum (reject, diminishing-returns-future)"
    }
  },
  "redemptionMechanics": {
    "modelAccessRequired": "boolean",
    "modelAccessTerms": {
      "type": "enum (api-inference-credits)",
      "cappedCredits": "integer",
      "timeBound": "duration",
      "usageRestriction": "enum (public-sector-only)",
      "developerHostedEndpoint": "string (URL)",
      "usageMetering": "enum (signed-usage-receipts, api-gateway-logs)",
      "creditDeductionPerCall": "number"
    },
    "optionalInstitutionalAllocation": "string (program-level public health fund)"
  },
  "costModel": {
    "computeCostResponsibility": "enum (ai-developer)",
    "estimatedComputeCost": "number (USD)",
    "platformFee": "number (percentage, optional for MVP)"
  },
  "developerAcceptance": {
    "acceptanceRequired": "boolean",
    "acceptanceType": "enum (binary-accept-decline)",
    "noNegotiation": "boolean (true)"
  },
  "signature": "string (cryptographic signature)",
  "createdAt": "timestamp",
  "immutableAfterJobStart": "boolean (true)"
}
```

### Training Request Schema

```json
{
  "requestId": "string (UUID)",
  "manifestId": "string (UUID)",
  "manifestVersion": "string (semver)",
  "manifestHash": "string (SHA-256 for verification)",
  "aiDeveloper": {
    "id": "string",
    "name": "string",
    "publicKey": "string (PEM)",
    "manifestAcceptance": {
      "accepted": "boolean",
      "acceptanceTimestamp": "timestamp",
      "acceptanceSignature": "string (cryptographic signature)"
    }
  },
  "enclaveImageFile": {
    "eifHash": "string (SHA-256 hash)",
    "eifSize": "integer (bytes)",
    "eifUploadTimestamp": "timestamp",
    "containsProprietaryCode": "boolean (true - not inspected by controller)",
    "containsBaselineModel": "boolean (true)",
    "baselineModelHash": "string (SHA-256 - computed inside enclave)"
  },
  "modelSpecification": {
    "architecture": "string",
    "hyperparameters": "object",
    "trainingConfig": "object"
  },
  "computeRequirements": {
    "instanceType": "string",
    "maxTrainingTime": "duration",
    "memoryRequirements": "string"
  },
  "status": "enum (pending, approved, rejected, executing, completed, failed)",
  "failureReason": "string (optional - populated if status is failed)",
  "atomicExecution": "boolean (true - no partial completion)",
  "submissionTimestamp": "timestamp",
  "signature": "string (cryptographic signature)"
}
```

### Attestation Receipt Schema

```json
{
  "receiptId": "string (UUID)",
  "requestId": "string (UUID)",
  "manifestId": "string (UUID)",
  "manifestHash": "string (SHA-256 - prevents retroactive edits)",
  "executionSummary": {
    "startTime": "timestamp",
    "endTime": "timestamp",
    "enclaveId": "string",
    "enclaveAttestation": "string (base64)",
    "eifHash": "string (SHA-256 - verified but not inspected)"
  },
  "policyEnforcement": {
    "purposeCompliance": "boolean",
    "datasetCompliance": "boolean",
    "privacyCompliance": "boolean"
  },
  "privacyAccounting": {
    "epsilonUsed": "number",
    "deltaUsed": "number",
    "privacyProof": "string (cryptographic proof)"
  },
  "dataDestruction": {
    "destructionTimestamp": "timestamp",
    "destructionProof": "string (cryptographic proof)"
  },
  "performanceResults": {
    "baselineMetric": "number",
    "baselineModelHash": "string (SHA-256)",
    "newMetric": "number",
    "trainedModelHash": "string (SHA-256)",
    "datasetVersionHash": "string (SHA-256)",
    "normalizedImprovementScore": "number (NIS)",
    "nisFormula": "string (e.g., (NewMetric - BaselineMetric) / (1 - BaselineMetric))",
    "creditsAwarded": "integer",
    "creditBandApplied": "string",
    "institutionalRecipient": "string (Hospital/Data Fiduciary ID)",
    "noIndividualPatientTracking": "boolean (true)",
    "duplicateMintingPrevention": {
      "trackingKey": "string (AI_Developer + Model Hash + Dataset Version Hash)",
      "isDuplicate": "boolean",
      "previousJobId": "string (UUID, if duplicate)"
    }
  },
  "redemptionDetails": {
    "modelAccessGranted": "boolean",
    "apiInferenceCredits": "integer",
    "timeBound": "duration",
    "usageRestriction": "string (public-sector-only)",
    "developerHostedEndpoint": "string (URL)",
    "apiTokenIssued": "string (time-bound token)",
    "usageMetering": "enum (signed-usage-receipts, api-gateway-logs)",
    "creditDeductionPerCall": "number",
    "remainingCredits": "integer",
    "noLifetimeFreeAccess": "boolean (true)",
    "noCashOut": "boolean (true)"
  },
  "costAccounting": {
    "computeCostUSD": "number",
    "computeCostResponsibility": "enum (ai-developer)",
    "platformFee": "number (optional)",
    "costTransparency": "boolean (true)"
  },
  "executionStatus": {
    "jobStatus": "enum (completed, failed)",
    "failureReason": "string (optional)",
    "atomicExecution": "boolean (true)",
    "noPartialCredits": "boolean (true)"
  },
  "cryptographicProofs": {
    "executionIntegrity": "string (merkle proof)",
    "outputConstraints": "string (proof)",
    "chainOfCustody": "string (proof)",
    "symmetricConfidentiality": "string (proof - both hospital data and developer IP protected)"
  },
  "visibilityControl": {
    "hospitalVisible": "boolean (true)",
    "developerVisible": "boolean (true - own job only)",
    "regulatorVisible": "boolean (if granted)",
    "publicVisible": "boolean (aggregate stats only, no identifiers)"
  },
  "signature": "string (hardware-backed signature)",
  "timestamp": "timestamp",
  "complianceStatements": [
    "Credits are not money and cannot be cashed out",
    "No individual patient tracking for credit allocation",
    "Model access is standardized as capped API/inference credits",
    "Developers accept manifest or decline; no post-hoc negotiation",
    "Institutional verification is document-based via DigiLocker; Pramaan does not access Aadhaar data"
  ]
}
```

## Social Credit Governance Model

### What Social Credits Are

Social Credits (SC) are **non-monetary, non-transferable, auditable units of verified impact** that represent institutional contribution to measurable model performance improvement.

**Key Characteristics:**
- NOT cash, NOT cryptocurrency for speculation, NOT tradable
- NOT held by individuals or tracked at patient level
- Protocol-level accounting units representing "verified contribution" of a data fiduciary's dataset
- Benefit institutions and public-interest entities, not individuals

### Credit Minting Logic (Anti-Gaming)

**Core Principle:** Credits are minted ONLY based on verified outcome improvement, not based on data quantity, age, or claimed quality directly.

**Deterministic Minting Process:**
1. Baseline metrics computed inside enclave
2. Trained model metrics computed inside enclave
3. Normalized Improvement Score (NIS) calculated: `NIS = (NewMetric - BaselineMetric) / (1 - BaselineMetric)`
4. NIS mapped to credits via protocol-defined credit bands
5. Hard caps enforced automatically

**Why Normalization Matters:**
Raw metric deltas can be misleading (0.02 improvement from 0.50 baseline vs. 0.98 baseline have very different significance). NIS normalization ensures:
- Same delta "worth more" when baseline is low
- Same delta "worth less" when baseline is already high
- Consistent valuation across different performance regimes

**Protocol-Defined Constraints:**
- Credit bands map NIS ranges to SC amounts (hospitals cannot arbitrarily assign)
- Max credits per training run (e.g., 500 SC)
- Max credits per hospital per model per year (e.g., 5,000 SC)
- Hospitals choose a template, not the numeric credit table

### Credit Visibility Model

**Hospital/Data Fiduciary:** Full visibility to credits earned and attestation receipts

**AI Developer:** Sees credit impact outcome only for their own training jobs

**Regulator/Auditor:** Read-only access to receipts and audit trails (when granted)

**Public:** Optional aggregate statistics only (no dataset or patient identifiers)

**NOT Public Metrics:** Credits are NOT exposed as social media-style follower counters or public leaderboards

### Credit Redemption (v1 Final Decision)

**Definition:** "Redeem" = convert credits into predefined institutional benefits, NOT cash-out, NOT individual payments, NOT trading

**Primary Redemption Mechanism:**
- **Model Access** = Capped, time-bound API/inference credits for public-sector usage only
- NO lifetime free access
- NO unlimited licensing
- NO weight/code transfer

**Why This Design:**
- Prevents business clash: AI developers won't agree to "free forever"
- Protects developer IP: model stays under developer control
- Creates deterministic economics: API credits have measurable cost
- Eliminates negotiation ambiguity: single standardized access form

**Developer Acceptance Model:**
- Hospital chooses whether Model Access is required in manifest
- Developer decision is binary: accept manifest terms OR decline
- NO post-hoc negotiation of redemption terms
- NO runtime choice among multiple redemption types

**Optional Parallel Mechanisms:**
- Institutional allocation records (e.g., program-level public health fund allocation)
- No monetary transfer required in MVP

### Judge-Proof Compliance Statements

All attestation receipts include:
- "Credits are not money and cannot be cashed out"
- "No individual patient tracking for credit allocation"
- "Model access is standardized as capped API/inference credits to avoid IP risk and ensure adoption"
- "Developers accept a manifest or decline; no post-hoc negotiation"
- "Institutional verification is document-based via DigiLocker; Pramaan does not access Aadhaar data"

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system—essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

Based on the prework analysis, I've identified the following testable properties while eliminating redundancy:

**Property 1: Protocol Manifest Validation**
*For any* Protocol Manifest, the system should validate it against the template-defined schema and ensure all required fields (purpose limitation, dataset scope, privacy guardrails, success metrics, incentive logic with credit bands and caps, redemption mechanics) are present and valid
**Validates: Requirements 1.1, 1.2, 7.2, 7.3**

**Property 2: Manifest Immutability After Job Start**
*For any* training job execution, the Protocol Manifest version used must remain immutable throughout execution, with manifest hash verification preventing retroactive edits
**Validates: Requirements 7.5, 7.6**

**Property 2: Manifest Immutability After Job Start**
*For any* training job execution, the Protocol Manifest version used must remain immutable throughout execution, with manifest hash verification preventing retroactive edits
**Validates: Requirements 7.5, 7.6**

**Property 3: Request-Manifest Compliance**
*For any* training request and Protocol Manifest pair, the system should correctly validate the request against manifest constraints and reject requests that violate policy parameters
**Validates: Requirements 1.3, 1.4**

**Property 4: Binary Developer Acceptance**
*For any* training request, the AI Developer acceptance must be binary (accept all manifest terms or decline), with no post-hoc negotiation allowed
**Validates: Requirements 7.9, 7.10**

**Property 5: Policy Violation Response**
*For any* detected policy violation during execution, the system should terminate execution and generate violation attestations
**Validates: Requirements 1.5**

**Property 6: Enclave Image File Protection**
*For any* developer-submitted Enclave Image File (.eif), the system should validate the .eif hash but NOT inspect internal code, weights, or evaluation logic, ensuring developer IP protection
**Validates: Requirements 2.6, 2.7**

**Property 7: Network Isolation During Training**
*For any* training execution within the secure enclave, all network egress attempts should be denied by default
**Validates: Requirements 2.2**

**Property 8: Data Destruction After Training**
*For any* completed training session, all decrypted healthcare data should be destroyed from memory and not persist to storage
**Validates: Requirements 2.3, 2.4**

**Property 9: Integrity Compromise Response**
*For any* enclave integrity compromise event, the system should halt execution and generate security alerts
**Validates: Requirements 2.5**

**Property 10: Differential Privacy Application**
*For any* training execution, differential privacy should be applied during gradient computation using DP-SGD with proper noise addition
**Validates: Requirements 3.1**

**Property 11: Privacy Budget Management**
*For any* training session, the privacy budget should be initialized from manifest specifications, tracked throughout training, and cause training termination when exhausted, with cryptographic proof of usage generated
**Validates: Requirements 3.2, 3.3, 3.4, 3.5**

**Property 9: Model Output Privacy Protection**
*For any* trained model, output perturbation should be applied to parameters and differential privacy guarantees should be validated before release
**Validates: Requirements 4.1, 4.2**

**Property 10: Model Internal Protection**
*For any* trained model, internal components that could leak training data should be prevented from external extraction
**Validates: Requirements 4.4**

**Property 11: Output Constraint Attestation**
*For any* model output, attestations proving output constraint compliance should be generated
**Validates: Requirements 4.5**

**Property 12: Normalized Improvement Score Calculation**
*For any* completed training, the system should compute Normalized Improvement Score using the formula NIS = (NewMetric - BaselineMetric) / (1 - BaselineMetric) to account for diminishing returns at high baseline performance
**Validates: Requirements 5.2**

**Property 13: Deterministic Credit Minting with Caps**
*For any* training execution, Social Credits should be minted deterministically based on protocol-defined credit bands mapping NIS ranges to credit amounts, with enforcement of hard caps (max per run, max per hospital per model per year), and allocation to institutional recipients only (no individual patient tracking)
**Validates: Requirements 5.3, 5.4, 5.7, 5.8, 5.9**

**Property 14: Credit Settlement to Institutional Recipients**
*For any* credit allocation, the Settlement Ledger should record transactions with institutional recipients (Hospital/Data Fiduciary) and NOT track individual patient-level allocations
**Validates: Requirements 5.5**

**Property 15: Standardized Model Access Redemption**
*For any* redemption request, the system should provide only standardized Model Access (capped, time-bound API/inference credits for public-sector usage), with no lifetime free access, no unlimited licensing, and no cash-out capability
**Validates: Requirements 6A.2, 6A.3, 6A.4, 6A.8**

**Property 15: Standardized Model Access Redemption**
*For any* redemption request, the system should provide only standardized Model Access (capped, time-bound API/inference credits for public-sector usage), with no lifetime free access, no unlimited licensing, and no cash-out capability
**Validates: Requirements 6A.2, 6A.3, 6A.4, 6A.8**

**Property 16: Comprehensive Attestation Generation**
*For any* system execution, time-stamped Attestation Receipts should be generated with cryptographic signatures, including proofs of differential privacy compliance, data destruction, execution integrity using hardware-backed keys and merkle proofs, manifest hash references, and judge-proof compliance statements
**Validates: Requirements 5.6, 6.1, 6.2, 6.3, 6A.8, 8.1, 8.3**

**Property 17: Visibility Control Enforcement**
*For any* credit or attestation data, the system should enforce role-based visibility (Hospital sees credits+receipts, Developer sees own job only, Regulator sees audit trails if granted, Public sees aggregate stats only with no identifiers)
**Validates: Requirements 6.6, 6.7, 6.8, 6.9, 6.10**

**Property 18: Audit Compliance**
*For any* audit request, the system should provide verifiable compliance artifacts and maintain immutable logs of all system interactions
**Validates: Requirements 6.4, 6.5**

**Property 19: Manifest Versioning and Notifications**
*For any* Protocol Manifest update, the system should support versioning for iterative refinement and notify affected AI Developers of changes
**Validates: Requirements 7.3, 7.7**

**Property 20: Template Functionality**
*For any* common healthcare use case, Protocol Manifest templates should be available with predefined metrics, normalization, credit mapping, and redemption mechanics
**Validates: Requirements 7.1, 7.2**

**Property 21: DigiLocker Institutional Verification**
*For any* hospital registration, the system should verify institutional authority via DigiLocker document validation (registration certificates, government affiliation, authorization letters) without storing Aadhaar numbers or processing biometric data
**Validates: Requirements 9.6, 9.7, 9.8**

**Property 22: Public Key Infrastructure**
*For any* attestation verification request, the PKI should enable third-party verification without system access and maintain cryptographic chain of custody
**Validates: Requirements 8.2, 8.4, 8.5**

**Property 23: Healthcare Data Format Support**
*For any* healthcare data in standard formats (HL7 FHIR, DICOM), the system should correctly parse, validate integrity and completeness, and encrypt using hospital-controlled keys
**Validates: Requirements 9.1, 9.2, 9.3**

**Property 24: Secure API Functionality**
*For any* API request for Protocol Manifest management or healthcare system integration, the system should implement proper security controls and provide detailed error diagnostics for failures
**Validates: Requirements 7.8, 9.4, 9.5**

**Property 25: Symmetric Confidentiality Protection**
*For any* training execution, the system should ensure only aggregated outputs leave the enclave (verified metrics, NIS, credits, receipts) and NOT persist proprietary developer code, weights, or research artifacts outside the enclave
**Validates: Requirements 2.8, 2.9**

**Property 26: System Monitoring and Alerting**
*For any* system operation, real-time monitoring should track enclave health and performance, generate alerts for violations and incidents, and trigger automated incident response procedures
**Validates: Requirements 10.1, 10.2, 10.3**

**Property 27: Dashboard and Audit Logging**
*For any* system metric or interaction, dashboards should display accurate tracking information and audit logs should maintain tamper-evident properties for forensic analysis
**Validates: Requirements 10.4, 10.5**

**Property 28: Authentication and Authorization**
*For any* API request, the system should authenticate users via AWS Cognito (OAuth 2.0/JWT) and enforce role-based access control with proper permission validation
**Validates: Requirements 11.1, 11.2, 11.3, 11.4, 11.5, 11.6**

**Property 29: Hospital Data Upload Security**
*For any* healthcare data, the hospital should upload directly to hospital-controlled S3 bucket with SSE-KMS encryption, and Pramaan should never accept raw data uploads
**Validates: Requirements 12.1, 12.2, 12.3, 12.4, 12.7**

**Property 30: Baseline Model Validation**
*For any* training job, the baseline model should be evaluated inside the enclave with metrics computed internally, and no external baseline claims should be accepted
**Validates: Requirements 13.2, 13.3, 13.4, 13.5**

**Property 31: Credit Redemption Implementation**
*For any* Model Access redemption, the system should issue time-bound API tokens with capped limits, implement usage metering, deduct credits per call, and restrict access to public-sector endpoints
**Validates: Requirements 14.2, 14.3, 14.4, 14.5, 14.6, 14.7**

**Property 32: Atomic Training Execution**
*For any* training job failure, the system should mark job as failed, not mint credits, notify stakeholders, and allow re-submission without partial credit allocation
**Validates: Requirements 15.1, 15.2, 15.3, 15.4, 15.5, 15.6**

**Property 33: Single Hospital Training Scope**
*For any* training job, the system should enforce single hospital per job and reject multi-hospital collaboration requests in v1
**Validates: Requirements 16.1, 16.2, 16.3**

**Property 34: Duplicate Minting Prevention**
*For any* training submission, the system should generate unique tracking key (AI_Developer + Model Hash + Dataset Version Hash) and prevent duplicate credit minting for identical combinations
**Validates: Requirements 17.4, 17.5, 17.7**

**Property 35: Dataset Immutability**
*For any* manifest version, the dataset should be immutable with hash validation, and dataset updates should require new manifest versions
**Validates: Requirements 18.2, 18.3, 18.4, 18.5**

**Property 36: Rate Limiting and Abuse Prevention**
*For any* API access, the system should enforce rate limits, request quotas per organization, concurrent job limits, and WAF protection
**Validates: Requirements 20.1, 20.2, 20.3, 20.4, 20.5**

**Property 37: Cost Transparency**
*For any* training job, the system should provide cost estimation before submission, track actual costs, and include costs in attestation receipts with developer payment responsibility
**Validates: Requirements 21.1, 21.2, 21.3, 21.4, 21.5, 21.7**

**Property 38: Compliance Report Export**
*For any* compliance export request, the system should generate structured JSON reports with manifest ID, purpose, dataset scope, privacy budget, attestation hash, credit events, and cryptographic signatures
**Validates: Requirements 22.1, 22.2, 22.3, 22.4, 22.5, 22.6**

## Implementation Decisions and Scope

### v1 MVP Decisions (Locked)

**Authentication:** AWS Cognito with OAuth 2.0/JWT, RBAC with 5 roles

**Data Upload:** Hospital-controlled S3 buckets, SSE-KMS encryption, Pramaan never accepts raw data

**Baseline Models:** Developer-provided inside .eif, enclave-validated, no external claims

**Credit Redemption:** Developer-hosted API, Pramaan-issued time-bound tokens, usage metering, credit deduction per call

**Failure Recovery:** Atomic training (no checkpoints), failed jobs mint no credits, re-submission allowed

**Training Scope:** Single hospital per job (no federated learning in v1)

**Model Versioning:** Duplicate minting prevention via (Developer + Model Hash + Dataset Hash) tracking

**Dataset Versioning:** Immutable per manifest version, updates require new manifest

**Dispute Resolution:** Manual audit trigger API, job state freeze, governance escalation

**Rate Limiting:** API Gateway rate limits, per-org quotas, concurrent job limits, WAF protection

**Cost Model:** Developer pays all compute costs, optional platform fee (future)

**Compliance Export:** Structured JSON via GET /audit/export/{job-id}, cryptographically signed

### Future Enhancements (Explicitly Deferred)

**Pramaan-Federated:** Multi-hospital collaboration, federated learning

**Advanced Failure Recovery:** Checkpoint/resume capability for long training jobs

**Diminishing Returns:** Credit bands for repeated model+dataset combinations

**Client-Side Encryption:** Advanced hospital data encryption before S3 upload

**Baseline Registry:** Protocol-provided standardized baseline models

**Platform Fee Model:** SaaS subscription or percentage-based platform fees

**Advanced Dispute Resolution:** Automated re-verification, smart contract arbitration

**Production Hardening:** Full AWS service integration, multi-region deployment, disaster recovery

## Error Handling

The Pramaan-Health system implements comprehensive error handling across all layers:

**Policy Layer Error Handling:**
- Invalid Protocol Manifests are rejected with detailed validation errors
- Malformed training requests return structured error responses
- Policy violations during execution trigger immediate termination with attestation generation

**Execution Layer Error Handling:**
- Enclave integrity failures halt all operations and generate security alerts
- Privacy budget exhaustion terminates training gracefully with partial results
- Data decryption failures prevent training initiation and log security events
- Network isolation violations are blocked and logged as security incidents

**Incentive Layer Error Handling:**
- Credit calculation failures are logged and require manual review
- Settlement ledger transaction failures trigger retry mechanisms with exponential backoff
- Performance evaluation errors prevent credit allocation and generate audit events

**Trust & Compliance Layer Error Handling:**
- Attestation generation failures prevent result release and trigger investigation
- Audit log corruption is detected through cryptographic integrity checks
- Regulatory reporting failures are escalated to compliance officers with detailed diagnostics

**Recovery Mechanisms:**
- Automatic retry for transient failures with circuit breaker patterns
- Graceful degradation for non-critical component failures
- Manual intervention workflows for critical system failures
- Comprehensive logging for forensic analysis and system debugging

## Technology Stack

### Programming Language
**Primary Language: Python**
- Well-suited for differential privacy training (DP-SGD) and ML workflows
- Excellent property-based testing support with Hypothesis library
- Rapid prototyping capabilities for research-grade systems
- Rich ecosystem for confidential computing and DP research tooling

**Secondary Languages (Future Scope):**
- TypeScript for lightweight admin or policy-authoring interfaces
- Infrastructure as Code (IaC) and CI/CD tooling (out of scope for pilot)

### AWS-Powered Architecture (Pilot Scope)

**1. Secure Execution & Compute**
- **AWS Nitro Enclaves**: Hardware-isolated secure execution environment for confidential AI training
- **Amazon EC2 (Parent Instance)**: Hosts the enclave and orchestrates execution lifecycle

**2. Identity, Keys & Trust**
- **AWS KMS**: Manages encryption keys, releases keys only after enclave attestation (PCR-based)
- **Hardware-backed Attestation**: Verifies enclave integrity before data access

**3. Data Storage (Governed Access)**
- **Amazon S3**: Encrypted storage for healthcare datasets with strict policy-controlled access
- **S3 Object Lock/Versioning** (conceptual): Supports auditability and immutability requirements

**4. Policy & Authorization**
- **Policy-as-Code (Manifest-driven)**: Protocol Manifest defines consent, scope, privacy, and incentives
- **Amazon Verified Permissions/Cedar** (conceptual): Inspiration for fine-grained authorization logic

**5. Privacy & ML**
- **Differential Privacy (DP-SGD)**: Applied during training to prevent memorization
- **Privacy Accounting (RDP-based)**: Tracks epsilon/delta consumption throughout training

**6. Evaluation & Attestation**
- **Enclave-internal Model Evaluator**: Computes AUROC/F1 against baseline models
- **Cryptographic Attestation Generator**: Produces signed Attestation Receipts

**7. Incentives & Audit**
- **Amazon Managed Blockchain (Hyperledger Fabric)**: Records Social Credit settlements on permissioned, regulator-friendly ledger
- **Immutable Audit Logs** (conceptual): Stores receipts and compliance artifacts

### Implementation Scope
This is an **architecture-first pilot** and demonstration, not a full production deployment. The focus is on:
- Correctness and enforcement logic
- Trust guarantees and verifiable compliance
- Property-based testing of core protocols
- Architectural validation of the four-layer design

Not all AWS services need to be fully deployed or wired end-to-end for the pilot implementation.

## Testing Strategy

The Pramaan-Health system employs a dual testing approach combining unit tests and property-based tests for comprehensive coverage:

**Property-Based Testing:**
- Each correctness property will be implemented as a property-based test using Hypothesis (Python)
- Minimum 100 iterations per property test to ensure statistical confidence
- Tests will generate random inputs (manifests, requests, data) to verify universal properties
- Each test will be tagged with: **Feature: pramaan-health, Property {number}: {property_text}**
- Property tests focus on universal correctness guarantees across all valid inputs

**Unit Testing:**
- Specific examples and edge cases for each component
- Integration tests for component interactions
- Error condition testing for all failure modes
- Mock testing for external dependencies (AWS Nitro, blockchain)
- Unit tests complement property tests by testing concrete scenarios

**Security Testing:**
- Penetration testing of secure enclave boundaries
- Cryptographic verification of attestation mechanisms
- Privacy leakage testing for differential privacy implementation
- Network isolation verification for enclave environment

**Compliance Testing:**
- DPDP Act compliance verification through audit trail testing
- Regulatory reporting format validation
- Third-party attestation verification testing
- Chain of custody integrity verification

**Performance Testing:**
- Training execution performance under various data sizes
- Privacy budget consumption rate analysis
- Attestation generation latency measurement
- System scalability testing for multiple concurrent training sessions

The testing strategy ensures that both functional correctness and security guarantees are thoroughly validated before deployment.

## Demo Flow for Judges

This 3-minute demonstration showcases the complete Pramaan-Health protocol in action:

### Demo Scenario
**Use Case:** A district hospital wants to contribute diabetic retinopathy screening data for AI model training while maintaining patient privacy and DPDP Act compliance.

### Flow (3 Minutes Total)

**Step 1: Hospital Creates Manifest** (30 seconds)
- Hospital admin logs into Hospital Portal
- Selects "Create Manifest" 
- Chooses template: "Diagnostic Classification - Public Health"
- Configures:
  - Purpose: Diabetic retinopathy detection improvement
  - Dataset scope: 10,000 retinal images, 2020-2024
  - Privacy budget: ε=3.0, δ=1e-5
  - Success metric: AUROC > 0.85
  - Model Access: Required (500 API credits, 6 months, public-sector only)
- Clicks "Publish Manifest"
- System validates and publishes manifest with cryptographic signature

**Step 2: AI Developer Submits Training Request** (30 seconds)
- Developer logs into Developer Portal
- Browses available manifests, finds diabetic retinopathy manifest
- Reviews terms: privacy budget, success criteria, Model Access redemption
- Clicks "Accept Terms" (binary decision, no negotiation)
- Uploads Enclave Image File (.eif) containing proprietary training code
- Submits training request
- System validates .eif hash and queues job

**Step 3: Backend Execution** (1 minute - show logs/monitoring)
- Enclave orchestrator provisions AWS Nitro Enclave
- KMS releases decryption keys after attestation verification (PCR-based)
- Data decrypted in enclave memory only
- DP-SGD training executes with privacy budget tracking
- Baseline model: AUROC = 0.78
- Trained model: AUROC = 0.88
- NIS calculation: (0.88 - 0.78) / (1 - 0.78) = 0.45
- Credit minting: NIS 0.45 → 350 Social Credits (protocol-defined band)
- Data destruction proof generated
- Network isolation verified (no egress)

**Step 4: Attestation Receipt Generation** (30 seconds)
- System generates comprehensive Attestation Receipt with:
  - Cryptographic signature (hardware-backed)
  - Privacy accounting proof: ε=2.8 used, δ=8e-6 used
  - Data destruction timestamp and proof
  - Performance results: AUROC 0.88, NIS 0.45, 350 SC awarded
  - Merkle proof of execution integrity
  - Manifest hash reference (prevents retroactive edits)
  - Judge-proof compliance statements
- Receipt stored in immutable audit repository
- Credits recorded on Settlement Ledger (Hyperledger Fabric)
- Hospital receives 350 SC (institutional allocation, no patient tracking)

**Step 5: API Integration Demo** (30 seconds)
- Show API documentation (Swagger/OpenAPI)
- Demonstrate programmatic access:
  - `GET /receipt/{id}` - Third-party verification
  - `GET /credits/{hospital-id}` - Credit balance query
  - `POST /verify-receipt` - Cryptographic verification
- Highlight: No UI required for integration, pure protocol access

### What Judges See

**Technical Depth:**
- Real Nitro Enclave integration (not simulated)
- Real DP-SGD implementation (Opacus)
- Deterministic credit minting with NIS normalization
- Hardware-backed cryptographic attestation

**Protocol Design:**
- Template-driven manifests (no free-form contracts)
- Binary developer acceptance (no negotiation)
- Standardized redemption (no IP conflicts)
- Symmetric confidentiality (hospital data + developer IP)

**Compliance & Trust:**
- DPDP Act compliance artifacts
- Immutable audit trails
- Third-party verifiable attestations
- Judge-proof compliance statements

**Infrastructure Thinking:**
- API-first design (like UPI)
- Minimal UI (protocol is the product)
- Extensible for future integrations
- Clear separation of concerns across 4 layers

### What Judges DON'T See
- Pixel-perfect dashboards
- Complex user workflows
- Regulator UI (API-only is sufficient)
- Production-scale deployment (pilot scope is clear)

This demo proves: **Pramaan-Health is real infrastructure, not vaporware.**