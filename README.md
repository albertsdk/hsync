<p align="center">
  <img src="docs/assets/hsync-logo-horizontal.svg" alt="HSYNC logo" width="480">
</p>

# HSYNC

**HSYNC** (HTTP Synchronization) provides **real-time**, **enterprise-grade** file synchronization across distributed environments using exclusively the standard **HTTP/HTTPS protocol**.

This architectural design makes HSYNC the optimal solution for environments subject to strict network policies, such as corporate infrastructures, cloud deployments, and zero-trust networks where custom protocols, SSH, or VPNs are restricted. Data is stored natively on the filesystem in its original format—ensuring immediate accessibility and avoiding vendor lock-in through proprietary containers or opaque block stores.

Synchronization is supported both via a centralized hub (Client-Server) and decentralized topologies (LAN/P2P).

> 👉 **Operational Advisory:** As with any automated filesystem mutation utility, administrators are strongly advised to maintain validated, independent backups prior to initial deployment in production environments.

[![License: MPL 2.0](https://img.shields.io/badge/License-MPL_2.0-brightgreen.svg)](LICENSE)
[![Python 3.12+](https://img.shields.io/badge/Python-3.12+-blue.svg)](https://www.python.org/)
[![Docker](https://img.shields.io/badge/Docker-🐳-2496ED.svg)](https://www.docker.com/)
[![Kotlin 2.4](https://img.shields.io/badge/Kotlin-2.4-7F52FF.svg)](https://kotlinlang.org/)
[![Android SDK 33+](https://img.shields.io/badge/Android-33+-3DDC84.svg)](https://developer.android.com/)
[![Qt 6](https://img.shields.io/badge/Qt-6-41CD52.svg)](https://www.qt.io/)
[![Platform](https://img.shields.io/badge/Platform-Linux_|_macOS_|_WD_My_Cloud-lightgrey.svg)](https://github.com/albertsdk/hsync-client/blob/main/docs/installation.md)

---

**Quick navigation:**

* **[Architectural Applicability](#architectural-applicability)**
* **[Technical Capabilities](#technical-capabilities--core-differentiators)**
* **[Market Comparison](#market-comparison)**
* **[Architecture Overview](#architecture-overview)**
* **[Project Ecosystem](#project-ecosystem)**
* **[Deployment & Documentation](#deployment--documentation)**
* **[Security & Vulnerability Management](#security--vulnerability-management)**

## Architectural Applicability

HSYNC is engineered to operate seamlessly within:

* **Corporate Networks:** Operates natively through standard ports (80/443), passing through existing firewalls, proxies, and deep packet inspection systems.
* **Cloud Infrastructures:** Fully compatible with standard IT infrastructure, including load balancers, Web Application Firewalls (WAFs), and reverse proxies.
* **Restricted Environments:** Functions autonomously in secure environments where standard administrative protocols (like SSH) or VPN access are prohibited.
* **Real-Time Workflows:** Replaces legacy polling mechanisms with event-driven, WebSocket-based push notifications for immediate data consistency.

---

## Technical Capabilities & Core Differentiators

**⚙️ Core Performance Differentiators**

* **Zero-Delay Sync (Fast after save):** Data transfer triggers in a fraction of a second upon file closure, eliminating traditional quiet-window latencies and ensuring continuous pipeline synchronization.
* **Block-Level Delta Transfer:** For large files, the system transmits exclusively the modified data blocks (rsync-style). This drastically reduces bandwidth consumption and transfer times.
* **Direct LAN/P2P Mode:** Enables direct device-to-device synchronization without centralized server dependency, minimizing infrastructure load and maximizing local network throughput.

**🛰️ Network & Architecture**

* **Universal HTTP/HTTPS Compatibility:** Guaranteed operation across any enterprise network setup without requiring custom routing or specific port forwarding.
* **Deterministic Deletion Handling (Tombstones):** Employs strict tombstone tracking to prevent the accidental resurrection of deleted files across distributed nodes.
* **Native Symlink Support:** Accurately preserves symbolic links across platforms, a critical requirement for development environments and system administration.
* **Real-Time Architecture:** Event-driven architecture powered by WebSockets, eliminating the high overhead and inefficiency of traditional HTTP polling.
* **Private P2P Networks:** LAN/P2P clusters can be isolated from one another with a group filter and authenticated with a shared group key, so independent teams can share one physical network without ever mixing data.
* **Self-Hosted, No Vendor Lock-In:** every topology runs on infrastructure you own — the hub is your own server and buckets are plain directories on your filesystem, with no third-party service in the data path.

**🛡️ Resilience & Data Integrity**

* **Fault-Tolerant Transfers (Resumable uploads):** Automatically resumes interrupted transfers from the exact point of failure, ensuring reliable delivery of massive payloads over unstable connections.
* **Zstandard (Zstd) Compression:** Utilizes modern compression algorithms to optimize throughput, particularly effective on low-bandwidth links.
* **SHA-256 Integrity Verification:** Enforces end-to-end cryptographic hashing for every file, guaranteeing absolute data integrity against transmission corruption.

**🔐 Security, Governance & Deployment**

* **Isolated Data Architecture (Bucket-based):** Organizes logical groups of files into isolated sync buckets, facilitating multi-tenant architectures and strict access control.
* **Standardized Authentication:** Secures endpoints via API Key (Bearer token) authentication, easily integrable with enterprise secret management systems.
* **Built-in Observability (Prometheus):** Exposes native metrics for direct integration into corporate monitoring and alerting stacks.
* **Containerized Deployment (Docker):** Ensures rapid, reproducible deployments via single `compose.yaml` files, ideal for cloud-native and on-premise scaling.
* **Infrastructure Protection (Rate Limiting):** Incorporates defense mechanisms against traffic spikes and abuse, ensuring high availability and service uptime.

---

## Market Comparison

The following table benchmarks HSYNC against industry standards across critical architectural dimensions.

| Tool | Protocol | HTTP-Native | Real-Time Sync | Native Storage | Architecture |
|------|----------|:---:|:---:|:---:|------|
| **HSYNC** | HTTP / HTTPS | ✅ | ✅ | ✅ | Hub-Spoke & P2P |
| rsync | SSH / Daemon | ❌ | ❌ | ✅ | Client-Server |
| Syncthing | BEP (Custom) | ❌ | ✅ | ✅ | P2P |
| Nextcloud | WebDAV (HTTP) | ✅ | ⚠️ ¹ | ✅ | Client-Server |
| Seafile | HTTP / HTTPS | ✅ | ⚠️ ¹ | ❌ ² | Client-Server |
| scp / sftp | SSH | ❌ | ❌ | ✅ | Client-Server |
| Unison | SSH / Socket | ❌ | ❌ | ✅ | P2P |
| Rclone | S3, SFTP, etc. | ⚠️ ⁴ | ❌ | ⚠️ ³ | Client-Server |

> **¹** Relies on local filesystem monitoring via a desktop client; lacks native event-driven push notifications (WebSocket) at the protocol level.<br>
> **²** Utilizes a proprietary block-based internal storage format, rendering files inaccessible from the filesystem without the server software.<br>
> **³** When utilizing the `crypt` backend, files are stored in an opaque encrypted format.<br>
> **⁴** Rclone's HTTP backend is restricted to read-only operations. Core synchronization requires non-HTTP protocols.

---

## Architecture Overview

HSYNC operates seamlessly in two primary network topologies:

### Server Mode (Hub-and-Spoke)

```mermaid
flowchart LR
    subgraph Clients["Endpoints"]
        CA["🖥️ Client A<br/><small>Workstation</small>"]
        CB["💻 Client B<br/><small>Remote Device</small>"]
    end

    Server["🗄️ HSYNC Server<br/><small>REST API & WebSocket Hub</small>"]

    CA <-->|"HTTP/HTTPS"| Server
    CB <-->|"HTTP/HTTPS"| Server

```

A central **HSYNC Server** hosts shared environments (buckets). Connected endpoints push changes via standard REST API calls and maintain a persistent WebSocket connection for event listening. Modifications on any endpoint trigger sub-second broadcast notifications, prompting immediate alignment across all nodes.

### LAN / P2P Mode (Decentralized)

```mermaid
flowchart LR
    CA["🖥️ Client A<br/><small>Workstation</small>"]
    CB["💻 Client B<br/><small>Remote Device</small>"]

    CA <-->|"HTTPS<br/>Direct Peer-to-Peer"| CB

```

Designed for serverless environments. Endpoints on the same local network autonomously discover one another and perform direct synchronization over HTTP. Ideal for high-speed, localized data transfer without central infrastructure dependencies.

---

## Project Ecosystem

HSYNC is developed as a modular ecosystem of specialized repositories:

```mermaid
flowchart TD
    Main["📦 HSYNC Main<br/><small>Umbrella Repository</small>"]

    Common["📚 HSYNC Common<br/><small>Core Library: Models, Protocol, Cryptography</small>"]
    Server["🗄️ HSYNC Server<br/><small>Hub: REST API & WebSocket</small>"]
    Client["💻 HSYNC Client<br/><small>CLI: Node Controller</small>"]
    Desktop["🖥️ HSYNC Desktop<br/><small>GUI Interface</small>"]
    Android["📱 HSYNC Android<br/><small>Mobile Application</small>"]
    Bootnode["📡 HSYNC Bootnode<br/><small>P2P Internet Discovery</small>"]

    Main -.-> Common
    Main -.-> Server
    Main -.-> Client
    Main -.-> Desktop
    Main -.-> Android
    Main -.-> Bootnode

    Server --> Common
    Client --> Common
    Desktop --> Client

```

| Component | Function | Lifecycle Status |
| --- | --- | --- |
| **[HSYNC Client](https://github.com/albertsdk/hsync-client)** | CLI client: Server Mode and LAN/P2P Mode | ✅ Stable |
| **[HSYNC Server](https://github.com/albertsdk/hsync-server)** | Central sync hub: REST API, WebSocket relay, file storage | ✅ Stable |
| **[HSYNC Common](https://github.com/albertsdk/hsync-common)** | Shared library: data models, protocol, delta transfer, hashing | ✅ Stable |
| HSYNC Desktop | Desktop GUI front-end | 🚧 In development |
| HSYNC Android | Android client app | 🚧 In development |
| **[HSYNC Bootnode](https://github.com/albertsdk/hsync-bootnode)** | Optional Internet discovery node for P2P topologies: `boot/1` over QUIC, no-log, RAM-only | 🚧 In development |

*Note: All Stable components are fully tested, documented, and suitable for enterprise deployment.*

---

## Deployment & Documentation

Comprehensive deployment guidelines and technical documentation are maintained within their respective component repositories:

* **[HSYNC Client — Implementation Guide](https://github.com/albertsdk/hsync-client/blob/main/docs/getting-started.md)**: Configuration for endpoint nodes connecting to a central server or engaging in P2P topologies.
* **[HSYNC Server — Initialization](https://github.com/albertsdk/hsync-server/blob/main/docs/getting-started.md)**: Guidelines for local deployment, secret generation, and certificate management.
* **[HSYNC Server — Production Deployment](https://github.com/albertsdk/hsync-server/blob/main/docs/deployment.md)**: Best practices for exposing the infrastructure to public networks securely.

---

## Security & Vulnerability Management

Security is a primary design pillar of HSYNC.
To report potential security vulnerabilities, please avoid public issue trackers and utilize the private reporting channel:

**[Report a Security Vulnerability](https://github.com/albertsdk/hsync/security/advisories/new)**

Review our [Security Policy](SECURITY.md) for detailed information on supported versions, patch management, and infrastructure hardening guidelines.

---

## Open Source Contributions

Engineering contributions are evaluated and integrated through a standard Pull Request workflow. Review the specific `CONTRIBUTING.md` in each repository for architecture standards and environment setup:

* [HSYNC Client Contribution Guidelines](https://github.com/albertsdk/hsync-client/blob/main/CONTRIBUTING.md)
* [HSYNC Server Contribution Guidelines](https://github.com/albertsdk/hsync-server/blob/main/CONTRIBUTING.md)
* [HSYNC Common Contribution Guidelines](https://github.com/albertsdk/hsync-common/blob/main/CONTRIBUTING.md)
* [HSYNC Main Contribution Guidelines](CONTRIBUTING.md)

## Licensing

The HSYNC ecosystem is released under the **[Mozilla Public License 2.0 (MPL 2.0)](LICENSE)**.

---

## Disclaimer

> [!WARNING]
> **HSYNC manipulates files and directories** — it creates, overwrites, moves, and deletes data.
> Misconfiguration, software defects, or other unforeseen conditions may lead to **data loss or
> corruption**. **Use at your own risk and always keep verified, independent backups.**

**Legal Disclaimer:** THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE, AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES, OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT, OR OTHERWISE, ARISING FROM, OUT OF, OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

This disclaimer supplements, and does not replace, Sections 6 ("Disclaimer of Warranty") and 7 ("Limitation of Liability") of the [Mozilla Public License 2.0](LICENSE), which govern this software.
