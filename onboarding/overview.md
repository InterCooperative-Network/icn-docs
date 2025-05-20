---
title: ICN Onboarding Overview
description: Learn how to get started with the InterCooperative Network.
---

# ICN Onboarding Overview

Welcome to the InterCooperative Network (ICN)! This overview will help you understand what ICN is, its core goals, and how its various components and repositories fit together.

## What is ICN?

The InterCooperative Network (ICN) is a decentralized, federated infrastructure designed for cooperative governance, verifiable computation, and the creation of digital commons. It empowers communities, federations, and cooperatives with tools for digital sovereignty, open economics, and participatory governance.

Key aspects include:
*   **Verifiable Compute**: Ensuring trust through transparent and auditable execution of code (WASM).
*   **Regenerating Economics**: A novel economic model based on *mana*, a per-identity regenerating resource.
*   **Decentralized Identity**: All participants (individuals, coops, communities) are represented by Decentralized Identifiers (DIDs).
*   **Deliberative Governance**: Decisions are made through participatory processes, often facilitated by platforms like AgoraNet.

## Repository Ecosystem

ICN is a modular system, with its components developed across several repositories. Here's a brief look at the key ones (as of May 2025):

*   **`icn-core`**: The heart of ICN, containing Rust crates for the runtime, identity management, DAG, mesh networking, and economic primitives.
*   **`icn-docs`**: (This repository) The canonical source for all documentation, philosophy, onboarding guides, and RFCs.
*   **`icn-website`**: The public-facing website and documentation mirror, built with Astro and TailwindCSS.
*   **`icn-infra`**: Infrastructure-as-Code (Terraform, Helm, Ansible) for deploying ICN in production and testnet environments.
*   **`icn-devnet`**: Tools to quickly spin up a local development network (Docker/K8s based) for testing and development.
*   **`icn-wallet`**: A Progressive Web Application (PWA) for managing identity, mana balances, and participating in governance.
*   **`icn-agoranet`**: The user interface for debates, proposals, and collaborative editing of governance documents.
*   **`icn-explorer`**: A tool for viewing the ICN DAG (Directed Acyclic Graph) and reputation data.

Understanding this structure will help you navigate the project and find the information or code you need.

## Next Steps

Depending on your interests, you might want to explore:

*   Setting up a local development environment: See `setup-devnet.md`
*   Contributing code: See `contribute-code.md`
*   Participating in governance: See `contribute-governance.md`
*   The philosophical underpinnings of ICN: See the `../philosophy/` section. 