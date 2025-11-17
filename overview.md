# UltraLocked: An Overview of Our Approach to Mobile Data Sovereignty

**Version:** 1.1  
**Last Updated:** July 31, 2025

---

## 🔒 What Is UltraLocked?

UltraLocked is a secure file vault for iOS that provides hardware-anchored, ephemeral security for your most sensitive files — documents, photos, recordings — using military-grade cryptographic protection rooted in your device's Secure Enclave hardware.

It operates on a zero-trust, offline-first philosophy. There are no cloud servers, no user accounts, and no network communications. Your data remains exclusively under your control, protected by cryptographic keys that are physically bound to your device's hardware and cannot be extracted by any software, including the application itself.

---

## 🧠 Why It Matters

Conventional "secure" applications suffer from fundamental architectural limitations:

- **Cloud-based solutions** require trust in third-party infrastructure, legal jurisdictions, and provider implementations
- **Software-only encryption** creates single points of failure and leaves recoverable forensic traces
- **Traditional vaults** lack plausible deniability and emergency failsafe mechanisms

UltraLocked addresses these limitations through hardware-anchored cryptography using the **Secure Enclave** — a tamper-resistant co-processor that performs all cryptographic operations in physical isolation from the main system.

This architecture ensures:
- Cryptographic keys are non-extractable, even with device-level access
- File compromise is cryptographically isolated (Perfect Forward Secrecy)
- Users possess credible tools for plausible deniability under coercion
- Forensic resilience through secure deletion and memory protection

---

## 🛡️ Core Security Mechanisms

### 1. **Hardware-Anchored Cryptography**
All encryption keys are generated within and confined to the Secure Enclave co-processor. Master key material is **non-extractable** — it cannot be read, copied, or exfiltrated by any software running on the main processor, including a compromised operating system.

### 2. **Per-File Perfect Forward Secrecy**
Every file is encrypted with a **unique, ephemeral key** derived through hardware-backed key agreement (ECDH) and key derivation (HKDF) [RFC 5869]. Each key is used once and immediately destroyed, ensuring that compromise of any single file provides no cryptographic advantage for attacking others.

### 3. **Zero-Infrastructure Privacy Model**
UltraLocked contains no networking code and operates 100% offline. This eliminates entire classes of vulnerabilities by design — there are no servers to attack, no user data to breach, and no metadata to subpoena.

### 4. **Duress Code & Plausible Deniability**
A sophisticated dual-PIN system provides users with a credible defense against coercive access:
- **Primary PIN:** Unlocks the real vault
- **Duress PIN:** Activates a convincing decoy vault while **silently destroying the real one**

The destruction process is designed as a background task that begins immediately upon duress activation.

### 5. **Comprehensive Emergency Protocols**
Multiple automated failsafes protect against various threat scenarios:
- **Dead Man's Switch:** Auto-destruction if device remains inaccessible beyond configured timeframe
- **Time-To-Live (TTL):** Individual file expiration for time-sensitive data
- **Threat Response System:** Real-time monitoring for screen recording, debugging, and tampering attempts

---

## 🧬 Technical Differentiators

| Security Aspect | UltraLocked | Conventional Apps |
|----------------|-------------|-------------------|
| **Root of Trust** | Hardware (Secure Enclave) | Software password or cloud service |
| **Key Architecture** | Per-file ephemeral keys | Single master key or password |
| **Network Posture** | 100% offline by design | Cloud-dependent or network-enabled |
| **Forensic Resistance** | Multi-pass secure deletion, memory protection | Standard file deletion, memory traces |
| **Plausible Deniability** | Hardware-backed duress system | Typically absent |
| **Threat Monitoring** | Real-time tamper detection | Limited or reactive security |
| **Recovery Model** | No-backup by design (hardware-bound) | Cloud recovery or password reset |
| **Audit Transparency** | Public whitepaper, committed to independent audit | Often proprietary or opaque |

---

## 🔐 Intended Use Cases

**High-Risk Individuals:**
- Investigative journalists protecting sources
- Human rights activists in hostile environments
- Travelers to surveillance states
- Whistleblowers and dissidents

**Privacy-Conscious Professionals:**
- Legal professionals handling privileged communications
- Healthcare workers managing sensitive patient data
- Financial advisors protecting client information
- Executives handling confidential business data

**Security-Aware Users:**
- Anyone requiring true local data sovereignty
- Users who distrust cloud-based security models
- Individuals facing potential device seizure or coercion

---

## ⚠️ Important Limitations & Trade-offs

UltraLocked prioritizes security over convenience through deliberate design choices:

**No Recovery Mechanism:** All cryptographic keys are hardware-bound. If your device is lost, destroyed, or becomes inaccessible, your vault data is **permanently irrecoverable**. Users must maintain independent backup strategies for critical information.

**Device-Specific Security:** Protection is anchored to your specific device's hardware. The vault cannot be migrated or synchronized across devices.

**Forensic Considerations:** While UltraLocked implements best-practice secure deletion, modern SSD wear-leveling may prevent guaranteed data erasure. Our approach significantly increases recovery costs and complexity but cannot guarantee absolute forensic impossibility.

**Threat Model Boundaries:** The Secure Enclave, while providing industry-leading protection, is not immune to sophisticated physical attacks or potential firmware vulnerabilities. Our defense-in-depth approach mitigates but cannot eliminate all theoretical attack vectors.

---

## 🧾 Privacy Commitment

**Zero Data Collection:**
- No analytics, diagnostics, or user behavior tracking
- No network communications or server infrastructure
- No user accounts, identifiers, or registration
- No crash reports or error telemetry

**Metadata Protection:**
- Automatic stripping of identifying metadata (EXIF, location data)
- Encrypted filename and vault structure storage
- No forensic breadcrumbs in system logs or caches

**Transparency & Verification:**
- Public technical documentation and threat modeling
- Commitment to independent security auditing
- Open discussion of limitations and residual risks

---

## 📘 Technical Documentation

- 📋 **[Complete Technical Whitepaper →](./whitepaper.md)** — Detailed architecture, cryptographic implementation, and threat analysis
- 🔍 **[Security Model →](./SECURITY.md)** — Vulnerability reporting and security contact information

---

*UltraLocked is committed to advancing the state of personal data sovereignty through transparent, hardware-anchored security. To ensure the integrity of our implementation, we are undergoing comprehensive independent security auditing, with results to be made publicly available.*

© 2025 UltraLocked. All rights reserved.
