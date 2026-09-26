# UltraLocked Security Architecture and Threat Model

Documentation revision: September 26, 2026.

This document describes the local vault and portable export designs, their trust
boundaries, and the status of ongoing remediation. It replaces earlier claims
that overstated forward secrecy, absence of networking, forensic erasure and
independent auditing. Design descriptions are not proof that a particular
released binary implements every control correctly.

## Release status and evidence

As of this revision, the App Store version is **2.1.5**. Source changes prepared
for **2.1.6, build 26**, are **not submitted or released**. The following distinction
is essential when evaluating an installed app:

| Area | Prepared 2.1.6 behavior; still subject to release validation |
| --- | --- |
| Key lifecycle | Preserve existing keys on lookup errors; refuse automatic reprovisioning of an existing vault; require newly created keys to persist and reload. |
| Rotation | Disable automatic rotation and reject manual rotation/rollback until a safe migration is implemented. |
| Emergency response | Persist pending destruction, block vault cryptography, invalidate local vault keys before content cleanup, and retain a blocked state if cleanup fails. |
| Authentication | Enforce biometric and configured location/time checks; treat local device checks as heuristics, not verified remote attestation. |
| Deadline enforcement | Check the persisted deadline before vault cryptography, including after relaunch. |
| Speech | Require supported on-device recognition; fail rather than fall back to server recognition. |
| Purchases | Use StoreKit-verified current entitlements; do not make access depend on the optional receipt service. |
| Local privacy | Reduce sensitive release logging, protect preview writes and exclude vault/secrets directories from device backup. |

These fixes are not a security guarantee for 2.1.5. Corrected website wording and
receipt-service validation have been deployed separately; neither changes an
already installed iOS binary.

Development tests cover key-provisioning policy, persisted deadlines, configured
time windows and simulated purchase/revocation/expiry behavior. Package tests
cover bundle round trips, malformed input and authentication failures.
Simulator results do not establish Secure Enclave persistence, interrupted wipe
behavior, physical-device authentication enforcement or Apple's production
subscription behavior. Disposable-device and Apple sandbox checks remain release
requirements. This work is engineering review and remediation, **not an
independent audit or security certification**. No completed independent audit
report accompanies this document.

## Scope and public code

The [security-core repository](https://github.com/UltraLocked/security-core)
contains the `UltraLockedFormat` Swift package for portable `.ultralocked`
exports, its tests, and format documentation. It does not contain the full iOS
app, local vault managers, StoreKit UI, signing assets or backend deployment
configuration. Reviewing that package cannot establish the correctness of the
private app's authentication, emergency response or production infrastructure.

## Local vault cryptography

The local vault uses persistent P-256 keys backed by the Secure Enclave for key
agreement and signing. For each encrypted object, the app creates a temporary
P-256 key pair, stores its public key, and requests ECDH with the persistent
agreement key. It derives a 256-bit symmetric key with HKDF-SHA256, a random salt
and file-specific context. CryptoKit AES-256-GCM encrypts the object. The stored
wrapper includes the object identifier, temporary public key, salt, encrypted
data and a signature produced with the signing key. Decryption verifies the
signature and derives the symmetric key again.

**This is per-object key derivation, not perfect forward secrecy.** Retaining
the persistent agreement key and the stored public parameters enables recovery
of earlier derived keys. Compromise or unauthorized use of that persistent key
can therefore affect earlier objects. The legacy HKDF context contains the
string `UltraLocked-PFS-v1`; it is a compatibility label, not a security property.
Changing that context without migration would make existing objects unreadable.

The Secure Enclave protects its private keys and performs the associated private
key operations. AES-GCM encryption, HKDF and handling of plaintext happen outside
the Enclave. Derived secrets and plaintext can exist in app or framework memory.
Memory clearing attempts cannot guarantee erasure of every Swift, CryptoKit or
system copy. Non-exportable keys do not prevent all misuse on a compromised OS.

Key lifecycle is part of data availability: a lookup error must not be treated
as permission to replace a key. The prepared update preserves existing key tags
and disables unsafe rotation; it does not claim transactional key migration.

## Portable encrypted exports

An explicit export creates an independently encrypted, passphrase-protected
bundle. It does not copy the local Secure Enclave private keys. The public format
uses Argon2id to derive a master key, HKDF-SHA256 to separate manifest and item
keys, and AES-256-GCM to encrypt the manifest and items. Header bytes are
associated authenticated data; item authentication also binds the item ID.

The parser bounds sizes and KDF parameters, checks reserved header fields, and
uses authenticated manifest descriptors when locating and decrypting items.
Authentication of item ciphertext occurs when that item is decrypted; unlocking
the manifest alone is not verification of every payload. Public headers, overall
size and the existence of a bundle remain observable.

See the package's [format specification](https://github.com/UltraLocked/security-core/blob/main/docs/vault-format.md)
and [threat model](https://github.com/UltraLocked/security-core/blob/main/docs/threat-model.md)
for limits and assumptions. Parser limits reduce resource-exhaustion risk; they
do not imply zero memory or CPU cost for hostile input.

Exports permit transfer/recovery with the passphrase. They also introduce an
offline password-guessing target. Losing the passphrase can make an export
unreadable. Deleting device keys does not invalidate an export, and deleting one
copy does not delete copies held by recipients or other services.

## Storage, metadata and previews

Vault content is stored in the app's local storage. The prepared update applies
iOS file protection to preview writes and excludes vault/secrets directories
from device backup. These settings are platform controls, not evidence that old
backups or previously shared copies have been removed.

Rendering, recording, importing and sharing require additional plaintext or
staging paths. Filenames, file sizes, settings, pending-share metadata and
filesystem traces must not be assumed universally hidden. The documentation does
not promise comprehensive metadata stripping or an absence of forensic traces.

## Authentication and device checks

App authentication and hardware key protection are separate controls. Interactive
biometric, PIN, location and time checks depend on the relevant app paths and
iOS APIs. The prepared changes replace permissive helper behavior with enforced
checks or a requirement to complete the interactive authentication flow.

Local jailbreak/debugging/integrity checks are heuristics. They can produce false
positives and be bypassed by an attacker with sufficient control. No verified
server-backed device attestation is claimed. Location checks depend on permissions,
accuracy and fresh readings; time controls depend on device time. Neither is
proof against a fully compromised device or manipulated environment.

## Emergency cleanup, duress and deadlines

Emergency cleanup is intended to invalidate the local vault's keys and remove
local content and staging data. The prepared update records pending destruction
before blocking vault cryptography and attempting key deletion. If cleanup fails,
access remains blocked and cleanup can be retried. Real-device testing must
confirm key invalidation and restart behavior before release.

Ordinary per-item deletion is not independently verified cryptographic erasure.
The ordinary Settings wipe clears the Vault tab while retaining Crypto Secrets,
PIN and settings; it must not be confused with emergency key destruction.

A duress PIN and decoy cannot guarantee plausible deniability or personal safety.
State flags, app presence, timing and other artifacts can reveal their existence.
A deadline cannot execute while the device is powered off, and iOS does not
promise continuous background execution. Checking an expired deadline before
cryptography on resume is different from erasing data at the exact expiry time.
Device-time manipulation is also outside a guaranteed deadline policy.

Flash wear leveling, system snapshots, memory copies and exported data prevent an
absolute erasure guarantee. Multi-pass overwrite does not establish physical
sanitization. Uninstalling is not a verified key-destruction procedure. No claim
is made that forensic recovery is impossible or that its cost has been measured.

## Networking and service boundaries

The app does not offer developer-hosted cloud vault storage. It does use network
services: Apple handles purchases and restores, and released app versions can
contact a developer-operated receipt-validation endpoint. Purchase data and
service metadata are distinct from vault file contents. User-directed export or
sharing can send content through a chosen provider.

Earlier releases may use server-based speech recognition through Apple's
framework. The prepared update requires on-device recognition and rejects
unsupported cases. This change cannot be assumed active in the installed 2.1.5
binary. See the [privacy policy](https://ultralocked.com/privacy) for disclosures.

Claims such as “no networking code,” “no servers,” “no metadata” and “all
operations happen offline” are not descriptions of this application.

## Threat model and residual risk

| Threat | Relevant control and boundary |
| --- | --- |
| Theft of local ciphertext | Hardware-backed key operations and authenticated encryption; protection depends on key access policy, app correctness and iOS. |
| Theft of an exported bundle | Passphrase-based authenticated encryption; weak passphrases permit offline guessing. |
| Modified export | Bounded parser and authenticated manifest/item decryption; the public package provides reviewable tests. |
| Unlocked or compromised device | Plaintext, screens, input and key use may be exposed; this is outside a complete protection guarantee. |
| Coercion | Duress/decoy tools have operational and forensic limits; no safety or deniability guarantee. |
| Loss of device or keys | Local data may be inaccessible; recovery requires a previously created usable export or another independent copy. |
| Failed or interrupted cleanup | Prepared blocking/retry behavior requires device testing; external copies remain. |
| Subscription-service failure | Prepared client uses StoreKit-verified entitlements; Apple environment checks remain necessary. |

Assumptions include correct platform cryptography, sufficiently strong export
passphrases, an uncompromised environment during plaintext use, and successful
validation of the release's key and authentication policies. This document does
not establish regulatory compliance, suitability for any particular high-risk
operation, or superiority over other products.

## Reporting and revisions

Report suspected vulnerabilities through [SECURITY.md](SECURITY.md). Future
revisions should identify the app version they describe and link any completed
independent audit report before making audit claims. Publishing source or passing
unit tests alone does not constitute independent auditing.
