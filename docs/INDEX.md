# Update Documentation Index

This directory documents installed-system update orchestration for Wicofe.

## Scope
- Update metadata and release channels.
- Artifact acquisition and integrity/authenticity verification.
- Staging, activation, rollback, and downgrade policy.
- Version compatibility, update logs, and recovery integration.

## Ownership
[WPM](https://github.com/Waizusora/WPM) owns individual package operations. [Wicofe-Image](https://github.com/Waizusora/Wicofe-Image) creates release images. [Wicofe-Recovery](https://github.com/Waizusora/Wicofe-Recovery) provides restore and repair workflows. This repository owns whole-system update orchestration.

## Security
Before unattended updates, specify signed metadata, trust roots, key rotation, replay protection, downgrade prevention, atomic activation, and interrupted-update behavior.

- [Central documentation index](https://github.com/Waizusora/Wicofe-Docs/blob/main/INDEX.md)
- [Repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md)
