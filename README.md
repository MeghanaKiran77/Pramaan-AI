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

## 🏗️ Architecture Overview

Pramaan-Health implements a **four-layer architecture**:

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
