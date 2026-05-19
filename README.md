# UltraLocked Security White Paper

This repository contains the public white paper for **UltraLocked** — a secure, hardware-bound, zero-trust file vault for iOS. The white paper describes the system's architecture, threat model, cryptographic design, and tamper-response mechanisms in detail.

## 📱 Download

**UltraLocked** is available on the App Store:
[Download on the App Store](https://apps.apple.com/us/app/ultralocked/id6749434984)

Website: [ultralocked.com](https://ultralocked.com)

## 🔐 Purpose

This document is intended for:
- **Security engineers** evaluating UltraLocked’s design guarantees
- **Auditors and compliance teams** reviewing risk posture
- **Researchers and cryptographers** validating or reusing cryptographic patterns
- **Technical decision-makers** assessing adoption risk

It aims to establish **technical trust** through transparency and reproducibility. The commercial iOS app remains private and subscription-funded, while the portable encrypted bundle format and security-critical documentation are published in the public [`security-core`](https://github.com/UltraLocked/security-core) repository.

## 📄 Contents

- [`whitepaper.md`](./whitepaper.md) — Technical security whitepaper  
- [`overview.md`](./overview.md) — Layperson whitepaper explainer

## 🔎 Public Security Code

The public security core is available at:
[github.com/UltraLocked/security-core](https://github.com/UltraLocked/security-core)

It includes the Swift package for `.ultralocked` encrypted export bundles, malformed-bundle tests, compatibility tests, and the public threat model. It does not include the commercial app shell, subscription UI, App Store configuration, signing material, outreach tooling, or backend deployment state.

## 🛡️ License

This white paper is released under the **Creative Commons Attribution–NoDerivatives 4.0 International (CC BY-ND 4.0)** license.  
You may redistribute it (even commercially) as long as you do not modify it and provide attribution.  
See [`LICENSE`](./LICENSE) for full terms.

## 📫 Security Contact

To report a vulnerability, please refer to [`SECURITY.md`](./SECURITY.md)  
or email: **security@ultralocked.com**

---

**App Store:** [Download UltraLocked](https://apps.apple.com/us/app/ultralocked/id6749434984)
**Website:** [ultralocked.com](https://ultralocked.com)

© 2025 Lab 1908 LLC. All rights reserved.
