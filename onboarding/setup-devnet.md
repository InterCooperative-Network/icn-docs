---
title: Setting Up ICN Devnet
description: Learn how to spin up a local development network for ICN.
---

# Setting Up the ICN Development Network (Devnet)

This guide provides instructions for setting up a local ICN development network. The devnet allows you to run a multi-node ICN environment on your own machine for development, testing, and exploration.

We primarily use the `icn-devnet` repository, which provides tools based on Docker and potentially Kubernetes (K8s) for ease of use.

## Prerequisites

Before you begin, ensure you have the following installed:

*   **Git**: For cloning the repositories.
*   **Docker**: And Docker Compose (usually included with Docker Desktop).
    *   Ensure the Docker daemon is running.
*   **(Optional) Kubectl & a local K8s cluster** (e.g., Minikube, Kind, Docker Desktop Kubernetes): If you plan to use Kubernetes-based scripts from `icn-devnet`.
*   **(Optional) Rust toolchain**: If you intend to build `icn-core` components from source for the devnet.

## Option 1: Using Docker (Recommended)

The `icn-devnet` repository typically contains Docker Compose files to quickly spin up a complete environment.

1.  **Clone `icn-devnet`**:
    ```bash
    git clone https://github.com/InterCooperative-Network/icn-devnet.git # Replace with actual URL if different
    cd icn-devnet
    ```

2.  **Explore Available Configurations**:
    Check the `README.md` within `icn-devnet` for different devnet configurations (e.g., small, medium, with specific services enabled).

3.  **Run Docker Compose**:
    Navigate to the chosen configuration directory (if applicable) and run:
    ```bash
    docker-compose up -d
    ```
    This will pull the necessary Docker images (or build them if specified) and start all the ICN services in the background (`-d`).

4.  **Verify Setup**:
    Check the logs for all running containers:
    ```bash
    docker-compose logs -f
    ```
    You should see nodes connecting, services starting, and potentially some initial network activity. Specific verification steps will be detailed in the `icn-devnet` repository's documentation.

5.  **Accessing Services**:
    The `icn-devnet` documentation will specify the ports and endpoints for accessing various services (e.g., node APIs, AgoraNet UI, Explorer).

6.  **Stopping the Devnet**:
    To stop and remove the containers:
    ```bash
    docker-compose down
    ```
    To stop without removing containers (so you can restart them faster later):
    ```bash
    docker-compose stop
    ```

## Option 2: Manual Setup (Advanced)

A fully manual setup involves building and running each component from `icn-core` and other repositories individually. This is a more complex process generally reserved for deep core development or specific debugging scenarios.

*This section is a placeholder. Detailed instructions for manual setup will be provided as the project matures and if there is a clear need beyond the Docker-based devnet. It would involve steps like:* 

*   Cloning `icn-core`, `icn-wallet`, `icn-agoranet`, etc.
*   Building each component (e.g., `cargo build --release` for Rust crates).
*   Configuring networking between nodes (libp2p bootstrap nodes, peer IDs).
*   Setting up genesis states for the economy and governance modules.
*   Running each service binary with appropriate command-line flags.

For most development and testing purposes, the Docker-based setup from `icn-devnet` is strongly recommended due to its simplicity and reproducibility.

## Troubleshooting

*   **Port Conflicts**: Ensure the ports required by ICN services are not already in use on your machine.
*   **Docker Resources**: Allocate sufficient memory and CPU resources to Docker, especially if running a larger devnet configuration.
*   Refer to the `icn-devnet` repository's issue tracker and documentation for specific troubleshooting tips.

Once your devnet is running, you can proceed to interact with it using the ICN Wallet, CLI tools, or by deploying test applications. 