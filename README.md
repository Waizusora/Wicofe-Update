# Wicofe-Update

System update client and update orchestration for Wicofe OS.

## Scope

- Update metadata and release channel handling.
- Artifact download and integrity/authenticity verification.
- Staging, activation, and rollback coordination.
- Version compatibility and downgrade policy.
- Update logs, status reporting, and recovery integration.

## Security requirements

Define signed metadata, trust roots, key rotation, replay/downgrade protection, atomic activation, interrupted-update behavior, and rollback constraints before enabling unattended updates.

## Boundaries

WPM manages individual packages. Wicofe-Image creates release images. Wicofe-Recovery provides repair and restore workflows. The OS update design must document how these systems interact.

## Status

Initial repository baseline; update format, signing policy, and activation strategy remain to be specified.

## License

Original project code targets MPL-2.0; dependencies require individual license review.
