---
title: ICN Architecture Overview
description: Understanding the layered architecture and components of the InterCooperative Network.
---

# ICN Architecture Overview

The InterCooperative Network (ICN) is built with a modular, layered architecture to ensure flexibility, security, and clear separation of concerns. This document provides a high-level overview of these layers and their primary components.

## Conceptual Layers

ICN can be visualized as a stack of protocols and services:

```
+----------------------------------------------------------------------+
|                            Applications & UI                         |
| (AgoraNet, ICN Wallet, CLIs, Community-specific Apps)                |
+----------------------------------------------------------------------+
|                              Governance                              |
| (RFC Process, Voting Mechanisms, Policy Enforcement)                 |
+----------------------------------------------------------------------+
|                                DAG & Ledger                          |
| (Content-Addressed Storage, Execution Receipts, Mana Ledger)         |
+----------------------------------------------------------------------+
|                            Compute & Runtime                         |
| (WASM Execution Environment, Job Scheduler, Resource Management)     |
+----------------------------------------------------------------------+
|                            Networking (Mesh)                         |
| (libp2p Swarm, Federation Routing, Subnets, Peer Discovery)          |
+----------------------------------------------------------------------+
|                        Identity & Credentials                        |
| (DIDs, Wallets, Verifiable Credentials, Reputation System)           |
+----------------------------------------------------------------------+
```

## Core Components & Modules (Primarily in `icn-core`)

Below are brief explanations of the key crates/modules that constitute the ICN. More detailed documentation for each will be linked here as it becomes available.

1.  **Identity Layer**
    *   **`icn-identity` (Placeholder Name)**: Manages Decentralized Identifiers (DIDs) for all network participants (individuals, cooperatives, communities, federations). Handles key management, cryptographic operations, and integration with the ICN Wallet.
        *   *Detailed Docs*: `../identity/README.md` (Placeholder)
    *   **`icn-credentials` (Placeholder Name)**: Implements Verifiable Credentials, allowing entities to make attestations and prove claims about themselves or others in a cryptographically secure way.
        *   *Detailed Docs*: `../identity/credentials.md` (Placeholder)
    *   **`icn-reputation` (Placeholder Name)**: System for calculating and tracking reputation scores based on verifiable actions (e.g., successful job execution, governance participation). Feeds into resource allocation and job selection.
        *   *Detailed Docs*: `../economy/reputation.md` (Placeholder)

2.  **Networking Layer (Mesh)**
    *   **`icn-libp2p` (Placeholder Name)**: Utilizes libp2p to establish a resilient peer-to-peer network. Manages peer discovery, connection handling, data transport, and pub/sub messaging for inter-node communication.
        *   *Detailed Docs*: `../networking/libp2p.md` (Placeholder)
    *   **`icn-routing` (Placeholder Name)**: Implements routing protocols for message delivery across the mesh, including routing within and between federations and subnets.
        *   *Detailed Docs*: `../networking/routing.md` (Placeholder)

3.  **Compute & Runtime Layer**
    *   **`icn-wasm-runtime` (Placeholder Name)**: The core WebAssembly (WASM) execution environment. Securely runs sandboxed WASM modules, enabling verifiable computation.
        *   *Detailed Docs*: `../compute/wasm-runtime.md` (Placeholder)
    *   **`icn-job-scheduler` (Placeholder Name)**: Manages the lifecycle of compute jobs, from submission to assignment (based on bids, reputation) and execution on available runtime nodes.
        *   *Detailed Docs*: `../compute/scheduler.md` (Placeholder)
    *   **`icn-resource-management` (Placeholder Name)**: Enforces resource quotas (mana) and policies, ensuring fair usage and preventing abuse of compute resources.
        *   *Detailed Docs*: `../economy/resource-management.md` (Placeholder)

4.  **DAG & Ledger Layer**
    *   **`icn-dag-store` (Placeholder Name)**: A content-addressed storage system (likely IPLD-based) forming a Directed Acyclic Graph (DAG). Stores execution receipts, metadata, and other critical network data immutably.
        *   *Detailed Docs*: `../dag/storage.md` (Placeholder)
    *   **`icn-receipts` (Placeholder Name)**: Defines the structure and validation for execution receipts – signed proofs of WASM job completion, anchored into the DAG.
        *   *Detailed Docs*: `../compute/receipts.md` (Placeholder)
    *   **`icn-mana-ledger` (Placeholder Name)**: The distributed ledger that tracks mana balances, regeneration, and transfers between DIDs.
        *   *Detailed Docs*: `../economy/mana.md` (Placeholder)

5.  **Governance Layer**
    *   **`icn-policy-engine` (Placeholder Name)**: A module for interpreting and enforcing governance policies defined through the RFC process (e.g., access control, resource limits for cooperatives).
        *   *Detailed Docs*: `../governance/policy-engine.md` (Placeholder)
    *   *(Note: Governance processes like RFCs and voting are also supported by tools like AgoraNet and community procedures, not just code modules.)*

6.  **Applications & UI Layer**
    *   These are typically separate repositories/applications that interact with the core ICN layers:
        *   **`icn-wallet`**: For user identity, mana, and governance interactions.
        *   **`icn-agoranet`**: For debates and proposals.
        *   **`icn-explorer`**: For viewing DAG and network status.
        *   **`icn-cli` tools** (`meshctl`, `runtimectl`): For command-line interaction.

## Cross-Cutting Concerns

*   **Observability**: Metrics (Prometheus), logging, and tracing are integrated across layers. See `../observability/README.md`.
*   **Security**: Cryptography, secure sandboxing, and robust validation are paramount throughout the stack.

This overview provides a starting point for understanding ICN's architecture. Each component will have more detailed documentation in its respective section or repository as the project evolves. 