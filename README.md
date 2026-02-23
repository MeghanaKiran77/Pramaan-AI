# Pramaan AI

> **UPI for Data & Consent** - Building the Consent Layer of India Stack

Pramaan AI (derived from the Sanskrit word for *proof, evidence, or validation*) is an infrastructure-level protocol designed to solve the "Data Sovereignty Crisis" of the AI era. The problem isn't that we don't have enough data for AI; it's that we don't have enough **trusted data**.

Pramaan AI enables AI models to learn from the 'Dark Data' of Bharat (private health records, local dialects, agricultural logs) without the model ever 'seeing' the sensitive raw data.

## 🎯 Vision

Create a protocol that allows confidential AI training on sensitive data while maintaining:
- **Data Sovereignty** - Data owners retain full control
- **Privacy Guarantees** - Differential privacy and hardware isolation
- **Verifiable Trust** - Cryptographic attestation of compliance
- **Fair Incentives** - Non-monetary Social Credits for institutional contribution

## 🏥 Pramaan-Health: Healthcare Pilot

This repository contains the specification for **Pramaan-Health**, a pilot implementation demonstrating confidential AI training on sensitive healthcare data.

### What Pramaan-Health IS

✅ A protocol and enforcement layer  
✅ A policy-as-code trust system  
✅ A confidential AI training primitive  
✅ A compliance-first infrastructure concept

### What Pramaan-Health is NOT

❌ An end-user application  
❌ A healthcare analytics app  
❌ A chatbot or data marketplace UI  
❌ A full India Stack deployment

## 🏗️ Four-Layer Architecture

Pramaan-Health is built as protocol-first infrastructure with minimal interfaces:

### 🟦 Layer 1: Core Protocol Engine (Backend Services)
**The main product** - Backend microservices implementing protocol logic:
- Manifest validation service
- Request validation service  
- Enclave orchestration service
- DP-SGD enforcement logic
- Evaluation engine
- Credit computation engine (NIS calculation, deterministic minting)
- Attestation generator
- Settlement service
- Audit logger

**Tech:** Python microservices, FastAPI, Pydantic, containerized (Docker), deployed on AWS

### 🟩 Layer 2: Secure Execution Layer
**The trust boundary** where confidential training occurs:
- AWS EC2 with Nitro Enclaves
- .eif image submission and validation
- KMS-based key release (attestation-gated)
- In-memory decryption only
- Network egress denied by default
- Symmetric confidentiality (hospital data + developer IP)

**Tech:** AWS Nitro Enclaves, AWS KMS, hardware attestation

### 🟨 Layer 3: API Gateway
**Public-facing endpoints** enabling integration:
- `POST /manifest` - Create Protocol Manifest
- `POST /training-request` - Submit training job
- `GET /receipt/{id}` - Retrieve attestation receipt
- `GET /credits/{hospital-id}` - View institutional credits
- `POST /verify-receipt` - Third-party verification

**Consumers:** Web UI, CLI tools, third-party systems, future mobile apps

### 🟧 Layer 4: Minimal Web Interfaces (Pilot Only)
**Thin demonstration UIs** (React/Next.js):

**Hospital Portal:** Create manifests (template dropdown), view jobs, view receipts, view credits

**Developer Portal:** Browse manifests, accept terms, upload .eif, view status, download receipts

**Note:** These are minimal functional interfaces for demonstration, not production-grade applications. The core value is in Layers 1-3.

## 🎬 Demo Flow for Judges (3 Minutes)

**Scenario:** District hospital contributes diabetic retinopathy screening data for AI training

### Step 1: Hospital Creates Manifest (30s)
- Admin logs into Hospital Portal
- Selects template: "Diagnostic Classification - Public Health"
- Configures: Purpose, dataset scope (10K images), privacy budget (ε=3.0), success metric (AUROC > 0.85), Model Access (500 API credits, 6 months)
- Clicks "Publish" → System validates and signs manifest

### Step 2: Developer Submits Request (30s)
- Developer logs into Developer Portal
- Browses manifests, finds diabetic retinopathy
- Reviews terms, clicks "Accept" (binary, no negotiation)
- Uploads .eif file (proprietary training code)
- Submits request → System validates .eif hash and queues job

### Step 3: Backend Execution (1min - show logs)
- Enclave provisions, KMS releases keys after attestation
- Data decrypted in enclave memory only
- DP-SGD training executes with privacy tracking
- Results: Baseline AUROC 0.78 → Trained 0.88
- NIS = (0.88 - 0.78) / (1 - 0.78) = 0.45
- Credits minted: 350 SC (protocol-defined band)
- Data destruction proof generated

### Step 4: Attestation Receipt (30s)
- Comprehensive receipt generated with:
  - Hardware-backed signature
  - Privacy proof: ε=2.8 used
  - Data destruction timestamp
  - Performance: AUROC 0.88, NIS 0.45, 350 SC
  - Merkle proof, manifest hash
  - Judge-proof compliance statements
- Credits recorded on Settlement Ledger
- Hospital receives 350 SC (institutional, no patient tracking)

### Step 5: API Integration (30s)
- Show OpenAPI docs
- Demonstrate: `GET /receipt/{id}`, `GET /credits/{hospital-id}`, `POST /verify-receipt`
- Highlight: No UI required, pure protocol access

**What Judges See:** Real Nitro integration, real DP-SGD, deterministic credit minting, hardware attestation, clean API design, minimal but functional interface

## 🚀 Implementation Phases

### Phase 1: Core Protocol Demo (Must Have)
Core protocol engine with mock enclave, deterministic credit minting, API endpoints, attestation generation

**Demo Capability:** Complete protocol flow with mock security

### Phase 2: Security Demo (Should Have)
Real AWS Nitro Enclaves, real DP-SGD with Opacus, hardware-backed attestation, audit system

**Demo Capability:** Prove real confidential computing and cryptographic guarantees

### Phase 3: UI Layer (Nice to Have)
Minimal Hospital Portal and Developer Portal

**Demo Capability:** User-friendly interaction model

## 🏗️ Protocol Architecture Details

### 1. The Handshake - Policy Layer
Machine-readable Protocol Manifests define legal-technical contracts between hospitals (Data Fiduciaries) and AI developers (Data Consumers). These manifests encode:
- Purpose limitation (medical research only)
- Dataset scope constraints
- Privacy guardrails (differential privacy budgets)
- Success metrics
- Incentive logic (Social Credits)
- Redemption mechanics (Model Access)

### 2. The Vault - Execution Layer
Raw medical data is never shared. AI training occurs inside hardware-isolated secure execution environments (AWS Nitro Enclaves) with:
- Zero persistence of decrypted data
- Network egress denied by default
- Decryption only inside enclave memory
- Training with Differential Privacy (DP-SGD)
- Symmetric confidentiality (protects both hospital data and developer IP)

### 3. The Settlement - Incentive Layer
Model performance is evaluated inside the trusted boundary. When verified improvement is achieved:
- **Social Credits** (non-monetary, non-transferable) are minted deterministically
- Credits are allocated to institutions (hospitals), not individuals
- Standardized redemption: capped, time-bound API/inference credits for public-sector usage
- Settlement recorded on permissioned ledger (Amazon Managed Blockchain)

### 4. The Proof - Trust & Compliance Layer
Time-stamped Attestation Receipts provide:
- Cryptographic proof of policy enforcement
- Proof of differential privacy budget usage
- Proof of zero-persistence data destruction
- Notarized audit artifacts for DPDP Act compliance
- Judge-proof compliance statements

## 🔑 Key Innovations

### Social Credits Governance
- **Non-monetary, non-transferable** units of verified impact
- Minted based on **Normalized Improvement Score (NIS)**: `NIS = (NewMetric - BaselineMetric) / (1 - BaselineMetric)`
- Protocol-defined credit bands and hard caps prevent gaming
- Institutional-only allocation (no individual patient tracking)
- Role-based visibility controls

### Standardized Redemption
- Model Access = capped, time-bound API/inference credits only
- No lifetime free access, no unlimited licensing
- Prevents business clashes and protects developer IP
- Binary developer acceptance (accept or decline, no negotiation)

### Symmetric Confidentiality
- Hospital data protected via hardware isolation
- Developer IP protected via Enclave Image Files (.eif)
- Only aggregated outputs leave the enclave
- No proprietary code or weights exposed

### DigiLocker Institutional Verification
- Document-based institutional authentication
- Registration certificates, government affiliation documents
- **Not Aadhaar-based** - no biometric processing

## 📚 Documentation

### Specification Documents

- **[Requirements](/.kiro/specs/pramaan-health/requirements.md)** - 10 major requirements with acceptance criteria
- **[Design](/.kiro/specs/pramaan-health/design.md)** - Complete architecture, data models, 27 correctness properties
- **[Tasks](/.kiro/specs/pramaan-health/tasks.md)** - 17 implementation tasks with property-based testing

### Core Principles

1. **Credits are not money and cannot be cashed out**
2. **No individual patient tracking for credit allocation**
3. **Model access is standardized as capped API/inference credits**
4. **Developers accept a manifest or decline; no post-hoc negotiation**
5. **Institutional verification is document-based via DigiLocker; Pramaan does not access Aadhaar data**

## 🛠️ Technology Stack

### Primary Language
**Python** - for differential privacy (DP-SGD), ML workflows, and property-based testing

### AWS-Powered Architecture
- **AWS Nitro Enclaves** - Hardware-isolated secure execution
- **AWS KMS** - Key management with attestation-based release
- **Amazon S3** - Encrypted healthcare data storage
- **Amazon Managed Blockchain** - Permissioned settlement ledger

### Key Libraries
- **Pydantic** - Data models and validation
- **Opacus** - Differential privacy (DP-SGD)
- **FastAPI** - Secure APIs
- **Hypothesis** - Property-based testing
- **Cryptography** - Attestation and signing

## 🧪 Testing Strategy

Dual testing approach:
- **Property-Based Testing** - 27 correctness properties with 100+ iterations each using Hypothesis
- **Unit Testing** - Specific examples, edge cases, and integration tests
- **Security Testing** - Enclave boundary verification, privacy leakage testing
- **Compliance Testing** - DPDP Act compliance verification

## 🚀 Project Status

**Current Phase:** Architecture & Specification (Hackathon Pilot)

This is an **architecture-first pilot** demonstrating:
- Correctness and enforcement logic
- Trust guarantees and verifiable compliance
- Property-based testing of core protocols
- Architectural validation of the four-layer design

Not all AWS services are fully deployed end-to-end in this pilot.

## 🤝 Contributing

This is currently a pilot project for hackathon submission. Contributions, feedback, and discussions are welcome!

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

Inspired by India Stack primitives (UPI, Aadhaar, DigiLocker) and the vision of building public digital infrastructure for the AI era.

## 📧 Contact

For questions, collaborations, or feedback, please open an issue or reach out through GitHub.

---

**Built with the vision of making AI training trustworthy, verifiable, and fair for Bharat's data sovereignty.**
