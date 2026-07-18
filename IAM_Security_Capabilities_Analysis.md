# IAM Security Capabilities Analysis for Banking (Mobile & Web)

**Prepared for:** Chief Information Officer  
**Domain:** Retail & Digital Banking — Mobile and Web Channels  
**Date:** July 2026  
**Classification:** Internal — Strategic Advisory

---

## Executive Summary

Selecting an Identity and Access Management (IAM) platform for a bank's digital channels is one of the most consequential security decisions a CIO will make. The platform must simultaneously satisfy regulatory mandates (PSD2/SCA, FFIEC, GDPR, DORA), defend against an increasingly sophisticated threat landscape (ATO, SIM-swap, deepfake social engineering), and deliver a frictionless experience that retains customers.

This document distils **20 years of IAM implementation experience** across global banking programmes into a structured evaluation framework. It identifies the **must-have capabilities**, ranks them by risk impact, and provides guidance on what to demand from vendors during RFP cycles.

---

## 1. Authentication Capabilities

### 1.1 Multi-Factor Authentication (MFA)

| Capability | Why It Matters for Banking | What to Look For |
|---|---|---|
| **Phishing-Resistant MFA** | SMS OTP and TOTP are vulnerable to real-time phishing and SIM-swap. Banks are high-value targets. | FIDO2/WebAuthn passkey support, device-bound credentials, hardware security key support. |
| **Step-Up / Adaptive Authentication** | Not every action carries the same risk. Login ≠ wire transfer. | Context-aware policy engine that can escalate authentication strength based on transaction value, geolocation, device trust, and behavioural anomalies. |
| **Passwordless Authentication** | Passwords are the #1 attack vector. Eliminating them reduces credential-stuffing, phishing, and help-desk cost. | Native passkey/biometric login for both mobile (Face ID, fingerprint) and web (platform authenticators), with graceful fallback. |
| **Out-of-Band (OOB) Verification** | Separating the authentication channel from the transaction channel defeats man-in-the-browser attacks. | Push notification approval with transaction signing (what-you-see-is-what-you-sign), number matching, and cryptographic binding. |

### 1.2 Biometric Authentication

| Capability | Details |
|---|---|
| **On-Device Biometrics** | Leverage the device's secure enclave (iOS Secure Enclave, Android StrongBox) — biometric data never leaves the device. |
| **Behavioural Biometrics** | Continuous, passive signals: typing cadence, swipe patterns, device hold angle, navigation rhythm. Detects session hijacking without user friction. |
| **Liveness Detection** | For selfie/video-based identity proofing, ensure the solution includes anti-spoofing (3D liveness, injection attack detection) to counter deepfakes and photo replay. |

### 1.3 Transaction Signing & Dynamic Linking (PSD2/SCA)

- Cryptographically bind the authentication to the specific transaction (payee + amount).
- Ensure the signing is performed in a secure execution environment on the user's device.
- Support dynamic linking display on a trusted channel independent of the browser.

---

## 2. Adaptive & Risk-Based Access Control

### 2.1 Real-Time Risk Engine

| Signal Category | Examples |
|---|---|
| **Device Intelligence** | Device fingerprint, jailbreak/root detection, emulator detection, app integrity (tamper/repackaging detection), screen-sharing detection. |
| **Network & Location** | IP reputation, VPN/proxy/Tor detection, impossible travel, geo-velocity, known bad ASNs. |
| **Behavioural Analytics** | Session velocity, navigation anomalies, mouse/touch movement patterns, time-of-day deviation from baseline. |
| **Threat Intelligence Feeds** | Compromised credential databases (e.g., Have I Been Pwned integration), fraud consortium data, dark web monitoring. |

### 2.2 Policy Decision Engine

- **Fine-grained, attribute-based policies (ABAC/PBAC):** Decisions based on user role, device posture, risk score, transaction context, and regulatory jurisdiction.
- **Real-time scoring:** Sub-100ms decision latency — anything slower degrades UX and becomes a bottleneck.
- **Explainable decisions:** Audit trail that shows *why* a particular authentication/authorization decision was made (critical for regulatory examinations).

### 2.3 Continuous Authentication / Session Intelligence

- Don't treat authentication as a one-time gate. Continuously evaluate risk throughout the session.
- Detect session anomalies: sudden device characteristic changes, cookie/token theft indicators, concurrent session from a new geography.
- Support session step-down (re-authentication) or termination when risk crosses a threshold mid-session.

---

## 3. Identity Proofing & Customer Onboarding (KYC Integration)

| Capability | Details |
|---|---|
| **Document Verification** | Automated ID document capture and verification (passport, national ID, driving licence) with NFC chip reading where available. |
| **Facial Match / Selfie Verification** | Compare selfie to ID document photo with certified liveness detection (ISO 30107-3 compliant). |
| **Database & Registry Checks** | Integration with government registries, credit bureaus, sanctions/PEP lists, and adverse media screening. |
| **Identity Wallet / Verifiable Credentials** | Forward-looking: support for receiving and verifying digital identity credentials (eIDAS 2.0, mDL, verifiable credentials). |
| **Fraud Consortium Integration** | Cross-institution signals to detect identity fraud rings (synthetic identities, mule accounts). |

---

## 4. Authorization & Access Governance

### 4.1 Fine-Grained Authorization

| Capability | Details |
|---|---|
| **Relationship-Based Access Control (ReBAC)** | Critical for banking: a user's access depends on their *relationship* to accounts (owner, joint holder, power of attorney, guardian). |
| **Transaction-Level Authorization** | Policies that evaluate not just "can this user access the payments service" but "can this user make a $50,000 international wire to this payee at this time." |
| **Externalized Authorization (Policy-as-Code)** | Decouple authorization logic from application code. Evaluate policies like OPA/Cedar/Topaz at a central decision point. |
| **Entitlement Management** | Role lifecycle (joiner/mover/leaver), periodic access reviews, segregation of duties (SoD) enforcement. |

### 4.2 API & Microservice Security

- **OAuth 2.0 / OIDC Compliance:** The IAM must serve as a standards-compliant authorization server for all APIs.
- **Token Security:** Short-lived access tokens, refresh token rotation, sender-constrained tokens (DPoP or mTLS), token binding to device.
- **Scope & Consent Management:** Granular scopes for Open Banking/PSD2 APIs; customer consent capture, storage, and revocation.
- **API Threat Protection:** Rate limiting, bot detection, token abuse detection integrated at the gateway layer.

---

## 5. Fraud Prevention & Threat Detection (Integrated with IAM)

| Capability | Details |
|---|---|
| **Account Takeover (ATO) Detection** | Credential stuffing detection, impossible travel, device change + password reset correlation, SIM-swap detection via carrier APIs. |
| **New Account Fraud (NAF)** | Synthetic identity detection, velocity checks on identity attributes (SSN, email, phone reuse), device intelligence during onboarding. |
| **Authorized Push Payment (APP) Fraud Signals** | Detect social engineering indicators: unusual session behaviour, coaching patterns, screen-sharing during high-value transactions. |
| **Bot & Automation Detection** | CAPTCHA-less bot detection, headless browser detection, automation framework fingerprinting. |
| **Dark Web Monitoring** | Proactive alerts when customer credentials, PII, or session tokens appear on dark web marketplaces. |

---

## 6. Mobile-Specific Security

| Capability | Details |
|---|---|
| **Mobile SDK Security** | SDK must include app shielding (anti-tampering, anti-debugging, obfuscation), secure storage of cryptographic keys, and certificate pinning. |
| **Device Binding** | Cryptographically bind a user's identity to a specific device using hardware-backed keys (Secure Enclave / StrongBox). |
| **Jailbreak / Root Detection** | Runtime detection with configurable policy responses (block, warn, step-up). |
| **Secure Channel (App-to-Server)** | Certificate pinning, mutual TLS, and protection against MITM proxies. |
| **Push Notification Security** | Encrypted push payloads, anti-replay, expiration, and protection against notification fatigue attacks (MFA bombing). |
| **In-App Biometric Binding** | Ensure biometric prompts are cryptographically bound to the authentication challenge — not just a local UI gate. |

---

## 7. Regulatory & Compliance Readiness

### 7.1 Standards & Regulation Matrix

| Regulation / Standard | Key IAM Requirement |
|---|---|
| **PSD2 / SCA** | Strong Customer Authentication with dynamic linking for payment transactions. |
| **FFIEC Guidance** | Layered security, risk-based authentication, customer awareness. |
| **GDPR / Data Privacy** | Consent management, data minimization, right to erasure of identity data, cross-border data transfer controls. |
| **DORA (EU)** | ICT risk management, resilience testing, third-party concentration risk reporting for IAM vendors. |
| **NIST 800-63-4** | Identity proofing (IAL), authentication (AAL), and federation (FAL) assurance levels. |
| **ISO 27001 / SOC 2 Type II** | Vendor must demonstrate certified security controls and provide audit reports. |
| **Open Banking Standards** | FAPI 2.0 compliance, consent APIs, TPP registration and management. |
| **eIDAS 2.0** | Acceptance of EU Digital Identity Wallet credentials for customer verification. |

### 7.2 Audit & Reporting

- Immutable, tamper-evident audit logs for all authentication and authorization events.
- Real-time dashboards for security posture: authentication success/failure rates, MFA adoption, risk score distributions.
- Regulatory-ready reports exportable for examiner review.
- SIEM/SOAR integration (Splunk, Sentinel, QRadar) via standard protocols.

---

## 8. Customer Experience & Usability

| Capability | Details |
|---|---|
| **Omnichannel Consistency** | Single identity across mobile app, web browser, ATM, branch, call centre, and chatbot. Session and context must carry across channels. |
| **Progressive Profiling** | Collect identity attributes incrementally over multiple sessions rather than demanding everything upfront at registration. |
| **Self-Service Account Recovery** | Secure, multi-factor account recovery flows that don't rely on knowledge-based questions (KBA is trivially defeated via social media/OSINT). |
| **Accessibility (WCAG 2.2)** | Authentication flows must be accessible to users with disabilities — alternative to biometrics, screen-reader compatible, sufficient timeouts. |
| **Localisation** | Multi-language, multi-locale support for authentication and consent screens. |
| **Low-Friction for Low-Risk** | Remember trusted devices, reduce MFA prompts for recognized sessions, one-tap approvals for routine actions. |

---

## 9. Architecture, Resilience & Integration

### 9.1 Deployment & Scalability

| Capability | Details |
|---|---|
| **Cloud-Native / Hybrid Deployment** | Support for private cloud, public cloud, and hybrid to meet data residency requirements. |
| **High Availability & Geo-Redundancy** | Active-active across multiple regions with automated failover. IAM downtime = banking downtime. |
| **Elastic Scalability** | Handle peak loads (salary day, tax deadlines, promotional campaigns) without degradation. |
| **Edge Authentication** | Process authentication decisions at the edge/CDN layer for latency-sensitive mobile interactions. |

### 9.2 Integration Ecosystem

| Integration Point | Requirement |
|---|---|
| **Core Banking / ESB** | Pre-built connectors or flexible API integration with core banking platforms (Temenos, Finastra, FIS, etc.). |
| **Fraud Platforms** | Bidirectional integration with fraud detection systems (NICE Actimize, Featurespace, SAS, etc.) for signal sharing. |
| **SIEM / SOAR** | Real-time event streaming for security operations. |
| **ITSM / IDAM Governance** | Integration with ServiceNow, SailPoint, Saviynt for identity governance and lifecycle management. |
| **Contact Centre** | Caller authentication via voice biometrics or secure IVR token exchange — agents should never handle passwords. |

### 9.3 Vendor Resilience & Exit Strategy

- **No single point of failure:** Evaluate the vendor's own infrastructure resilience, SLA history, and incident response track record.
- **Standards-based protocols:** OIDC, SAML, SCIM — avoid proprietary lock-in.
- **Data portability:** Ability to export all identity data, policies, and configurations in standard formats.
- **Multi-vendor strategy:** Consider whether the architecture allows swapping components (e.g., replace the risk engine without replacing the entire IAM stack).

---

## 10. Emerging Capabilities (2025–2027 Horizon)

| Emerging Capability | Strategic Relevance |
|---|---|
| **Decentralized Identity (DID / Verifiable Credentials)** | Customers present bank-issued or government-issued credentials without the bank storing PII centrally. Reduces data breach blast radius. |
| **AI/ML-Driven Identity Threat Detection** | Real-time anomaly detection that evolves with attacker tactics. Look for solutions with explainable AI (not black-box models) for regulatory acceptance. |
| **Continuous Access Evaluation Protocol (CAEP)** | Shared Security Signals Framework (SSF) — real-time event sharing between IAM, EDR, and CASB for instant session revocation. |
| **Passkey Ecosystem Maturity** | Cross-device passkey sync (iCloud Keychain, Google Password Manager), third-party passkey providers, enterprise passkey management. |
| **Generative AI Threat Vectors** | Deepfake voice/video for identity proofing attacks, AI-generated phishing at scale. IAM solutions must have counter-AI defenses. |
| **Quantum-Resistant Cryptography** | Post-quantum algorithms (ML-KEM, ML-DSA) for token signing, key exchange. Begin evaluating vendor PQC roadmaps now. |

---

## 11. Vendor Evaluation Criteria — Weighted Scorecard

Use the following weighted framework when scoring IAM vendors during the RFP process:

| # | Evaluation Category | Weight | Key Questions |
|---|---|---|---|
| 1 | **Authentication Strength & Flexibility** | 20% | Passkey/FIDO2 support? Adaptive step-up? Passwordless maturity? |
| 2 | **Risk Engine & Fraud Integration** | 18% | Real-time risk scoring? Behavioural analytics? Fraud platform integration? |
| 3 | **Regulatory Compliance** | 15% | PSD2/SCA certified? FAPI compliant? NIST 800-63 alignment? |
| 4 | **Mobile Security** | 12% | SDK hardening? Device binding? Biometric binding? Offline capability? |
| 5 | **Architecture & Resilience** | 10% | HA/DR design? Multi-region? Elastic scaling? Edge auth? |
| 6 | **Customer Experience** | 8% | Omnichannel? Self-service recovery? Accessibility? Low-friction for low-risk? |
| 7 | **Integration Ecosystem** | 7% | Core banking connectors? SIEM? Fraud? Governance platforms? |
| 8 | **Identity Proofing & KYC** | 5% | Document verification? Liveness? Verifiable credentials support? |
| 9 | **Future Roadmap** | 3% | DID/VC? PQC? AI/ML threat detection? CAEP/SSF? |
| 10 | **Vendor Viability & Support** | 2% | Financial stability? Banking references? 24/7 support? SLA history? |

---

## 12. Recommended Evaluation Approach

1. **Define your threat model first.** Catalogue your top 10 attack scenarios (ATO, credential stuffing, SIM-swap, insider threat, etc.) and score each vendor against those scenarios specifically.

2. **Demand a banking-specific proof of concept.** Generic demos are insufficient. Require the vendor to demonstrate:
   - End-to-end passkey registration and login (mobile + web).
   - Adaptive step-up for a high-value wire transfer.
   - Device binding and transaction signing on a mobile device.
   - Integration with your fraud platform (bidirectional signal flow).

3. **Test failure modes.** Evaluate what happens when the IAM platform is degraded or unreachable. Can users still authenticate? Is there a secure fallback? How quickly does the system recover?

4. **Assess operational burden.** The best IAM platform is one your team can actually operate. Evaluate policy authoring UX, monitoring dashboards, incident investigation workflows, and Day 2 operational complexity.

5. **Negotiate data ownership and exit terms upfront.** Ensure contractual rights to full data export in standard formats (SCIM, OIDC metadata) and a transition assistance period.

---

## 13. Summary — Non-Negotiable Capabilities Checklist

The following capabilities should be considered **non-negotiable minimums** for any IAM platform serving a bank's digital channels:

- [ ] FIDO2/WebAuthn passkey support (phishing-resistant MFA)
- [ ] Adaptive, risk-based authentication with real-time risk scoring
- [ ] Device binding with hardware-backed cryptographic keys
- [ ] Transaction signing with dynamic linking (PSD2/SCA)
- [ ] Behavioural biometrics or continuous session risk evaluation
- [ ] Mobile SDK with app shielding, jailbreak detection, and certificate pinning
- [ ] OAuth 2.0 / OIDC / FAPI 2.0 standards compliance
- [ ] Immutable audit logging with SIEM integration
- [ ] Self-service, multi-factor account recovery (no KBA)
- [ ] High availability with active-active geo-redundancy
- [ ] Vendor SOC 2 Type II and/or ISO 27001 certification
- [ ] Post-quantum cryptography roadmap

---

*This analysis is intended as a strategic starting point. Each capability should be further refined based on the bank's specific regulatory jurisdiction, existing technology landscape, customer demographics, and risk appetite.*
