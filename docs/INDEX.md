# Update Documentation Index

This directory documents installed-system update orchestration for Wicofe.

## Architecture
- [Whole-system update architecture 0.1](WHOLE_SYSTEM_UPDATE_ARCHITECTURE_0.1.md) — draft design for release trust, compatibility, staging, activation, health confirmation, rollback and WPM coordination.

## Whole-system product direction
- [Wicofe system delivery and installation direction 0.1](https://github.com/Waizusora/Wicofe-Doc/blob/main/SYSTEM_DELIVERY_AND_INSTALLATION_DIRECTION_0.1.md) — planned flexible Delivery Release cadence and urgent out-of-band security updates; WPM CLI integration remains proposed.

## Scope
- Update metadata and release channels.
- Artifact acquisition and integrity/authenticity verification.
- Staging, activation, rollback and downgrade policy.
- Version compatibility, update logs and recovery integration.

## Ownership
[WPM](https://github.com/Waizusora/WPM) owns individual package operations. [Wicofe-Image](https://github.com/Waizusora/Wicofe-Image) creates release images. [Wicofe-Recovery](https://github.com/Waizusora/Wicofe-Recovery) provides restore and repair workflows. This repository owns whole-system update orchestration.

## Security
Before unattended updates, specify signed metadata, trust roots, key rotation, replay protection, downgrade prevention, activation guarantees, interrupted-update behavior and recovery. Do not claim atomic updates or rollback until the selected storage/boot model is implemented and power-failure tested.

- [Central documentation index](https://github.com/Waizusora/Wicofe-Doc/blob/main/INDEX.md)
- [Repository documentation map](https://github.com/Waizusora/Wicofe-Doc/blob/main/REPOSITORY_DOCUMENTATION_MAP.md)
