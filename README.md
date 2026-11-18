# Wicofe-Update

System update client and update orchestration for Wicofe OS.

## Scope

- Update metadata and release channel handling.
- Artifact download and integrity/authenticity verification.
- Staging, activation, and rollback coordination.
- Version compatibility and downgrade policy.
- Update logs, status reporting, and recovery integration.

## Planned user-facing update workflow

The proposed Terminal commands are `wpm sync` and `wpm upgrade`. If adopted, WPM remains the CLI/package-manager entry point and delegates whole-system release orchestration to this repository through a versioned contract. The command mapping and semantics are not finalized or implemented. See the [system delivery and installation direction](https://github.com/Waizusora/Wicofe-Docs/blob/main/SYSTEM_DELIVERY_AND_INSTALLATION_DIRECTION_0.1.md).

## Security requirements

Define signed metadata, trust roots, key rotation, replay/downgrade protection, atomic activation, interrupted-update behavior, and rollback constraints before enabling unattended updates.

## Boundaries

WPM manages individual packages. Wicofe-Image creates release images. Wicofe-Recovery provides repair and restore workflows. The OS update design must document how these systems interact.

## Status

Initial repository baseline; update format, signing policy, and activation strategy remain to be specified.

## License

Original project code targets MPL-2.0; dependencies require individual license review.

## Documentation

See [docs/INDEX.md](docs/INDEX.md) for this repository's technical documentation. The [central repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md) identifies canonical ownership and cross-repository boundaries.

## Wicofe-wide principles

Wicofe is an independent Unix-like, POSIX-compatible operating system and software ecosystem, designed as an alternative to established and widely adopted software ecosystems and technologies, including GNU. Native components must use Wicofe interfaces and must not require Linux libc or GNU compatibility layers. Prefer permissive licenses; avoid GPL/AGPL, and consider LGPL only when no suitable permissive alternative exists. Reuse or fork upstream software when implementing it from scratch would be disproportionately difficult. See the [canonical project principles](https://github.com/Waizusora/Wicofe-Docs/blob/main/PROJECT_PRINCIPLES.md).
