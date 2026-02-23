# Requirements Document

## Introduction

Pramaan-Health is a pilot implementation of the Pramaan AI protocol that enables confidential AI training on healthcare data without exposing raw sensitive information. The system provides a trust layer that allows AI models to learn from hospital data while maintaining data sovereignty, privacy compliance, and verifiable enforcement of data usage policies.

## Glossary

- **Pramaan_System**: The complete confidential AI training protocol implementation
- **Hospital**: Healthcare institution that owns and controls sensitive patient data (Data Fiduciary)
- **AI_Developer**: Entity seeking to train AI models on healthcare data (Data Consumer)
- **Protocol_Manifest**: Machine-readable, template-driven legal-technical contract defining data usage terms
- **Secure_Enclave**: Hardware-isolated execution environment (AWS Nitro Enclave)
- **Attestation_Receipt**: Cryptographic proof of policy compliance and execution
- **Social_Credits**: Non-monetary, non-transferable, auditable units of verified impact representing institutional contribution to measurable model performance improvement. Credits are NOT cash, NOT tradable, and NOT held by individuals.
- **Normalized_Improvement_Score (NIS)**: Standardized metric quantifying model performance improvement relative to baseline, accounting for diminishing returns at high baseline performance
- **Model_Access**: Standardized redemption mechanism providing capped, time-bound API/inference credits for public-sector usage only
- **Differential_Privacy**: Mathematical privacy guarantee limiting information leakage
- **Settlement_Ledger**: Permissioned blockchain recording credit transactions
- **DPDP_Act**: Digital Personal Data Protection Act (India's privacy regulation)
- **DigiLocker**: Government document verification system used for institutional authentication (not Aadhaar-based)
- **Enclave_Image_File (.eif)**: Developer-submitted containerized code for secure execution, protecting developer IP

## Requirements

### Requirement 1: Policy Definition and Enforcement

**User Story:** As a Hospital, I want to define machine-readable data usage policies, so that I can programmatically enforce how my sensitive data is used for AI training.

#### Acceptance Criteria

1. WHEN a Hospital creates a Protocol Manifest, THE Pramaan_System SHALL validate it against the complete policy schema
2. THE Protocol_Manifest SHALL specify purpose limitation, dataset scope, privacy guardrails, success metrics, and incentive logic
3. WHEN an AI_Developer requests data access, THE Pramaan_System SHALL verify their request against the Protocol_Manifest constraints
4. THE Pramaan_System SHALL reject any training request that violates the defined policy parameters
5. WHEN policy violations are detected, THE Pramaan_System SHALL terminate execution and generate violation attestations

### Requirement 2: Secure Execution Environment

**User Story:** As a Hospital, I want AI training to occur in a hardware-isolated environment with symmetric confidentiality, so that my raw data never leaves the secure boundary and developer IP is equally protected.

#### Acceptance Criteria

1. THE Secure_Enclave SHALL decrypt healthcare data only within enclave memory
2. THE Secure_Enclave SHALL deny all network egress by default during training execution
3. WHEN training completes, THE Secure_Enclave SHALL destroy all decrypted data from memory
4. THE Secure_Enclave SHALL prevent any persistence of raw healthcare data to storage
5. WHEN enclave integrity is compromised, THE Pramaan_System SHALL halt execution and generate security alerts
6. THE AI_Developer SHALL submit code only as an Enclave_Image_File (.eif) to protect proprietary algorithms
7. THE Pramaan_System SHALL validate .eif hash but NOT inspect internal code, weights, or evaluation logic
8. THE Secure_Enclave SHALL ensure only aggregated outputs leave the enclave (verified performance metrics, normalized improvement scores, credits minted, signed receipts)
9. THE Pramaan_System SHALL NOT persist proprietary developer code, weights, or research artifacts outside the enclave

### Requirement 3: Differential Privacy Training

**User Story:** As a Hospital, I want AI training to use differential privacy techniques, so that individual patient information cannot be inferred from the trained model.

#### Acceptance Criteria

1. THE Pramaan_System SHALL apply differential privacy during gradient computation (DP-SGD)
2. WHEN training begins, THE Pramaan_System SHALL initialize the privacy budget from Protocol_Manifest specifications
3. THE Pramaan_System SHALL track privacy budget consumption throughout training
4. WHEN privacy budget is exhausted, THE Pramaan_System SHALL terminate training
5. THE Pramaan_System SHALL generate cryptographic proof of privacy budget usage

### Requirement 4: Model Output Constraints

**User Story:** As a Hospital, I want trained models to have output constraints, so that they cannot be used to reconstruct or infer sensitive training data.

#### Acceptance Criteria

1. THE Pramaan_System SHALL apply output perturbation to trained model parameters
2. THE Pramaan_System SHALL validate that model outputs satisfy differential privacy guarantees
3. WHEN model evaluation occurs, THE Pramaan_System SHALL perform it within the trusted boundary
4. THE Pramaan_System SHALL prevent extraction of model internals that could leak training data
5. THE Pramaan_System SHALL generate attestations proving output constraint compliance

### Requirement 5: Performance Evaluation and Incentives

**User Story:** As an AI_Developer, I want my model performance to be evaluated fairly within the trusted system, so that the Hospital can receive Social_Credits for verified improvements to healthcare outcomes.

#### Acceptance Criteria

1. WHEN model training completes, THE Pramaan_System SHALL evaluate performance against Protocol_Manifest success metrics within the trusted boundary
2. THE Pramaan_System SHALL compute a Normalized_Improvement_Score (NIS) using the formula: NIS = (NewMetric - BaselineMetric) / (1 - BaselineMetric) to account for diminishing returns at high baseline performance
3. THE Pramaan_System SHALL mint Social_Credits deterministically based on protocol-defined credit bands mapping NIS ranges to credit amounts
4. THE Pramaan_System SHALL enforce hard caps: maximum credits per training run (e.g., 500 SC) and maximum credits per hospital per model per year (e.g., 5,000 SC)
5. THE Pramaan_System SHALL record credit allocation on the Settlement_Ledger with institutional recipient (Hospital/Data Fiduciary), not individual patients
6. THE Pramaan_System SHALL generate performance attestations with cryptographic signatures
7. THE Social_Credits SHALL be non-monetary, non-transferable, and auditable units representing verified institutional contribution
8. THE Pramaan_System SHALL NOT allow hospitals to arbitrarily assign credit values (prevents inflation)
9. THE Pramaan_System SHALL NOT track or allocate credits at individual patient level

### Requirement 6: Compliance and Audit Trail

**User Story:** As a Hospital compliance officer, I want comprehensive audit trails and attestations with controlled visibility, so that I can demonstrate DPDP Act compliance to regulators.

#### Acceptance Criteria

1. THE Pramaan_System SHALL generate time-stamped Attestation_Receipts for all policy enforcement actions
2. THE Attestation_Receipt SHALL contain cryptographic proof of differential privacy compliance
3. THE Attestation_Receipt SHALL include proof of zero-persistence data destruction
4. WHEN audit requests are made, THE Pramaan_System SHALL provide verifiable compliance artifacts
5. THE Pramaan_System SHALL maintain immutable logs of all system interactions for regulatory review
6. THE Hospital/Data_Fiduciary SHALL have visibility to credits earned and attestation receipts
7. THE AI_Developer SHALL see credit impact outcome only for their training jobs
8. THE Regulator/Auditor SHALL have read-only access to receipts and audit trails when granted
9. THE Pramaan_System SHALL optionally publish aggregate statistics without dataset or patient identifiers
10. THE Pramaan_System SHALL NOT expose credits as public metrics or social media-style counters

### Requirement 6A: Social Credit Redemption

**User Story:** As a Hospital, I want to redeem earned Social_Credits for institutional benefits, so that verified contributions translate into measurable public health value without creating business conflicts.

#### Acceptance Criteria

1. THE Pramaan_System SHALL define "redemption" as converting credits into predefined institutional benefits, NOT cash-out, NOT individual payments, NOT trading
2. THE primary redemption mechanism SHALL be Model_Access: capped, time-bound API/inference credits for public-sector usage only
3. THE Pramaan_System SHALL NOT provide lifetime free access or unlimited licensing to protect developer IP
4. THE Pramaan_System SHALL enforce deterministic redemption economics with measurable API credit costs
5. THE AI_Developer decision SHALL be binary: accept manifest terms (including Model_Access if required) or decline participation
6. THE Pramaan_System SHALL NOT allow post-hoc negotiation of redemption terms
7. THE Pramaan_System SHALL support optional parallel institutional allocation records (e.g., program-level public health allocation) without requiring monetary transfer
8. THE Pramaan_System SHALL include clear statements: "Credits are not money and cannot be cashed out", "No individual patient tracking for credit allocation", "Model access is standardized as capped API/inference credits"

### Requirement 7: Protocol Manifest Management

**User Story:** As a Hospital, I want to create and manage Protocol Manifests using validated templates, so that I can efficiently establish data sharing agreements with AI developers without negotiation ambiguity.

#### Acceptance Criteria

1. THE Pramaan_System SHALL provide template-driven Protocol_Manifest creation for healthcare use cases (diagnostic classification - public health, diagnostic classification - research, segmentation, risk scoring)
2. THE Protocol_Manifest templates SHALL predefine allowed metrics, normalization methods, credit mapping, caps, and permissible redemption mechanics
3. WHEN a Protocol_Manifest is created, THE Pramaan_System SHALL validate all required fields against template constraints
4. THE Pramaan_System SHALL support versioning of Protocol_Manifests using semantic versioning (semver)
5. WHEN a training job starts with a manifest version, THE manifest version SHALL be immutable for that execution
6. THE Pramaan_System SHALL reference manifest hash/version in attestation receipts to prevent retroactive edits
7. WHEN Protocol_Manifests are updated, THE Pramaan_System SHALL notify affected AI_Developers
8. THE Pramaan_System SHALL NOT allow AI-generated free-form contracts; manifests are structurally constrained and rule-based
9. THE Hospital SHALL choose whether Model_Access redemption is required in the manifest
10. WHEN Model_Access is required, THE mechanism SHALL be standardized as capped, time-bound API/inference credits for public-sector usage only

### Requirement 8: Cryptographic Attestation System

**User Story:** As a regulator, I want cryptographically verifiable proofs of system behavior, so that I can audit compliance without accessing sensitive healthcare data.

#### Acceptance Criteria

1. THE Pramaan_System SHALL generate cryptographic signatures for all attestations using hardware-backed keys
2. THE Pramaan_System SHALL provide public key infrastructure for attestation verification
3. WHEN attestations are generated, THE Pramaan_System SHALL include merkle proofs of execution integrity
4. THE Pramaan_System SHALL enable third-party verification of attestations without system access
5. THE Pramaan_System SHALL maintain cryptographic chain of custody for all compliance artifacts

### Requirement 9: Healthcare Data Integration

**User Story:** As a Hospital, I want to securely integrate my existing healthcare data systems with institutional verification, so that I can participate in confidential AI training without disrupting clinical operations.

#### Acceptance Criteria

1. THE Pramaan_System SHALL support standard healthcare data formats (HL7 FHIR, DICOM)
2. WHEN healthcare data is ingested, THE Pramaan_System SHALL validate data integrity and completeness
3. THE Pramaan_System SHALL encrypt healthcare data using hospital-controlled keys
4. THE Pramaan_System SHALL provide secure APIs for healthcare system integration
5. WHEN data integration fails, THE Pramaan_System SHALL provide detailed error diagnostics
6. THE Pramaan_System SHALL verify Hospital institutional authority via DigiLocker-based document verification (registration certificates, government affiliation documents, authorization letters)
7. THE Pramaan_System SHALL NOT store Aadhaar numbers, process biometric data, or process patient identity through DigiLocker
8. THE Pramaan_System SHALL validate issuer authenticity and document validity for institutional verification

### Requirement 10: System Monitoring and Observability

**User Story:** As a system administrator, I want comprehensive monitoring of the Pramaan system, so that I can ensure reliable operation and detect security incidents.

#### Acceptance Criteria

1. THE Pramaan_System SHALL provide real-time monitoring of enclave health and performance
2. THE Pramaan_System SHALL generate alerts for policy violations, security incidents, and system failures
3. WHEN system anomalies are detected, THE Pramaan_System SHALL automatically trigger incident response procedures
4. THE Pramaan_System SHALL provide dashboards for tracking training jobs, credit allocations, and compliance status
5. THE Pramaan_System SHALL maintain audit logs with tamper-evident properties for forensic analysis

### Requirement 11: Authentication and Authorization

**User Story:** As a system operator, I want secure authentication and role-based access control, so that only authorized entities can access system functions.

#### Acceptance Criteria

1. THE Pramaan_System SHALL use AWS Cognito for OAuth 2.0 and JWT-based authentication
2. THE Pramaan_System SHALL implement role-based access control (RBAC) with roles: hospital_admin, hospital_compliance, ai_developer, regulator_readonly, system_operator
3. THE Pramaan_System SHALL issue short-lived API tokens for programmatic access
4. THE Pramaan_System SHALL use IAM role-based access for internal service-to-service communication (no static keys)
5. THE Pramaan_System SHALL enforce role-based permissions on all API endpoints
6. THE Pramaan_System SHALL implement session management for web portal access

### Requirement 12: Healthcare Data Upload and Encryption

**User Story:** As a Hospital, I want a secure mechanism to make my encrypted healthcare data available for training, so that data never leaves my control until enclave execution.

#### Acceptance Criteria

1. THE Hospital SHALL upload healthcare data directly to hospital-controlled S3 bucket
2. THE Pramaan_System SHALL NOT accept raw healthcare data uploads directly
3. THE Hospital SHALL use server-side encryption with hospital KMS key (SSE-KMS)
4. THE Pramaan_System SHALL reference hospital S3 bucket location in Protocol Manifest
5. THE Pramaan_System SHALL validate data availability and encryption before training
6. THE Pramaan_System SHALL support optional pre-signed URLs for facilitated upload (if needed)
7. FOR MVP, THE Hospital data SHALL already be encrypted in their S3 bucket before manifest creation

### Requirement 13: Baseline Model Validation

**User Story:** As a Hospital, I want baseline models to be validated fairly, so that credit minting is based on genuine improvement and not manipulated baselines.

#### Acceptance Criteria

1. THE AI_Developer SHALL provide baseline model inside the Enclave_Image_File (.eif)
2. THE Secure_Enclave SHALL evaluate baseline model performance inside the trusted boundary
3. THE Pramaan_System SHALL compute baseline metrics within enclave (not accept external claims)
4. THE Pramaan_System SHALL NOT store baseline model code or weights outside enclave
5. THE Protocol_Manifest SHALL enforce evaluation metrics that baseline must satisfy
6. THE Pramaan_System SHALL validate baseline model against protocol-defined fairness criteria

### Requirement 14: Credit Redemption Implementation

**User Story:** As an AI_Developer, I want a clear implementation of Model Access redemption, so that I can provide capped API access to hospitals without IP risk.

#### Acceptance Criteria

1. THE AI_Developer SHALL host model API endpoint under their control
2. THE Pramaan_System SHALL issue time-bound API tokens with capped inference limits
3. THE Pramaan_System SHALL implement usage metering via developer-reported signed usage receipts or API gateway logs
4. THE Pramaan_System SHALL deduct credits for each API call based on usage
5. THE Pramaan_System SHALL restrict API access to verified public-sector endpoints only
6. THE Pramaan_System SHALL prevent access after credit exhaustion or time expiration
7. THE Pramaan_System SHALL maintain audit trail of all API usage for compliance

### Requirement 15: Failure Recovery and Atomicity

**User Story:** As a Hospital, I want clear guarantees about training job completion, so that I understand what happens if execution fails.

#### Acceptance Criteria

1. THE Pramaan_System SHALL treat training jobs as atomic operations
2. WHEN enclave crashes or execution fails, THE training job SHALL be marked as failed
3. THE Pramaan_System SHALL NOT mint credits for failed training jobs
4. THE Pramaan_System SHALL notify Hospital and AI_Developer of job failure
5. THE AI_Developer MAY re-submit training request after failure
6. THE Pramaan_System SHALL NOT support partial credit allocation in v1
7. THE Pramaan_System SHALL mark checkpoint/resume capability as future enhancement

### Requirement 16: Single Hospital Training Scope

**User Story:** As a system architect, I want clear boundaries on training scope, so that v1 complexity is manageable and federated learning is deferred.

#### Acceptance Criteria

1. THE Pramaan_System SHALL support single hospital per training job in v1
2. THE Pramaan_System SHALL NOT support multi-hospital collaboration or federated learning in v1
3. THE Protocol_Manifest SHALL reference exactly one Hospital/Data Fiduciary
4. THE Pramaan_System SHALL explicitly document federated learning as future roadmap (Pramaan-Federated)

### Requirement 17: Model and Dataset Versioning

**User Story:** As a system operator, I want to prevent duplicate credit minting, so that developers cannot game the system by re-submitting identical models.

#### Acceptance Criteria

1. THE Pramaan_System SHALL generate model version hash for each trained model
2. THE Pramaan_System SHALL generate baseline model hash for each training job
3. THE Protocol_Manifest SHALL reference dataset version hash
4. THE Pramaan_System SHALL tie credit minting to unique combination: (AI_Developer, Model Hash, Dataset Version Hash)
5. WHEN identical model and dataset version are re-submitted, THE Pramaan_System SHALL NOT mint additional credits
6. THE Pramaan_System SHALL mark diminishing return bands as future enhancement
7. THE Attestation_Receipt SHALL include model version hash, baseline hash, and dataset version hash

### Requirement 18: Dataset Immutability and Versioning

**User Story:** As a Hospital, I want clear dataset versioning, so that I can update data while maintaining audit trail integrity.

#### Acceptance Criteria

1. THE Protocol_Manifest SHALL reference specific dataset version with hash
2. WHEN Hospital updates dataset, THE Hospital SHALL create new manifest version
3. THE Pramaan_System SHALL treat datasets as immutable per manifest version
4. THE Pramaan_System SHALL validate dataset hash before training execution
5. THE Attestation_Receipt SHALL reference dataset version hash for audit trail

### Requirement 19: Dispute Resolution and Manual Audit

**User Story:** As a Hospital compliance officer, I want dispute resolution mechanisms, so that I can challenge attestations if policy violations are suspected.

#### Acceptance Criteria

1. THE Pramaan_System SHALL provide manual audit trigger API endpoint
2. WHEN manual audit is requested, THE Pramaan_System SHALL freeze job state
3. THE Pramaan_System SHALL enable re-verification from logs and attestation receipts
4. THE Pramaan_System SHALL support escalation to governance committee for dispute resolution
5. THE Pramaan_System SHALL maintain dispute resolution audit trail

### Requirement 20: Rate Limiting and Abuse Prevention

**User Story:** As a system operator, I want rate limiting and abuse prevention, so that the system remains available and resistant to spam or DDoS attacks.

#### Acceptance Criteria

1. THE Pramaan_System SHALL implement API rate limiting via API Gateway
2. THE Pramaan_System SHALL enforce request quotas per AI_Developer organization
3. THE Pramaan_System SHALL limit maximum concurrent training jobs per organization
4. THE Pramaan_System SHALL deploy Web Application Firewall (WAF) in front of web portals
5. THE Pramaan_System SHALL log and alert on suspicious request patterns

### Requirement 21: Cost Model and Payment

**User Story:** As an AI_Developer, I want clear understanding of cost responsibilities, so that I can budget for training jobs appropriately.

#### Acceptance Criteria

1. THE AI_Developer SHALL pay all AWS compute costs for training execution
2. THE Hospital SHALL NOT pay for training compute costs
3. THE Pramaan_System SHALL provide cost estimation API before job submission
4. THE Pramaan_System SHALL track actual compute costs per training job
5. THE Pramaan_System SHALL include compute cost in attestation receipt for transparency
6. THE Pramaan_System MAY charge platform fee as percentage of compute cost (future enhancement)
7. THE Pramaan_System SHALL document cost model clearly in Protocol Manifest

### Requirement 22: Regulatory Compliance Export

**User Story:** As a Hospital compliance officer, I want exportable compliance reports, so that I can provide structured audit artifacts to regulators.

#### Acceptance Criteria

1. THE Pramaan_System SHALL provide compliance report export API: GET /audit/export/{job-id}
2. THE compliance report SHALL include: Manifest ID, purpose, dataset scope, privacy budget, attestation hash, credit mint event
3. THE Pramaan_System SHALL export compliance reports in structured JSON format
4. THE Pramaan_System SHALL support batch export for multiple training jobs
5. THE Pramaan_System SHALL include cryptographic signature on exported compliance reports
6. THE Pramaan_System SHALL maintain export audit trail for regulatory review

### Requirement 23: Secrets Management

**User Story:** As a system operator, I want secure secrets management, so that credentials are never exposed in code or logs.

#### Acceptance Criteria

1. THE Pramaan_System SHALL use AWS Secrets Manager for all sensitive credentials (database credentials, signing keys, API tokens)
2. THE Pramaan_System SHALL NOT store long-lived AWS keys in code, configuration files, or UI
3. THE Pramaan_System SHALL rotate secrets regularly (minimum monthly rotation)
4. THE Pramaan_System SHALL use IAM roles for service-to-service authentication (no static keys)
5. THE Pramaan_System SHALL audit all secret access via CloudTrail
6. THE Pramaan_System SHALL use KMS for signing keys where possible instead of Secrets Manager

### Requirement 24: Network Isolation and Architecture

**User Story:** As a security architect, I want proper network isolation, so that sensitive services are not exposed to public internet.

#### Acceptance Criteria

1. THE Pramaan_System SHALL deploy services in AWS VPC with private and public subnets
2. THE database and orchestrator services SHALL run in private subnets (no direct internet access)
3. THE API Gateway and load balancer SHALL run in public subnets only
4. THE Pramaan_System SHALL use VPC endpoints for S3 and KMS access (avoid public internet paths)
5. THE Pramaan_System SHALL implement security groups with least privilege network access
6. THE Pramaan_System SHALL enable VPC Flow Logs for network traffic monitoring

### Requirement 25: Supply Chain Security

**User Story:** As a security engineer, I want supply chain security controls, so that dependencies and artifacts are trustworthy.

#### Acceptance Criteria

1. THE Pramaan_System SHALL lock all Python dependencies with pinned versions (requirements.txt with hashes)
2. THE Pramaan_System SHALL use dependency scanning (GitHub Dependabot or equivalent)
3. THE Pramaan_System SHALL sign container images for deployment verification
4. THE Pramaan_System SHALL run CI checks: linting, unit tests, schema validation, security scans
5. THE Pramaan_System SHALL maintain Software Bill of Materials (SBOM) for all dependencies
6. THE Pramaan_System SHALL verify .eif image signatures before execution

### Requirement 26: Enhanced Data Protection

**User Story:** As a compliance officer, I want comprehensive data protection controls, so that PHI is never exposed in logs or error messages.

#### Acceptance Criteria

1. THE Pramaan_System SHALL encrypt all data at rest: S3 (SSE-KMS), databases (encryption at rest), EBS volumes
2. THE Pramaan_System SHALL encrypt all data in transit using TLS 1.3 (minimum TLS 1.2)
3. THE Pramaan_System SHALL enforce HSTS (HTTP Strict Transport Security) on all web endpoints
4. THE Pramaan_System SHALL use separate KMS keys by domain: hospital data keys (hospital-controlled CMK), Pramaan metadata keys (Pramaan CMK)
5. THE Pramaan_System SHALL redact all PHI from logs, error messages, and attestation receipts
6. THE Pramaan_System SHALL implement data minimization: only collect and store necessary data
7. THE Pramaan_System SHALL enable MFA for all privileged users (hospital admins, system operators)

### Requirement 27: Enhanced Monitoring and Incident Response

**User Story:** As a security operator, I want comprehensive monitoring and alerting, so that security incidents are detected and responded to quickly.

#### Acceptance Criteria

1. THE Pramaan_System SHALL enable AWS CloudTrail for all control plane actions
2. THE Pramaan_System SHALL centralize logs in CloudWatch with retention policies
3. THE Pramaan_System SHALL alert on: policy violations, repeated failed logins, IAM policy changes, unexpected S3 access patterns, enclave integrity failures
4. THE Pramaan_System SHALL keep all logs redacted (no PHI in logs)
5. THE Pramaan_System SHALL implement automated incident response for critical alerts
6. THE Pramaan_System SHALL maintain security incident audit trail