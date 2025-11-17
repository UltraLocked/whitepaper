### **UltraLocked: A Whitepaper on Hardware-Anchored, Ephemeral Security for iOS**

**Version:** 1.0
**Date:** October 28, 2025

---

### **1.0 Executive Summary**

UltraLocked is a secure file vault for iOS, engineered from a zero-trust, offline-first philosophy. It provides military-grade privacy and plausible deniability by anchoring all cryptographic operations in the device's Secure Enclave. This hardware-level root of trust ensures that encryption keys are non-extractable, offering a degree of security that is physically impossible for software-only solutions to achieve.

This document details the technical architecture of UltraLocked, which is founded on three core principles:

1.  **Hardware-Anchored Cryptography:** All cryptographic operations are bound to the device's Secure Enclave, making secret key material inaccessible to the main processor, the operating system, or the application itself.
2.  **Per-File Perfect Forward Secrecy (PFS):** Every file is treated as a distinct cryptographic entity, encrypted with a unique, single-use key. The compromise of one file's key has no cryptographic impact on any other file in the vault.
3.  **Robust Emergency Failsafe Systems:** Advanced protocols, including a Duress Code system that activates a decoy vault while silently destroying the real one, provide users with powerful tools for plausible deniability and data protection under coercion.

By transparently outlining our threat models and security mechanisms, this paper aims to demonstrate that UltraLocked provides a new standard of personal data sovereignty and forensic resilience on a mobile platform. To ensure the integrity of our implementation, we are committed to undergoing a comprehensive, independent third-party security audit, the results of which will be made public.

---

### **2.0 Introduction: The Need for a New Security Paradigm**

#### **2.1 Limitations of Conventional Security Models**

The landscape of digital security is dominated by two primary models, both of which possess inherent limitations for users with extreme security needs:

*   **Cloud-Based "Zero-Knowledge" Services:** While these services prevent the provider from reading user data, they still require fundamental trust in the provider's infrastructure, implementation, and legal jurisdiction. They are vulnerable to server-side compromise, sophisticated metadata analysis, and compelled disclosure through legal channels. The user's data, though encrypted, resides on third-party hardware beyond their control.

*   **Standard On-Device Encryption:** Traditional secure storage apps often rely on a single master password or key to encrypt the entire vault. This creates a single point of failure; if the key is compromised, the entire dataset is vulnerable. Furthermore, these applications often fail to provide sufficient forensic resilience, leaving recoverable data fragments on disk, and offer no plausible deniability against coerced access.

#### **2.2 The UltraLocked Solution: A Hardware-Anchored, Ephemeral Approach**

UltraLocked was conceived to address these architectural limitations. It operates on the principle that the only truly trustworthy component is the user's own hardware. Our solution is built on three strategic shifts:

1.  **Shifting the Root of Trust:** We move the root of trust from a software-based password or a third-party server to the physically isolated Secure Enclave co-processor integrated into Apple's silicon.
2.  **Implementing an Ephemeral Keying System:** We abandon the single master key model in favor of a per-file cryptographic system. This granular approach dramatically minimizes the impact of any potential, however unlikely, cryptographic break.
3.  **Empowering the User with Failsafes:** We provide users with pre-configured, powerful tools for plausible deniability and data destruction, recognizing that the threat of physical coercion is a critical component of a comprehensive security model.


### **3.0 Core Principles & Design Philosophy**

The architecture of UltraLocked is not a collection of disparate features but a cohesive system built upon a set of immutable principles. These principles govern every design decision, from the lowest-level cryptographic operations to the highest-level user interface, ensuring a consistent and uncompromising security posture.

#### **3.1 Hardware as the Root of Trust**

All security guarantees in UltraLocked derive from the physical hardware of the user's device, specifically the Secure Enclave co-processor. We treat the main operating system and even the application itself as potentially untrustworthy environments. Secret key material, such as the master keys used for deriving file encryption keys, is generated within and confined to the Secure Enclave. This key material is non-extractable; it cannot be read, copied, or exfiltrated by any software running on the main application processor, including a compromised OS kernel. All sensitive cryptographic operations, such as signing and key agreement, are delegated to the Secure Enclave, which performs them in its isolated memory space and returns only the result. This design ensures that the ultimate security of the user's vault is anchored in the physical integrity of their device's silicon.

#### **3.2 Zero-Knowledge & Zero-Infrastructure**

UltraLocked is engineered to operate 100% offline. The application contains no networking code for communicating with developer-controlled servers. This architectural choice eliminates entire classes of vulnerabilities by design:
*   There are no remote servers to attack.
*   There is no user data, metadata, or analytics to be breached or subpoenaed.
*   There is no user account system to compromise.

We, the developers, have no knowledge of our users or their data. The user is the sole custodian of their information. This model provides the strongest possible guarantee of privacy, as there is simply no infrastructure to trust or attack.

#### **3.3 Ephemeral by Default**

Data persistence is treated as a liability. To minimize the forensic footprint, cryptographic materials are designed to be ephemeral. For each file encryption or decryption operation, a unique, single-use key is derived through a hardware-backed process. This key exists only in volatile memory for the brief moment it is needed and is securely wiped immediately after the operation is complete. It is never written to persistent storage. This principle extends to the data itself through user-configurable self-destruct timers (Time-To-Live), ensuring files do not outlive their intended lifespan.

#### **3.4 Plausible Deniability**

A core tenet of the UltraLocked design is to provide the user with a credible means of denying the existence of their primary vault when under duress. This is more than just hiding data; it is about providing a functional, convincing alternative that can satisfy an adversary's demands. The Duress Code system is the primary mechanism for this, activating a separate, pre-populated decoy vault. The activation of this decoy vault serves as a silent trigger for the cryptographic destruction of the real vault's keys, protecting the user by making the authentic data permanently irrecoverable.

#### **3.5 Defense-in-Depth**

UltraLocked's security is not reliant on a single mechanism. Instead, it employs multiple, independent layers of protection to create a resilient system with no single point of failure. These layers include:
1.  **Hardware:** The physical isolation of the Secure Enclave.
2.  **Operating System:** The security features of the iOS sandbox.
3.  **Cryptography:** The per-file Perfect Forward Secrecy model.
4.  **Application Logic:** Continuous, real-time threat monitoring for signs of environmental compromise.
5.  **User-Defined Protocols:** Configurable failsafes like the Dead Man's Switch.

A failure or bypass of one layer is intended to be caught or mitigated by another, ensuring that the integrity of the user's vault is maintained even under adverse conditions.


### **4.0 System Architecture**

The UltraLocked architecture is a multi-layered, defense-in-depth model designed for compartmentalization and resilience. Each layer operates with a minimum of trust in the layers above it, with the hardware serving as the ultimate root of trust. This design ensures that a compromise at a higher level, such as the application UI, does not automatically compromise the lower-level cryptographic core.

#### **4.1 Architectural Overview**

The system is conceptually divided into five distinct layers, each with a specific responsibility:

*   **Layer 1: Hardware & OS Foundation:** This is the immutable base upon which all security is built. It consists of the iOS Sandboxed Environment, the system Keychain for storing key references, and, most critically, the Secure Enclave for all cryptographic operations.
*   **Layer 2: Cryptographic Core:** A dedicated, isolated set of services responsible for all encryption, decryption, and key management. It is the sole component that interfaces directly with the Secure Enclave for cryptographic tasks, acting as a gatekeeper for hardware-backed security.
*   **Layer 3: Security & Monitoring Services:** A suite of independent, parallel monitors that continuously assess the device's state for signs of tampering or compromise. These services monitor screen activity, clipboard state, network connections, device integrity, and debugger attachment.
*   **Layer 4: Centralized Threat Coordination:** This layer acts as the system's security nerve center. It receives threat events from the monitoring services, assesses the cumulative risk, and executes a proportional response based on a pre-defined security policy.
*   **Layer 5: Vault & Application Logic:** The user-facing components that manage files, settings, and interactions. This layer operates on a zero-trust basis, treating all underlying data as hostile until it has been authenticated and securely decrypted by the lower layers.

The interaction between these layers is strictly controlled to maintain compartmentalization, as illustrated below.

```mermaid
graph TD
    subgraph "UI & Application Logic (Layer 5)"
        A1[Vault & Editor UI]
        A2[App Lock UI]
        A3[Settings UI]
    end

    subgraph "Threat Coordination (Layer 4)"
        B1[TamperResponseCoordinator]
    end

    subgraph "Security & Monitoring Services (Layer 3)"
        C1[ScreenSecurityManager]
        C2[AntiDebuggingManager]
        C3[NetworkThreatMonitor]
        C4[DeviceAttestationManager]
    end

    subgraph "Cryptographic Core (Layer 2)"
        D1[HardwareBackedCryptoService]
        D2[SecureFileDeletion]
        D3[KeyRotationManager]
    end

    subgraph "Hardware & OS Foundation (Layer 1)"
        E1[Secure Enclave]
        E2[Keychain]
        E3[File System]
    end

    %% Flow of Control and Data
    A1 & A2 & A3 --> B1
    C1 & C2 & C3 & C4 --> B1
    B1 --> A2 & A1
    A1 --> D1 & D2
    D1 & D2 & D3 --> E1 & E2 & E3
```

#### **4.2 The Secure Data Model**

All user data is stored within the application's sandboxed App Group container, which allows for secure, high-speed data sharing between the main application and its Share Extension. The on-disk structure is designed to reveal minimal information.

*   **Encrypted File Wrapper:** Individual files are not stored as raw encrypted blobs. Instead, they are encapsulated within a structured `EncryptedFileWrapper` object. This wrapper contains the encrypted file content (ciphertext), the unique ephemeral public key used for that specific encryption session, a cryptographic salt used in key derivation, and a hardware-backed digital signature (ECDSA) that authenticates the integrity of the entire wrapper. This structure is critical for enabling Perfect Forward Secrecy.
*   **Encrypted Metadata Database:** The list of vault items, including their creation dates, display names, and TTL settings, is stored in a single metadata file. This file contains no sensitive content or plaintext filenames. The entire metadata file is itself encrypted using the same hardware-backed cryptographic core, ensuring that the vault's structure and contents are unintelligible without proper authentication.

The resulting file system layout is simple and forensically opaque:

```mermaid
graph LR
    subgraph App Group Container
        direction LR
        VaultDir["/Vault"]
        subgraph VaultDir
            direction TB
            FilesDir["/Files"]
            Metadata["metadata.json.encrypted"]
        end
        subgraph FilesDir
            direction TB
            File1["{uuid-1}.dat"]
            Thumb1["thumb_{uuid-1}.dat"]
            File2["{uuid-2}.dat"]
            Thumb2["thumb_{uuid-2}.dat"]
        end
    end
```

### **6.0 Threat Modeling & Mitigations**

The security architecture of UltraLocked is purpose-built to counter a range of modern digital and physical threats. This section outlines the primary threat vectors considered during development and the specific architectural features designed to mitigate them.

#### **6.1 Acknowledged Limitations & Residual Risks**

While UltraLocked implements industry-leading security practices, we acknowledge the practical limitations of our mitigations and the residual risks that remain inherent to any security system:

**Secure Deletion on Flash Storage:** UltraLocked employs a multi-pass overwrite strategy via `SecureFileDeletion` to sanitize file blocks before unlinking. However, we acknowledge the inherent challenges of guaranteed data erasure on modern flash storage (SSDs). Due to wear-leveling algorithms managed by the hardware controller, the physical blocks may not be overwritten as intended. This process should be considered a significant deterrent that dramatically increases the cost and complexity of data recovery, rather than a guarantee of absolute forensic impossibility.

**Secure Enclave Vulnerabilities:** Our security model's root of trust is the Secure Enclave. While this provides industry-leading hardware isolation, we recognize that it is not immune to advanced, physical side-channel attacks (e.g., power analysis, fault injection) or potential vulnerabilities in Apple's proprietary firmware. Our defense-in-depth strategy aims to mitigate risks, but we operate under the assumption that a sufficiently motivated state-level actor may possess undisclosed methods to target this hardware.

**Memory Forensics:** The `SecureMemoryManager` uses `mlock` to prevent sensitive cryptographic material from being paged to disk. However, we acknowledge that in certain edge cases, such as OS-level hibernation or system crashes, memory contents could still be written to persistent storage. Our ephemeral keying model is designed to minimize the window of exposure for any single key.

**Duress System Limitations:** Upon duress code entry, the destruction of the real vault is initiated as a background task. This process is not instantaneous and its completion time depends on the size of the vault. A sophisticated adversary could potentially halt this process by immediately placing the device in airplane mode and powering it down for forensic imaging. The design goal is to make the destruction process begin immediately and silently, maximizing the probability of completion before an adversary can intervene.

**Forensic Traces of Duress Activation:** The activation of duress mode sets a flag in `UserDefaults` to maintain the decoy state across app launches. While this flag is cryptographically unsigned and could be dismissed as a transient state, a sophisticated forensic analyst might interpret it as evidence of duress system activation. This represents a calculated trade-off between functionality and perfect forensic stealth.

| Threat Vector | Description | Primary Mitigation(s) |
| :--- | :--- | :--- |
| **Coercive Access** | An attacker physically forces the user to unlock their device and the app ("Five-Dollar Wrench Attack"). The goal is to gain access to the real, sensitive data. | **Duress Code System:** The primary defense. Entering the duress PIN reveals a plausible decoy vault while silently triggering the cryptographic destruction of the real vault, protecting the user by making the authentic data permanently irrecoverable. |
| **Device Seizure & Forensic Analysis** | The user's device is confiscated and subjected to advanced offline analysis to recover data, keys, or user activity. | **Hardware-Anchored Keys:** Master keys are confined to the Secure Enclave and are non-extractable, preventing them from being recovered even with low-level access to the device's flash storage. <br><br> **Per-File PFS:** Since each file uses a unique key, forensic recovery would require a separate, computationally intensive attack for every single file. <br><br> **Secure Memory Management:** Prevents sensitive key material from being written to swap files on disk and disables core dumps, minimizing the forensic data available for cold boot or memory dump attacks. <br><br> **Secure File Deletion:** Uses multi-pass overwrites to sanitize storage blocks before a file is deleted, making data recovery significantly more difficult than a standard file system unlink. |
| **Malicious Software / Malware** | A malicious application or profile on the device attempts to escalate privileges to access UltraLocked's sandboxed data or keychain entries. | **iOS App Sandboxing:** The fundamental OS-level protection that isolates the app's data container. <br><br> **Device Attestation:** Actively checks for signs of a compromised environment (e.g., jailbreak), which is often a prerequisite for cross-app malware. If detected, security policies can be enforced to lock or wipe the vault. |
| **Shoulder Surfing / Screen Capture** | An observer or a malicious process (e.g., spyware) views or records the screen to capture sensitive information or the user's PIN. | **Screen Security Monitor:** Detects active screen recording or mirroring (e.g., AirPlay) and automatically applies a privacy overlay to obscure all content. <br><br> **Anti-Debugging:** Prevents attackers from attaching debuggers, which are a common tool for runtime analysis and screen inspection. |
| **Network-Based Attacks (MITM)** | An attacker on a hostile network (e.g., public Wi-Fi) attempts to intercept or manipulate traffic, primarily to attack potential future features or OS-level services. | **Network Threat Monitor:** Although the app is offline-first, this monitor provides a layer of defense by detecting insecure Wi-Fi, suspicious proxies, and potential SSL/TLS interception, which could be used in more advanced, chained attacks. It can trigger alerts or lock the vault if a high-risk network is detected. |
| **Application Tampering** | The app's binary is modified (e.g., repackaged with malicious code) to bypass security checks and exfiltrate data. | **Device Attestation:** Performs code integrity and bundle signature checks on launch. If the application's signature does not match the officially signed version, it indicates tampering, and the app will refuse to run or will immediately wipe the vault. |
| **User Incapacitation** | The user loses their device, is incapacitated, or is otherwise unable to access it for an extended period, risking eventual compromise. | **Dead Man's Switch:** Acts as an ultimate failsafe, automatically and securely wiping the entire vault if the user does not perform an authenticated "check-in" within a pre-configured time window. <br><br> **Self-Destruct Timers (TTL):** Ensures that individual files with a limited lifespan are automatically and securely deleted, minimizing the window of exposure for time-sensitive data. |

---

### **6.2 Key Management & Recovery**

UltraLocked's approach to key management is fundamentally different from conventional secure storage solutions. Understanding this philosophy is critical for users who require the highest levels of security.

**The No-Backup Philosophy:** UltraLocked is designed as a 'single-source-of-truth' vault. All cryptographic keys are anchored to the device's unique hardware through the Secure Enclave. Consequently, there is **no cloud backup or key recovery mechanism by design**. This is a fundamental architectural choice that prioritizes security against remote compromise over data recovery convenience.

**Master Key Provisioning:** The initial `masterKeyAgreementKey` and `masterSigningKey` in the `SecureEnclaveManager` are generated on first application launch using `SecKeyCreateRandomKey` with Secure Enclave attributes. These keys are created once per application installation and remain permanently bound to the device's hardware. If this initial provisioning process is interrupted (e.g., device shutdown during key generation), the application will detect the incomplete state on next launch and restart the key generation process with a clean slate.

**Device Loss Scenario:** Users must understand that if the device is lost, stolen, or its Secure Enclave becomes permanently inaccessible (e.g., hardware failure), the vault data becomes irrecoverable by design. This includes scenarios such as:
- Physical device destruction or theft
- Secure Enclave hardware failure
- iOS system corruption affecting Keychain access
- User forgetting both their main PIN and duress PIN

**Error Handling in Cryptographic Operations:** The system implements robust error handling for cryptographic failures. If `SecKeyCreateRandomKey` fails during key generation, the system retries up to three times with brief delays between attempts. If ECDH key agreement fails during file operations, the system logs the error and prevents the file operation from proceeding, maintaining data integrity. Users are advised to maintain their own independent, secure backup strategies for truly critical information that must survive device loss.

---

### **7.0 Privacy Posture**

UltraLocked's commitment to user privacy is absolute and is reflected in its core architectural design.

7.1.  **No Data Collection:** The application collects no analytics, no diagnostics, and no user data of any kind. There is no code included for tracking user behavior, session length, or feature usage.

7.2.  **Offline by Design:** The app contains no networking code for communicating with a central or third-party server. This eliminates any possibility of remote data exfiltration, metadata collection, or server-side logging. All operations, including security checks and cryptographic functions, are performed entirely on-device.

7.3.  **Metadata Sanitization:** To protect user anonymity, UltraLocked automatically strips potentially identifying metadata (e.g., EXIF GPS coordinates, camera model information) from media files upon import into the vault. The goal is to store only the essential content, divorced from its potentially revealing context.

---

### **8.0 Conclusion**

UltraLocked represents a significant step forward in personal data security on mobile devices. By moving the root of trust to the hardware and designing for failure, it provides a resilient and trustworthy environment for sensitive information. Its architecture is built not just to protect data from unauthorized access, but to provide the user with credible tools for plausible deniability and forensic resilience in the most challenging of circumstances. We believe this transparent, user-controlled model is the future of personal privacy.

To maintain the highest standards of security and transparency, we are committed to subjecting UltraLocked to rigorous independent security auditing. The results of these audits will be made publicly available, ensuring that our security claims can be independently verified and that any discovered vulnerabilities are promptly addressed.

---

### **Appendix**

#### **A.1 Glossary of Terms**

*   **Secure Enclave:** A dedicated, hardware-based security co-processor in Apple's chips that is isolated from the main processor [Apple Platform Security Guide]. It handles cryptographic operations and ensures that secret key material is never exposed to the operating system.
*   **Perfect Forward Secrecy (PFS):** An encryption property ensuring that if a long-term key is compromised, past session keys (and thus past encrypted data) are not compromised. In UltraLocked's context, it refers to the per-file ephemeral keying system.
*   **AES-256-GCM:** Advanced Encryption Standard with a 256-bit key in Galois/Counter Mode [NIST SP 800-38D]. A modern, authenticated encryption algorithm that provides both confidentiality and integrity.
*   **ECDH (Elliptic-Curve Diffie-Hellman):** A key agreement protocol that allows two parties, each having an elliptic-curve public-private key pair, to establish a shared secret over an insecure channel.
*   **HKDF (HMAC-based Key Derivation Function):** A function that takes a potentially weak secret and a salt and produces a stronger, cryptographically secure key [RFC 5869].
*   **Plausible Deniability:** The ability for a user to credibly deny the existence of a piece of information. In UltraLocked, this is achieved via the Duress Code and decoy vault.
*   **Forensic Resilience:** The ability of a system to resist forensic analysis and data recovery efforts after data has been deleted.

#### **A.2 Cryptographic Standards References**

*   **Apple Platform Security Guide:** [https://support.apple.com/guide/security/welcome/web](https://support.apple.com/guide/security/welcome/web)
*   **NIST Special Publication 800-38D:** Recommendation for Block Cipher Modes of Operation: Galois/Counter Mode (GCM) and GMAC.
*   **RFC 5869:** HMAC-based Extract-and-Expand Key Derivation Function (HKDF).