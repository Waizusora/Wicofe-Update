# Wicofe Whole-System Update Architecture 0.1

**Status:** Draft proposal. This document defines whole-system update orchestration and security requirements. It does not freeze an update bundle format, signing protocol, partition strategy, or activation implementation.

## 1. Purpose

Wicofe-Update manages updates to an installed Wicofe system: release discovery, metadata verification, compatibility checks, artifact acquisition, staging, activation, health validation and rollback coordination.

It is distinct from WPM, which manages individual packages. A system update may coordinate kernel, boot components, system services, libraries and package state as one compatibility unit.

## 2. Ownership boundaries

| Repository | Canonical responsibility |
|---|---|
| Wicofe-Update | Whole-system release metadata, update policy, staging, activation, rollback and update reporting |
| WPM | Individual package format, repository metadata, dependency resolution and package transactions |
| Wicofe-Image | Release image composition and immutable image artifacts |
| Wicofe-Build | Build graph, source/toolchain provenance and build artifact manifests |
| Wicofe-ThirdParty | Third-party provenance, license and compliance records |
| Wicofe-Recovery | Recovery environment, repair and restore operations |
| Wicofe-Kernel | Boot/runtime support for the selected activation strategy |
| Wicofe-Services / Osynit | Service readiness, shutdown and restart coordination |
| Wicofe-Tests | Update, rollback, compatibility and power-failure test suites |

Wicofe-Update must not redefine package metadata, build release images, or replace the recovery environment. WPM package updates and OS release updates need an explicit coordination contract to prevent conflicting writes.

## 3. Update models

Candidate models require separate evaluation:
- **A/B system slots:** stage a complete new system in an inactive slot, validate it, then switch boot selection. This can provide strong rollback properties but requires boot/layout and storage support.
- **Snapshot-based update:** stage changes in a filesystem/storage snapshot and atomically switch the active root. Requires snapshot and recovery semantics.
- **In-place transactional update:** update files in place with a durable transaction and recovery log. More difficult to make atomic, especially for kernel, boot and shared state.

No model is selected by this draft. Do not claim atomic updates or reliable rollback until the storage, bootloader, filesystem and recovery contracts are defined and tested.

## 4. Release metadata and trust

A release manifest should identify:
- schema version and release/channel identity;
- monotonic release sequence and version;
- supported source versions and upgrade paths;
- target architectures and hardware/ABI requirements;
- exact component and image artifact hashes/sizes;
- required bootloader, kernel, filesystem and package compatibility;
- update strategy and target slot/snapshot requirements;
- minimum updater/recovery versions;
- third-party inventory/SBOM and license notice references;
- creation time, expiry policy and signing key identity.

Authenticate metadata before trusting URLs, version constraints or artifact hashes. Verify each downloaded artifact independently. Use a defined signature envelope and trust-root policy; do not invent an ad hoc cryptographic format.

## 5. Anti-rollback and key management

Define monotonic release sequence handling and a protected record of the highest accepted security version where platform capabilities permit. Distinguish ordinary downgrade policy from security rollback prevention and recovery authorization.

The trust design must specify:
- root and delegated signing roles;
- threshold/quorum requirements, if any;
- key rotation, expiration, revocation and compromise recovery;
- metadata versioning and expiry;
- replay protection and stale mirror behavior;
- offline verification and recovery trust;
- hardware-backed storage availability and fallback risks.

A version string alone is not a secure anti-rollback mechanism. Do not claim protection against rollback by a privileged attacker unless the threat model and protected state support it.

## 6. Compatibility and preflight

Before staging, verify:
- current system identity, version and update state;
- source release is on a supported upgrade path;
- target architecture and CPU baseline;
- ABI and kernel/userspace compatibility;
- bootloader and firmware requirements;
- filesystem and storage layout features;
- available space, slot/snapshot capacity and power conditions;
- required services and package database consistency;
- no conflicting package transaction or pending destructive storage operation.

Unknown compatibility must fail closed for unattended updates. Provide a clear diagnostic rather than silently forcing installation.

## 7. Staging and activation

The update flow should separate preparation from activation:

1. Discover and authenticate release metadata.
2. Resolve a compatible update plan.
3. Download artifacts into a bounded staging area.
4. Verify hashes, signatures and provenance.
5. Validate dependencies and required free space.
6. Stage new system state without damaging the currently bootable system where the chosen model permits.
7. Run offline consistency checks and required migration preparation.
8. Record activation intent durably.
9. Switch active slot/snapshot or execute the explicitly documented in-place commit protocol.
10. Reboot or restart only under the approved activation policy.
11. Validate health and mark the new release successful.
12. Retain rollback state until the defined confirmation/retention condition is met.

The exact atomicity guarantee depends on the selected storage and boot model. The update manager must expose the real guarantee and not describe a multi-step in-place operation as atomic.

## 8. Health confirmation and rollback

Define a bounded post-activation health window. Health criteria should include boot completion, critical service readiness, root filesystem availability and essential system checks. Avoid making optional services a prerequisite for successful boot.

For slot-based designs, boot-attempt counters and fallback selection must be owned by a defined boot control component. For snapshot designs, specify snapshot promotion and rollback. For in-place updates, recovery requires a durable journal and explicit per-operation recovery semantics.

Rollback must account for persistent user data and schema migrations. Restoring old system binaries while retaining incompatible new data may make the system unusable. Every migration needs compatibility, backup and rollback policy.

Never delete the last known-good system state before the new release is confirmed and rollback retention expires.

## 9. WPM coordination

Define mutual exclusion between whole-system updates and package transactions. During OS update staging/activation, WPM must either be blocked or operate against a separately staged package database under a shared transaction protocol.

Specify:
- package database snapshot/restore behavior;
- package versions pinned by the OS release;
- whether post-release package updates are permitted;
- handling of locally installed packages and user modifications;
- conflict resolution and orphan package policy;
- recovery when a package transaction is interrupted.

WPM must not independently replace kernel/boot/system components managed by the whole-system release unless the release policy explicitly delegates those artifacts.

## 10. Power-loss, interruption and failure handling

Test and document interruption at every durable transition: metadata acceptance, download, verification, staging, activation intent, boot selection change, first boot, health confirmation and rollback.

Requirements:
- preserve the current bootable system until the selected model's safe activation point;
- flush critical metadata before changing boot selection;
- detect truncated writes and storage errors;
- make recovery idempotent;
- distinguish failed, incomplete and indeterminate update states;
- never mark success before health confirmation;
- avoid automatic retries when target identity or storage state is uncertain;
- coordinate recovery handoff when normal boot is impossible.

A/B and snapshot models can reduce risk but do not automatically protect user data, boot firmware variables, or shared state.

## 11. Security and privacy

- Verify signed metadata before following artifact references.
- Verify artifact hashes and authenticity before activation.
- Isolate download and staging paths.
- Bound artifact size, decompression ratio and total disk use.
- Reject path traversal, unsafe links and unexpected file types.
- Protect signing trust roots and anti-rollback state.
- Keep credentials and private keys out of logs.
- Require authorization for manual downgrade, recovery override and channel changes.
- Do not silently change update channels or reboot policy.

## 12. Observability and user control

Expose current version, available release, verified update plan, download/staging progress, activation state, last result and rollback reason. Separate security-critical warnings from informational notices.

Define user/admin controls for channel selection, metered/offline behavior, scheduling, reboot deferral and maintenance windows. A deferred reboot must not leave the user believing the new release is active.

## 13. Testing strategy

- Manifest schema, signature and trust-root validation.
- Expired, replayed, revoked and rollback metadata tests.
- Wrong architecture, unsupported upgrade path and ABI mismatch tests.
- Corrupt/truncated artifact and decompression-bomb tests.
- Insufficient space and concurrent WPM transaction tests.
- Power-loss injection at every state transition.
- Idempotent resume and recovery tests.
- Boot-attempt exhaustion and fallback tests for slot model.
- Snapshot promotion/rollback tests for snapshot model.
- In-place journal recovery tests if that model is selected.
- Persistent-data and schema migration compatibility tests.
- Key rotation, revocation and offline recovery tests.
- End-to-end update/rollback tests on x86-64 and AArch64 when supported.

## 14. Open decisions

- Initial update model: A/B, snapshots or in-place transaction.
- Boot-control ownership and boot-attempt protocol.
- Release manifest and signature envelope.
- Trust roots, delegated keys, threshold and revocation model.
- Protected anti-rollback storage and threat model.
- Supported upgrade paths and release channels.
- Persistent user-data migration strategy.
- WPM lock and package database coordination.
- Update scheduling, reboot and offline policies.
- Recovery handoff and rollback retention.
- Update telemetry and privacy policy.

## 15. Completion criteria

A production updater requires a versioned authenticated release format, explicit compatibility rules, a selected and implemented activation model, durable interruption recovery, tested rollback/data migration, WPM coordination, key lifecycle procedures and end-to-end power-failure tests. Until these exist, this remains a draft architecture and no atomicity guarantee is implied.
