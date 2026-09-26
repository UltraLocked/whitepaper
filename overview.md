# UltraLocked: Security Overview

Updated September 26, 2026.

UltraLocked stores vault content locally on an iPhone or iPad. Its local vault
uses Secure Enclave-backed key agreement and signing, with AES-256-GCM file
encryption performed by the app through CryptoKit. The Secure Enclave protects
certain private keys; plaintext and derived symmetric keys still exist in app
memory during use.

## Two kinds of storage

The local vault depends on keys associated with the device. Losing access to
those keys can make the local files unreadable. UltraLocked cannot recover them
with an account password reset.

An explicitly created `.ultralocked` export is different: it is a portable,
passphrase-protected bundle intended for transfer or recovery on another device.
Keep any needed export and its passphrase separately and securely. A weak
passphrase makes an exported bundle vulnerable to offline guessing. Deleting
the local vault does not delete copies already exported or shared.

## What the encryption does

Each local encrypted object uses a separately derived key. This helps separate
objects, but it is **not perfect forward secrecy**: the persistent device key
and the stored public parameters can derive earlier file keys again. AES-GCM
provides confidentiality and integrity when its keys remain protected.

An unlocked or compromised device can expose content through the app, screen,
keyboard, previews or process memory. Hardware-backed keys do not make a
compromised operating system safe.

## Networking and privacy

The vault is not a developer-hosted cloud storage service. That does not mean
the application has no networking. Purchases and restores involve Apple, and
released versions can contact a developer-operated receipt-validation service.
User-directed sharing can send files outside the app. Earlier releases may use
Apple's server-based speech recognition; the prepared update requires on-device
speech recognition and fails when it is unavailable.

Local encryption does not guarantee that every piece of metadata, log, temporary
file or externally shared copy is absent. Refer to the current
[privacy policy](https://ultralocked.com/privacy) for the service's disclosures.

## Emergency controls have limits

Duress and deadline controls are intended to restrict access and initiate local
cleanup. They cannot guarantee safety under coercion or hide all evidence that
a vault or decoy exists. iOS can suspend an app, and it cannot execute while the
device is powered off. A deadline is not a promise of erasure at that exact time.

Deleting files or overwriting them cannot guarantee physical erasure on flash
storage. Uninstalling the app is not a verified key-destruction procedure.
Device loss and failed cleanup can also mean permanent loss of access.

## Release status

As of this revision, **2.1.5 is the App Store version**. A **2.1.6 update is prepared
but not released or submitted**. It includes changes to key preservation,
emergency cleanup, authentication checks, speech handling and subscriptions.
These changes must not be assumed present in an installed 2.1.5 app.

Simulator and package tests support development, but physical-device security
checks and Apple sandbox purchase checks remain release requirements. There is
no completed independent security audit report supplied with these documents.

Read the [technical white paper](whitepaper.md) for the scope, limitations and
remaining validation. The [public code](https://github.com/UltraLocked/security-core)
covers portable export bundles; the full commercial app remains private.
