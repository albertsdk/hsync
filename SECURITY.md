# Security Policy

## Supported Versions

Security fixes are applied to the latest version of each project in the
HSYNC family. See the individual project's `SECURITY.md` for their
version-specific support table.

## Reporting a Vulnerability

**Please do not report security vulnerabilities through public GitHub issues.**

Instead, use GitHub's private vulnerability reporting:

1. Open the **Security** tab of the relevant repository.
2. Click **Report a vulnerability** to start a private advisory.

Alternatively, email `security [at] albertobasso [dot] dev`.

Please include the affected project and version, steps to reproduce, the
impact, and any suggested mitigation. Allow a reasonable time for a fix
before any public disclosure. We aim to acknowledge reports within a few
days and to keep you informed of remediation progress.

## Security Model

HSYNC operates over standard HTTP/HTTPS and involves both a central server
and multiple clients. Key security controls across the ecosystem:

- **Authentication** — all data endpoints require a Bearer API key,
  compared in constant time. WebSocket clients authenticate with an
  `auth` frame before any event is processed.
- **Transport security** — TLS (HTTPS/WSS) is available on all components.
  LAN/P2P mode uses persistent self-signed identities with connection-level
  fingerprint pinning (TOFU).
- **Rate limiting** — per-IP limits on uploads, downloads, WebSocket
  connections, and general endpoints, plus per-connection WebSocket message
  throttling.
- **Path-traversal protection** — bucket and filename validation reject
  unsafe paths.
- **Integrity** — SHA-256 verification on every file transfer.

For component-specific hardening recommendations, see the individual
project's `SECURITY.md`.