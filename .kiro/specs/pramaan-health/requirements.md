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