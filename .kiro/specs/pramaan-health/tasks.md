# Implementation Plan: Pramaan-Health

## Overview

This implementation plan breaks down the Pramaan-Health confidential AI training protocol into three phases aligned with hackathon demonstration priorities. The plan focuses on building core protocol infrastructure first, then adding security layers, and finally implementing minimal web interfaces for demonstration.

## Phased Implementation Strategy

### Phase 1: Core Protocol Demo (Priority 1 - Must Have)
Build the essential protocol engine to demonstrate deterministic governance and credit minting with mock security.

### Phase 2: Security Demo (Priority 2 - Should Have)
Integrate real AWS Nitro Enclaves and cryptographic attestation to prove confidential computing capabilities.

### Phase 3: UI Layer (Priority 3 - Nice to Have)
Add minimal web interfaces for hospital and developer portals to demonstrate user flows.

## Tasks

### PHASE 1: Core Protocol Demo (Must Have)

- [ ] 1. Set up project structure and core data models
  - Create Python project structure with FastAPI, Pydantic, Docker
  - Define Pydantic models for Protocol Manifest, Training Request, and Attestation Receipt schemas (including new fields: dataset version hash, model hash, baseline hash, cost model, redemption details)
  - Set up configuration management for system parameters
  - Initialize logging and error handling infrastructure
  - Create API gateway structure with REST endpoints
  - Set up AWS Cognito for authentication (OAuth 2.0/JWT)
  - Implement RBAC with roles: hospital_admin, hospital_compliance, ai_developer, regulator_readonly, system_operator
  - _Requirements: 7.1, 9.1, 11.1, 11.2_
  - _Phase: 1 - Core Protocol Demo_

- [ ] 1.1 Write property test for authentication and authorization
  - **Property 28: Authentication and Authorization**
  - **Validates: Requirements 11.1, 11.2, 11.3, 11.4, 11.5, 11.6**
  - _Phase: 1 - Core Protocol Demo_

- [ ] 2. Implement Protocol Manifest management with templates
  - [ ] 2.1 Create Protocol Manifest validator with template system
    - Implement schema validation using Pydantic
    - Add field-level validation for purpose limitation, dataset scope, privacy guardrails
    - Create manifest signing and verification functions
    - Implement healthcare-specific templates: diagnostic classification (public health), diagnostic classification (research), segmentation, risk scoring
    - Add template selection and instantiation logic
    - Implement manifest hash generation for immutability
    - _Requirements: 1.1, 1.2, 7.1, 7.2, 7.3_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 2.2 Write property test for Protocol Manifest validation
    - **Property 1: Protocol Manifest Validation**
    - **Property 20: Template Functionality**
    - **Validates: Requirements 1.1, 1.2, 7.1, 7.2, 7.3**
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 2.3 Implement manifest versioning system
    - Create version management for Protocol Manifests (semver)
    - Add backward compatibility checking
    - Prevent retroactive edits after job start
    - Reference manifest hash in attestation receipts
    - _Requirements: 7.4, 7.5, 7.6_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 2.4 Write property test for manifest immutability
    - **Property 2: Manifest Immutability After Job Start**
    - **Property 19: Manifest Versioning and Notifications**
    - **Validates: Requirements 7.4, 7.5, 7.6, 7.7**
    - _Phase: 1 - Core Protocol Demo_

- [ ] 3. Build request processing and validation system
  - [ ] 3.1 Create training request processor with binary acceptance model
    - Implement request validation against Protocol Manifest constraints
    - Add request lifecycle management (pending, approved, rejected, executing, completed)
    - Create request-manifest compliance checker
    - Implement binary developer acceptance (accept or decline, no negotiation)
    - Add .eif hash validation (without inspecting internals)
    - _Requirements: 1.3, 1.4, 7.9, 7.10_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 3.2 Write property tests for request validation
    - **Property 3: Request-Manifest Compliance**
    - **Property 4: Binary Developer Acceptance**
    - **Validates: Requirements 1.3, 1.4, 7.9, 7.10**
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 3.3 Implement policy violation detection and response
    - Create violation detection mechanisms
    - Add execution termination on policy violations
    - Implement violation attestation generation
    - _Requirements: 1.5_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 3.4 Write property test for policy violation response
    - **Property 5: Policy Violation Response**
    - **Validates: Requirements 1.5**
    - _Phase: 1 - Core Protocol Demo_

- [ ] 4. Implement mock secure execution environment
  - [ ] 4.1 Create mock enclave simulation framework
    - Build mock AWS Nitro Enclave environment for Phase 1 development
    - Implement network isolation simulation
    - Add memory management for secure data handling
    - Create .eif execution simulation
    - Simulate symmetric confidentiality (hospital data + developer IP protection)
    - _Requirements: 2.2, 2.3, 2.4, 2.6, 2.7, 2.8, 2.9_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 4.2 Write property tests for secure execution
    - **Property 6: Enclave Image File Protection**
    - **Property 7: Network Isolation During Training**
    - **Property 8: Data Destruction After Training**
    - **Property 25: Symmetric Confidentiality Protection**
    - **Validates: Requirements 2.2, 2.3, 2.4, 2.6, 2.7, 2.8, 2.9**
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 4.3 Implement integrity monitoring and response
    - Create enclave integrity checking mechanisms
    - Add automatic halt on integrity compromise
    - Implement security alert generation
    - _Requirements: 2.5_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 4.4 Write property test for integrity compromise response
    - **Property 9: Integrity Compromise Response**
    - **Validates: Requirements 2.5**
    - _Phase: 1 - Core Protocol Demo_

- [ ] 5. Build mock differential privacy training engine
  - [ ] 5.1 Implement mock DP-SGD training framework
    - Create mock differentially private gradient computation
    - Implement noise addition simulation
    - Add support for simple model architectures (logistic regression, small neural nets)
    - _Requirements: 3.1_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 5.2 Create privacy budget management system
    - Implement privacy budget initialization from Protocol Manifest
    - Add real-time budget tracking during training
    - Create automatic training termination on budget exhaustion
    - Generate cryptographic proofs of privacy budget usage
    - _Requirements: 3.2, 3.3, 3.4, 3.5_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 5.3 Write property tests for privacy budget management
    - **Property 10: Differential Privacy Application**
    - **Property 11: Privacy Budget Management**
    - **Validates: Requirements 3.1, 3.2, 3.3, 3.4, 3.5**
    - _Phase: 1 - Core Protocol Demo_

- [ ] 6. Build performance evaluation and deterministic credit minting engine
  - [ ] 6.1 Create model performance evaluator with NIS calculation
    - Implement performance evaluation against Protocol Manifest success metrics
    - Add baseline model comparison functionality (baseline provided in .eif, evaluated inside enclave)
    - Implement Normalized Improvement Score (NIS) calculation: NIS = (NewMetric - BaselineMetric) / (1 - BaselineMetric)
    - Create performance improvement calculation accounting for diminishing returns
    - Generate model hash, baseline hash, dataset version hash
    - _Requirements: 5.1, 5.2, 13.2, 13.3, 13.4, 17.1, 17.2, 17.3_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 6.2 Implement deterministic credit minting with protocol-defined caps and duplicate prevention
    - Create protocol-defined credit bands mapping NIS ranges to SC amounts
    - Enforce hard caps: max credits per run (500 SC), max per hospital per model per year (5,000 SC)
    - Implement institutional-only credit allocation (Hospital/Data Fiduciary)
    - Prevent individual patient-level tracking
    - Prevent hospital discretion in credit assignment (anti-inflation)
    - Implement duplicate minting prevention: track (AI_Developer + Model Hash + Dataset Version Hash)
    - Reject or apply diminishing returns for duplicate submissions
    - _Requirements: 5.3, 5.4, 5.7, 5.8, 5.9, 17.4, 17.5, 17.7_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 6.3 Write property tests for credit minting
    - **Property 12: Normalized Improvement Score Calculation**
    - **Property 13: Deterministic Credit Minting with Caps**
    - **Property 14: Credit Settlement to Institutional Recipients**
    - **Property 30: Baseline Model Validation**
    - **Property 34: Duplicate Minting Prevention**
    - **Validates: Requirements 5.2, 5.3, 5.4, 5.5, 5.7, 5.8, 5.9, 13.2, 13.3, 13.4, 13.5, 17.4, 17.5, 17.7**
    - _Phase: 1 - Core Protocol Demo_

- [ ] 7. Implement mock settlement ledger
  - [ ] 7.1 Create mock blockchain settlement service
    - Implement in-memory settlement ledger for Phase 1
    - Record credit transactions with institutional recipients
    - Maintain transaction history
    - Add credit balance queries
    - Implement visibility controls (Hospital sees credits, Developer sees own job impact)
    - _Requirements: 5.5, 6.6, 6.7_
    - _Phase: 1 - Core Protocol Demo_

- [ ] 8. Implement attestation generation engine
  - [ ] 8.1 Create comprehensive attestation receipt generator
    - Implement time-stamped attestation receipt generation
    - Add cryptographic signature generation using mock hardware keys (Phase 1)
    - Include proofs: DP compliance, data destruction, execution integrity
    - Generate merkle proofs for execution integrity
    - Embed manifest hash references (prevent retroactive edits)
    - Include judge-proof compliance statements:
      - "Credits are not money and cannot be cashed out"
      - "No individual patient tracking for credit allocation"
      - "Model access is standardized as capped API/inference credits"
      - "Developers accept manifest or decline; no post-hoc negotiation"
      - "Institutional verification is document-based via DigiLocker; Pramaan does not access Aadhaar data"
    - _Requirements: 5.6, 6.1, 6.2, 6.3, 6A.8, 8.1, 8.3_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 8.2 Write property test for comprehensive attestation
    - **Property 16: Comprehensive Attestation Generation**
    - **Validates: Requirements 5.6, 6.1, 6.2, 6.3, 6A.8, 8.1, 8.3**
    - _Phase: 1 - Core Protocol Demo_

- [ ] 9. Build API endpoints for core protocol
  - [ ] 9.1 Implement REST API endpoints with FastAPI and rate limiting
    - POST /manifest - Create and validate manifests
    - GET /manifests - List available manifests
    - POST /training-request - Submit training jobs
    - GET /training-request/{id} - Check job status
    - GET /receipt/{id} - Retrieve attestation receipts
    - GET /credits/{hospital-id} - View institutional credits
    - POST /verify-receipt - Third-party verification
    - GET /audit/export/{job-id} - Export compliance reports
    - GET /cost-estimate - Estimate training costs
    - POST /dispute/trigger - Trigger manual audit
    - Add comprehensive error handling and diagnostics
    - Implement API rate limiting via API Gateway
    - Enforce request quotas per AI_Developer organization
    - Limit maximum concurrent jobs per organization
    - Deploy WAF for DDoS protection
    - _Requirements: 7.1, 7.8, 9.4, 9.5, 20.1, 20.2, 20.3, 20.4, 20.5, 21.3, 22.1_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 9.2 Add API documentation
    - Generate OpenAPI/Swagger documentation
    - Add example requests and responses
    - Document authentication and authorization requirements
    - Document rate limits and quotas
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 9.3 Write property tests for API functionality
    - **Property 24: Secure API Functionality**
    - **Property 36: Rate Limiting and Abuse Prevention**
    - **Property 38: Compliance Report Export**
    - **Validates: Requirements 7.8, 9.4, 9.5, 20.1, 20.2, 20.3, 20.4, 20.5, 22.1, 22.2, 22.3, 22.4, 22.5, 22.6**
    - _Phase: 1 - Core Protocol Demo_

- [ ] 10. Implement audit, compliance, and cost tracking systems
  - [ ] 10.1 Create immutable audit repository with compliance export
    - Implement tamper-evident audit logging
    - Add cryptographic integrity checks for audit logs
    - Create verifiable compliance artifact generation
    - Implement compliance report export in structured JSON format
    - Add batch export capability for multiple jobs
    - Include cryptographic signatures on exported reports
    - Implement regulatory reporting API endpoints
    - _Requirements: 6.4, 6.5, 22.1, 22.2, 22.3, 22.4, 22.5, 22.6_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 10.2 Implement cost estimation and tracking
    - Create cost estimation API based on instance type and training time
    - Track actual compute costs per training job
    - Include compute costs in attestation receipts
    - Enforce developer payment responsibility
    - Document cost model in Protocol Manifest
    - _Requirements: 21.1, 21.2, 21.3, 21.4, 21.5, 21.7_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 10.3 Implement dispute resolution system
    - Create manual audit trigger API endpoint
    - Add job state freeze capability
    - Enable re-verification from logs and receipts
    - Implement governance escalation workflow
    - Maintain dispute resolution audit trail
    - _Requirements: 19.1, 19.2, 19.3, 19.4, 19.5_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 10.4 Write property tests for audit and cost systems
    - **Property 18: Audit Compliance**
    - **Property 37: Cost Transparency**
    - **Validates: Requirements 6.4, 6.5, 21.1, 21.2, 21.3, 21.4, 21.5, 21.7, 22.1, 22.2, 22.3, 22.4, 22.5, 22.6**
    - _Phase: 1 - Core Protocol Demo_

- [ ] 11. Implement hospital data management and dataset versioning
  - [ ] 11.1 Create hospital data reference system
    - Implement S3 bucket reference in Protocol Manifest (hospital-controlled)
    - Add dataset version hash generation and validation
    - Ensure Pramaan never accepts raw data uploads
    - Validate data availability and SSE-KMS encryption before training
    - Support optional pre-signed URLs for facilitated upload
    - Enforce dataset immutability per manifest version
    - Require new manifest version for dataset updates
    - _Requirements: 12.1, 12.2, 12.3, 12.4, 12.7, 18.1, 18.2, 18.3, 18.4, 18.5_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 11.2 Write property tests for data upload security and dataset versioning
    - **Property 29: Hospital Data Upload Security**
    - **Property 35: Dataset Immutability**
    - **Validates: Requirements 12.1, 12.2, 12.3, 12.4, 12.7, 18.1, 18.2, 18.3, 18.4, 18.5**
    - _Phase: 1 - Core Protocol Demo_

- [ ] 12. Implement failure handling and single-hospital scope enforcement
  - [ ] 12.1 Create atomic training execution logic
    - Implement atomic training job execution (no checkpoints)
    - Add failure detection and job status update to "failed"
    - Prevent credit minting for failed jobs
    - Implement stakeholder notification on failure
    - Allow re-submission after failure
    - Explicitly prevent partial credit allocation
    - _Requirements: 15.1, 15.2, 15.3, 15.4, 15.5, 15.6, 15.7_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 12.2 Enforce single hospital training scope
    - Validate Protocol Manifest references exactly one Hospital/Data Fiduciary
    - Reject multi-hospital collaboration requests
    - Document federated learning as future roadmap (Pramaan-Federated)
    - _Requirements: 16.1, 16.2, 16.3, 16.4_
    - _Phase: 1 - Core Protocol Demo_
  
  - [ ] 12.3 Write property tests for failure handling and scope enforcement
    - **Property 32: Atomic Training Execution**
    - **Property 33: Single Hospital Training Scope**
    - **Validates: Requirements 15.1, 15.2, 15.3, 15.4, 15.5, 15.6, 16.1, 16.2, 16.3**
    - _Phase: 1 - Core Protocol Demo_

- [ ] 13. Checkpoint - Phase 1 Complete
  - Ensure all Phase 1 tests pass
  - Verify API endpoints functional with authentication and rate limiting
  - Test end-to-end flow with mock enclave
  - Verify duplicate minting prevention works
  - Verify atomic failure handling works
  - Verify cost estimation and tracking works
  - Demo capability: Manifest creation → Request submission → Mock training → Credit minting → Attestation generation → Compliance export
  - _Phase: 1 - Core Protocol Demo_

### PHASE 2: Security Demo (Should Have)

- [ ] 14. Integrate real AWS Nitro Enclaves
  - [ ] 14.1 Set up AWS EC2 with Nitro Enclave support
    - Provision EC2 parent instance with enclave capabilities
    - Configure enclave resource allocation (CPU, memory)
    - Set up enclave networking and isolation policies
    - _Requirements: 2.1, 2.2_
    - _Phase: 2 - Security Demo_
  
  - [ ] 12.2 Implement real enclave orchestration service
    - Replace mock enclave with real Nitro integration
    - Implement enclave provisioning and lifecycle management
    - Add PCR-based attestation verification
    - Implement real network isolation enforcement (no egress)
    - _Requirements: 2.2, 2.5_
    - _Phase: 2 - Security Demo_
  
  - [ ] 12.3 Create .eif build and deployment pipeline
    - Implement Enclave Image File (.eif) generation from developer code
    - Add .eif signing and hash verification
    - Create .eif deployment to enclave
    - Ensure developer IP protection (no code inspection by controller)
    - _Requirements: 2.6, 2.7, 2.8, 2.9_
    - _Phase: 2 - Security Demo_
  
  - [ ] 12.4 Write property tests for real enclave execution
    - **Property 6: Enclave Image File Protection**
    - **Property 7: Network Isolation During Training**
    - **Property 9: Integrity Compromise Response**
    - **Property 25: Symmetric Confidentiality Protection**
    - **Validates: Requirements 2.2, 2.5, 2.6, 2.7, 2.8, 2.9**
    - _Phase: 2 - Security Demo_

- [ ] 13. Integrate AWS KMS for key management
  - [ ] 13.1 Implement KMS-based key release
    - Set up AWS KMS for encryption key management
    - Implement attestation-gated key release (PCR-based)
    - Add in-memory decryption within enclave
    - Ensure keys never persist outside enclave
    - _Requirements: 2.1_
    - _Phase: 2 - Security Demo_

- [ ] 14. Implement real DP-SGD training with Opacus
  - [ ] 14.1 Replace mock DP-SGD with Opacus library
    - Integrate Opacus for real differentially private gradient computation
    - Implement proper noise addition mechanisms for gradient perturbation
    - Add support for PyTorch model architectures
    - Create real privacy accounting with RDP composition
    - Integrate with privacy budget management system
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5_
    - _Phase: 2 - Security Demo_
  
  - [ ] 14.2 Write property test for real differential privacy
    - **Property 10: Differential Privacy Application**
    - **Property 11: Privacy Budget Management**
    - **Validates: Requirements 3.1, 3.2, 3.3, 3.4, 3.5**
    - _Phase: 2 - Security Demo_

- [ ] 15. Implement hardware-backed attestation signing
  - [ ] 15.1 Integrate AWS KMS for attestation signing
    - Replace mock signing with real hardware-backed keys
    - Implement KMS-based signature generation for attestation receipts
    - Add attestation verification with public keys
    - Create PKI infrastructure for third-party verification
    - Implement cryptographic chain of custody
    - _Requirements: 8.1, 8.2, 8.4, 8.5_
    - _Phase: 2 - Security Demo_
  
  - [ ] 15.2 Write property test for PKI functionality
    - **Property 22: Public Key Infrastructure**
    - **Validates: Requirements 8.2, 8.4, 8.5**
    - _Phase: 2 - Security Demo_

- [ ] 16. Integrate real settlement ledger (optional)
  - [ ] 16.1 Set up Amazon Managed Blockchain (Hyperledger Fabric)
    - Provision permissioned blockchain network
    - Deploy smart contracts for credit settlement
    - Integrate settlement service with blockchain RPC
    - Add transaction verification and immutability guarantees
    - _Requirements: 5.5_
    - _Phase: 2 - Security Demo_
    - _Note: Optional - can use enhanced mock ledger with persistence if blockchain setup is time-constrained_

- [ ] 17. Checkpoint - Phase 2 Complete
  - Ensure all Phase 2 tests pass
  - Verify real Nitro Enclave integration functional
  - Test real DP-SGD with Opacus
  - Verify hardware-backed attestation signing
  - Demo capability: Real enclave execution → Real DP training → Cryptographic attestation
  - _Phase: 2 - Security Demo_

### PHASE 3: UI Layer (Nice to Have)

- [ ] 18. Build Hospital Portal (Minimal Web Interface)
  - [ ] 18.1 Create Hospital Portal frontend with Next.js/React
    - Set up Next.js project structure
    - Implement manifest creation form with template dropdown
    - Create job listing view with status tracking
    - Add attestation receipt viewer
    - Implement Social Credits dashboard (institutional view)
    - Add simple authentication
    - _Requirements: 7.1, 10.4_
    - _Phase: 3 - UI Layer_
  
  - [ ] 18.2 Integrate Hospital Portal with API Gateway
    - Connect frontend to backend REST APIs
    - Implement API client with error handling
    - Add loading states and user feedback
    - _Phase: 3 - UI Layer_

- [ ] 19. Build Developer Portal (Minimal Web Interface)
  - [ ] 19.1 Create Developer Portal frontend with Next.js/React
    - Set up Next.js project structure
    - Add manifest browsing interface
    - Create manifest acceptance flow (binary: accept or decline)
    - Implement .eif file upload component
    - Add job status tracking view
    - Create credits owed display
    - Add attestation receipt download
    - Add simple authentication
    - _Requirements: 7.9, 7.10_
    - _Phase: 3 - UI Layer_
  
  - [ ] 19.2 Integrate Developer Portal with API Gateway
    - Connect frontend to backend REST APIs
    - Implement API client with error handling
    - Add loading states and user feedback
    - _Phase: 3 - UI Layer_

- [ ] 20. Checkpoint - Phase 3 Complete
  - Verify Hospital Portal functional
  - Verify Developer Portal functional
  - Test complete user flows end-to-end via UI
  - Demo capability: Hospital creates manifest via UI → Developer accepts and uploads .eif via UI → View results in both portals
  - _Phase: 3 - UI Layer_

### ADDITIONAL TASKS (If Time Permits)

- [ ] 21. Implement model output protection and constraints
  - [ ] 21.1 Create model output perturbation system
    - Implement parameter perturbation for trained models
    - Add differential privacy validation for model outputs
    - Create output constraint verification mechanisms
    - Implement model internal access prevention
    - _Requirements: 4.1, 4.2, 4.4, 4.5_
  
  - [ ] 21.2 Write property tests for model output protection
    - **Property 12: Model Output Privacy Protection**
    - **Property 13: Model Internal Protection**
    - **Property 14: Output Constraint Attestation**
    - **Validates: Requirements 4.1, 4.2, 4.4, 4.5**

- [ ] 22. Implement standardized redemption system
  - [ ] 22.1 Create Model Access redemption manager
    - Implement capped, time-bound API/inference credit allocation
    - Enforce public-sector usage restrictions
    - Prevent lifetime free access or unlimited licensing
    - Add redemption tracking and usage monitoring
    - _Requirements: 6A.2, 6A.3, 6A.4_
  
  - [ ] 22.2 Write property test for standardized redemption
    - **Property 15: Standardized Model Access Redemption**
    - **Validates: Requirements 6A.2, 6A.3, 6A.4, 6A.8**

- [ ] 23. Implement visibility control system
  - [ ] 23.1 Create role-based visibility manager
    - Implement visibility controls: Hospital sees credits+receipts, Developer sees own jobs, Regulator sees audit trails (if granted), Public sees aggregate stats only
    - Prevent exposure of credits as public social media-style metrics
    - Ensure no dataset or patient identifiers in public data
    - _Requirements: 6.6, 6.7, 6.8, 6.9, 6.10_
  
  - [ ] 23.2 Write property test for visibility control
    - **Property 17: Visibility Control Enforcement**
    - **Validates: Requirements 6.6, 6.7, 6.8, 6.9, 6.10**

- [ ] 24. Implement DigiLocker institutional verification
  - [ ] 24.1 Create DigiLocker integration service
    - Implement document-based institutional verification
    - Validate registration certificates, government affiliation documents, authorization letters
    - Check issuer authenticity and document validity
    - Ensure no Aadhaar storage, no biometric processing, no patient identity processing
    - _Requirements: 9.6, 9.7, 9.8_
  
  - [ ] 24.2 Write property test for DigiLocker verification
    - **Property 21: DigiLocker Institutional Verification**
    - **Validates: Requirements 9.6, 9.7, 9.8**

- [ ] 25. Implement healthcare data format support
  - [ ] 25.1 Create healthcare data parsers
    - Implement HL7 FHIR format parser
    - Implement DICOM format parser
    - Add data integrity and completeness validation
    - Create hospital-controlled encryption key management
    - _Requirements: 9.1, 9.2, 9.3_
  
  - [ ] 25.2 Write property test for healthcare data support
    - **Property 23: Healthcare Data Format Support**
    - **Validates: Requirements 9.1, 9.2, 9.3**

- [ ] 26. Implement system monitoring and alerting
  - [ ] 26.1 Build monitoring and alerting system
    - Implement real-time system monitoring
    - Create alert generation for violations, incidents, and failures
    - Add automated incident response procedures
    - Create basic metrics dashboard
    - _Requirements: 10.1, 10.2, 10.3, 10.4, 10.5_
  
  - [ ] 26.2 Write property tests for monitoring
    - **Property 26: System Monitoring and Alerting**
    - **Property 27: Dashboard and Audit Logging**
    - **Validates: Requirements 10.1, 10.2, 10.3, 10.4, 10.5**

- [ ] 27. Create developer notification system
  - [ ] 27.1 Implement notification service
    - Create notification system for Protocol Manifest updates
    - Add email/webhook notification delivery
    - Create notification preference management
    - _Requirements: 7.7_

- [ ] 28. Final checkpoint - Complete system validation
  - Run all property-based tests with 100+ iterations each
  - Verify all unit tests pass
  - Validate complete workflow functionality
  - Ensure all Phase 1 and Phase 2 requirements are implemented and tested
  - Generate final system documentation

## Hackathon Execution Priority

### Must Have (Phase 1) - Tasks 1-11
Core protocol engine with mock enclave, deterministic credit minting, API endpoints, attestation generation

**Demo Capability:** Show complete protocol flow with mock security

**Time Estimate:** 60-70% of development time

### Should Have (Phase 2) - Tasks 12-17
Real Nitro Enclaves, real DP-SGD with Opacus, hardware-backed attestation, audit system

**Demo Capability:** Prove real confidential computing and cryptographic guarantees

**Time Estimate:** 20-30% of development time

### Nice to Have (Phase 3) - Tasks 18-20
Minimal web interfaces for hospital and developer portals

**Demo Capability:** Show user-friendly interaction model

**Time Estimate:** 10% of development time

### If Time Permits - Tasks 21-28
Model output protection, redemption system, visibility controls, DigiLocker, healthcare data parsers, monitoring

**Demo Capability:** Show additional governance and compliance features

## Notes

- **Phase 1 is critical** - demonstrates protocol correctness and deterministic governance
- **Phase 2 proves security** - shows real confidential computing, not just theory
- **Phase 3 is polish** - makes demo more accessible but not core value
- **Regulator dashboard skipped** - API-only access is sufficient for MVP
- Each task references specific requirements for traceability
- Property tests validate universal correctness properties using Hypothesis library
- Unit tests validate specific examples and edge cases
- Checkpoints ensure incremental validation throughout development
- **Primary implementation language: Python** with focus on rapid prototyping and research-grade systems
- **Key Python libraries**: Pydantic (data models), Opacus (differential privacy), FastAPI (APIs), Cryptography (attestation), Hypothesis (property-based testing), boto3 (AWS SDK)
- **AWS services integration**: Nitro Enclaves (secure execution), KMS (key management), S3 (encrypted storage), Managed Blockchain (settlement ledger - optional in Phase 1)
- **Frontend stack** (Phase 3): Next.js/React with minimal styling
- **Architecture focus**: This is a pilot implementation demonstrating correctness, enforcement logic, and trust guarantees rather than full production deployment
