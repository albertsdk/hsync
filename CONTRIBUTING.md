# Contributing to HSYNC

Thanks for your interest in contributing! HSYNC is a family of projects —
each with its own repository, setup instructions, and contribution workflow.

## Which repository should you contribute to?

| Project | Repository | What it does |
|---------|-----------|--------------|
| **HSYNC Common** | [hsync-common](https://github.com/albertobasso/hsync-common) | Shared library: models, protocol, hashing, compression |
| **HSYNC Server** | [hsync-server](https://github.com/albertobasso/hsync-server) | REST API + WebSocket hub |
| **HSYNC Client** | [hsync-client](https://github.com/albertobasso/hsync-client) | CLI client (Server Mode + LAN/P2P) |
| **HSYNC Desktop** | [hsync-desktop](https://github.com/albertobasso/hsync-desktop) | Desktop GUI (Qt 6) |
| **HSYNC Android** | [hsync-android](https://github.com/albertobasso/hsync-android) | Android client app |

Each repository contains its own `CONTRIBUTING.md` with:

- Development environment setup
- Coding standards and tools
- Test suite instructions
- Commit message conventions (Conventional Commits)
- Pull request guidelines
- Release process

## General guidelines

- **Changes to shared models** (`hsync-common`) should be tested against
  the projects that depend on them (`hsync-server`, `hsync-client`) before
  opening a PR.
- **API changes** must keep the OpenAPI reference (`docs/api.md`) and
  environment variable tables in sync.
- **Cross-project decisions** are documented as Architecture Decision
  Records in the [hsync-common ADR directory](https://github.com/albertobasso/hsync-common/blob/main/docs/internal/adr/README.md).

## Reporting issues

- **Bugs:** open an issue in the relevant repository.
- **Security vulnerabilities:** use GitHub's private vulnerability
  reporting — see [SECURITY.md](SECURITY.md). Do not open public issues
  for security-sensitive matters.

## Code of Conduct

All HSYNC projects follow the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md).
By participating, you are expected to uphold this code.