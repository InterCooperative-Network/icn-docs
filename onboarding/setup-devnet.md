---
title: Setting Up ICN Devnet
description: Learn how to spin up a local development network for ICN.
---

# Setting Up the ICN Development Network (Devnet)

This guide provides step-by-step instructions for setting up a local InterCooperative Network (ICN) development network. The devnet allows you to run a multi-node ICN environment on your own machine for development, testing, and exploration of ICN features.

We primarily use the `icn-devnet` repository, which provides tools based on Docker and Docker Compose for ease of use and a consistent setup.

## Prerequisites

Before you begin, ensure you have the following installed on your system:

*   **Git**: For cloning the necessary repositories. ([Installation Guide](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git))
*   **Docker**: The Docker engine. ([Installation Guide](https://docs.docker.com/engine/install/))
*   **Docker Compose**: Usually included with Docker Desktop, but can be installed separately. ([Installation Guide](https://docs.docker.com/compose/install/))

Ensure the Docker daemon is running before proceeding.

*   **(Optional) Kubectl & a local Kubernetes cluster**: (e.g., Minikube, Kind, K3s, or Docker Desktop Kubernetes). This is only if you plan to use or experiment with Kubernetes-based scripts from `icn-devnet`, if available.
*   **(Optional) Rust toolchain**: (e.g., via `rustup`). Required if you intend to build `icn-core` components from source and integrate them with the devnet, rather than using pre-built Docker images.

## Option 1: Using Docker Compose (Recommended)

The `icn-devnet` repository is the standard way to spin up a complete local environment. It typically contains `docker-compose.yml` files that define and configure the necessary ICN services (runtime nodes, networking, etc.).

1.  **Clone the `icn-devnet` Repository**:
    Open your terminal and run:
    ```bash
    git clone https://github.com/InterCooperative-Network/icn-devnet.git
    cd icn-devnet
    ```
    *(Note: If the official URL differs, please use the correct one. This is a common convention.)*

2.  **Explore Available Configurations (Important!)**:
    The `icn-devnet` repository might offer multiple configurations (e.g., a minimal setup, a multi-node setup, a setup with specific experimental features enabled). 
    *   **Check the `README.md` in `icn-devnet`**: This is crucial. It will detail available configurations, what each includes, and any specific environment variables or `.env` files you might need to set up.
    *   Look for subdirectories like `configs/` or different `docker-compose-*.yml` files.
    *   For this guide, we'll assume a default or common configuration. Adjust commands if your chosen configuration requires a specific file (e.g., `docker-compose -f docker-compose-full.yml up`).

3.  **Prepare Environment (if required)**:
    Some configurations might require a `.env` file for specific settings (e.g., node names, ports if you need to change defaults, feature flags).
    ```bash
    # Example: if an .env.example is provided
    # cp .env.example .env
    # nano .env # Then edit as needed
    ```

4.  **Pull Docker Images & Start the Devnet**:
    Navigate to the root of the `icn-devnet` directory (or the specific configuration directory if applicable) and run:
    ```bash
    docker-compose pull # Pulls the latest versions of images defined in the compose file
    docker-compose up -d # Starts all services in detached mode (-d)
    ```
    This command will:
    *   Download (pull) any pre-built Docker images for ICN components from a Docker registry.
    *   If images are built locally, it might trigger builds the first time or if sources change.
    *   Create and start all the containers, networks, and volumes defined in the `docker-compose.yml`.

5.  **Verify the Setup**:
    *   **Check Container Status**: See if all containers are running:
        ```bash
        docker-compose ps
        ```
        You should see services like ICN nodes, possibly a local blockchain/ledger, networking components, and any supporting services (e.g., log collectors, metrics). Their `State` should be `Up`.
    *   **View Logs**: Monitor the aggregated logs from all services:
        ```bash
        docker-compose logs -f --tail=100 # Shows live logs from all containers, last 100 lines
        ```
        To view logs for a specific service (e.g., `icn_node_1` if named so in `docker-compose.yml`):
        ```bash
        docker-compose logs -f icn_node_1
        ```
        Look for messages indicating successful startup, peer discovery, and network formation. No critical error messages should be spamming the logs.

6.  **Accessing Services & Interacting with the Devnet**:
    The `README.md` in `icn-devnet` or specific service documentation will provide details on how to interact with the running devnet:
    *   **Node Endpoints**: e.g., RPC/API endpoints for runtime nodes (e.g., `localhost:8080`, `localhost:8081`).
    *   **CLI Tools**: The `icn-cli` tools (`meshctl`, `runtimectl`) might be pre-configured or require pointing to these local endpoints.
    *   **ICN Wallet**: The `icn-wallet` PWA might need to be configured to connect to your local devnet nodes.
    *   **Explorer UI**: If an explorer service is part of the devnet, its local URL (e.g., `http://localhost:3000`).

7.  **Stopping the Devnet**:
    *   To stop all running services and remove the containers, networks, and volumes created by `docker-compose up` (useful for a clean restart):
        ```bash
        docker-compose down
        ```
    *   To stop the services without removing them (so you can restart them faster later with `docker-compose start`):
        ```bash
        docker-compose stop
        ```

## Option 2: Manual Setup (Advanced)

A fully manual setup involves building and running each ICN component from `icn-core` and other repositories individually. This is a significantly more complex process generally reserved for deep core development, specific debugging scenarios, or when Docker is not an option.

*This section remains a placeholder. Detailed instructions for manual setup will be provided if there is a clear community need beyond the Docker-based devnet. It would involve steps like cloning all relevant repos, building binaries (e.g., `cargo build --release`), manually configuring network peering, genesis states, and running each service with precise command-line flags.*

For most development and testing purposes, the Docker Compose setup from `icn-devnet` is **strongly recommended** due to its simplicity, reproducibility, and encapsulation of complex configurations.

## Troubleshooting Common Issues

*   **Port Conflicts**: If `docker-compose up` fails with errors about ports already in use, another service on your machine is using a port ICN needs. You may need to stop the conflicting service or reconfigure the ICN devnet (if possible via `.env` files or `docker-compose.yml` overrides) to use different ports.
*   **Docker Resources**: Ensure Docker has sufficient resources (CPU, Memory, Disk Space). Large devnets can be resource-intensive. Adjust Docker Desktop's resource allocation settings if needed.
*   **Disk Space**: Docker images and volumes can consume significant disk space over time. Regularly prune unused images, containers, and volumes (e.g., `docker system prune -a`).
*   **Firewall Issues**: Ensure your firewall isn't blocking Docker's networking or communication between containers.
*   **Outdated Images**: Run `docker-compose pull` periodically to ensure you have the latest images, especially after new releases.
*   **Check `icn-devnet` Issues**: The `icn-devnet` repository's GitHub Issues page is the best place to look for existing solutions or report new problems specific to the devnet setup.

Once your devnet is up and running, you can proceed to explore ICN features, develop applications, or contribute to its core components! 