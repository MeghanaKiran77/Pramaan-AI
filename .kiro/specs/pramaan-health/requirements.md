# Requirements Document

## Introduction

Pramaan-Health is a pilot implementation of the Pramaan AI protocol that enables confidential AI training on healthcare data without exposing raw sensitive information. The system provides a trust layer that allows AI models to learn from hospital data while maintaining data sovereignty, privacy compliance, and verifiable enforcement of data usage policies.

## Glossary

- **Pramaan_System**: The complete confidential AI training protocol implementation
- **Hospital**: Healthcare institution that owns and controls sensitive patient data (Data Asset Owner)
- **AI_Developer**: Entity seeking to train AI models on healthcare data (Data Consumer)
- **Protocol_Manifest**: Machine-readable legal-technical contract defining data usage terms
- **Secure_Enclave**: Hardware-isolated execution environment (AWS Nitro Enclave)
- **Attestation_Receipt**: Cryptographic proof of policy compliance and execution
- **Social_Credits**: Non-monetary incentive tokens awarded for verified performance improvements
- **Differential_Privacy**: Mathematical privacy guarantee limiting information leakage
- **Settlement_Ledger**: Permissioned blockchain recording credit transactions
- **DPDP_Act**: Digital Personal Data Protection Act (India's privacy regulation)

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

**User Story:** As a Hospital, I want AI training to occur in a hardware-isolated environment, so that my raw data never leaves the secure boundary and cannot be extracted.

#### Acceptance Criteria

1. THE Secure_Enclave SHALL decrypt healthcare data only within enclave memory
2. THE Secure_Enclave SHALL deny all network egress by default during training execution
3. WHEN training completes, THE Secure_Enclave SHALL destroy all decrypted data from memory
4. THE Secure_Enclave SHALL prevent any persistence of raw healthcare data to storage
5. WHEN enclave integrity is compromised, THE Pramaan_System SHALL halt execution and generate security alerts

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

**User Story:** As an AI_Developer, I want my model performance to be evaluated fairly within the trusted system, so that I can receive Social_Credits for verified improvements to healthcare outcomes.

#### Acceptance Criteria

1. WHEN model training completes, THE Pramaan_System SHALL evaluate performance against Protocol_Manifest success metrics
2. THE Pramaan_System SHALL compute Social_Credits based on verified performance improvements
3. WHEN performance meets threshold criteria, THE Pramaan_System SHALL record credit allocation on the Settlement_Ledger
4. THE Pramaan_System SHALL route earned credits to designated public health funds
5. THE Pramaan_System SHALL generate performance attestations with cryptographic signatures

### Requirement 6: Compliance and Audit Trail

**User Story:** As a Hospital compliance officer, I want comprehensive audit trails and attestations, so that I can demonstrate DPDP Act compliance to regulators.

#### Acceptance Criteria

1. THE Pramaan_System SHALL generate time-stamped Attestation_Receipts for all policy enforcement actions
2. THE Attestation_Receipt SHALL contain cryptographic proof of differential privacy compliance
3. THE Attestation_Receipt SHALL include proof of zero-persistence data destruction
4. WHEN audit requests are made, THE Pramaan_System SHALL provide verifiable compliance artifacts
5. THE Pramaan_System SHALL maintain immutable logs of all system interactions for regulatory review

### Requirement 7: Protocol Manifest Management

**User Story:** As a Hospital, I want to create and manage Protocol Manifests programmatically, so that I can efficiently establish data sharing agreements with multiple AI developers.

#### Acceptance Criteria

1. THE Pramaan_System SHALL provide APIs for Protocol_Manifest creation and validation
2. WHEN a Protocol_Manifest is created, THE Pramaan_System SHALL validate all required fields and constraints
3. THE Pramaan_System SHALL support versioning of Protocol_Manifests for iterative policy refinement
4. THE Pramaan_System SHALL enable Protocol_Manifest templates for common healthcare use cases
5. WHEN Protocol_Manifests are updated, THE Pramaan_System SHALL notify affected AI_Developers

### Requirement 8: Cryptographic Attestation System

**User Story:** As a regulator, I want cryptographically verifiable proofs of system behavior, so that I can audit compliance without accessing sensitive healthcare data.

#### Acceptance Criteria

1. THE Pramaan_System SHALL generate cryptographic signatures for all attestations using hardware-backed keys
2. THE Pramaan_System SHALL provide public key infrastructure for attestation verification
3. WHEN attestations are generated, THE Pramaan_System SHALL include merkle proofs of execution integrity
4. THE Pramaan_System SHALL enable third-party verification of attestations without system access
5. THE Pramaan_System SHALL maintain cryptographic chain of custody for all compliance artifacts

### Requirement 9: Healthcare Data Integration

**User Story:** As a Hospital, I want to securely integrate my existing healthcare data systems, so that I can participate in confidential AI training without disrupting clinical operations.

#### Acceptance Criteria

1. THE Pramaan_System SHALL support standard healthcare data formats (HL7 FHIR, DICOM)
2. WHEN healthcare data is ingested, THE Pramaan_System SHALL validate data integrity and completeness
3. THE Pramaan_System SHALL encrypt healthcare data using hospital-controlled keys
4. THE Pramaan_System SHALL provide secure APIs for healthcare system integration
5. WHEN data integration fails, THE Pramaan_System SHALL provide detailed error diagnostics

### Requirement 10: System Monitoring and Observability

**User Story:** As a system administrator, I want comprehensive monitoring of the Pramaan system, so that I can ensure reliable operation and detect security incidents.

#### Acceptance Criteria

1. THE Pramaan_System SHALL provide real-time monitoring of enclave health and performance
2. THE Pramaan_System SHALL generate alerts for policy violations, security incidents, and system failures
3. WHEN system anomalies are detected, THE Pramaan_System SHALL automatically trigger incident response procedures
4. THE Pramaan_System SHALL provide dashboards for tracking training jobs, credit allocations, and compliance status
5. THE Pramaan_System SHALL maintain audit logs with tamper-evident properties for forensic analysis