# UltraLocked Security Documentation

UltraLocked is a commercial iOS file vault. These documents explain its local
vault encryption, portable export format, and security limitations.

**Updated September 26, 2026.** This revision corrects earlier claims about
perfect forward secrecy, networking, Secure Enclave execution, deletion and
independent auditing. The App Store version is 2.1.5; source changes prepared for
2.1.6 have not been submitted or released. See the release-status section of the
[technical white paper](whitepaper.md#release-status-and-evidence).

- [Overview](overview.md): a short explanation of the design and its limits.
- [Technical white paper](whitepaper.md): architecture, threat model and validation status.
- [Public security-core package](https://github.com/UltraLocked/security-core):
  the portable encrypted export format and its tests. This is not the full iOS
  app or proof of its production behavior.
- [Security reporting](SECURITY.md): how to report a vulnerability.

The commercial app, purchase UI, signing assets and backend deployment
configuration remain private. This documentation is not an independent security
audit or certification; no completed independent audit report is linked here.

[App Store](https://apps.apple.com/us/app/ultralocked/id6749434984) ·
[Website](https://ultralocked.com)

## License

This documentation is released under the Creative Commons
Attribution–NoDerivatives 4.0 International license. See [LICENSE](LICENSE).
The public security-core code has its own license.

© 2026 Lab 1908 LLC. All rights reserved.
