# ICN Core Crates API Reference

This document provides a comprehensive reference for all crates within the `icn-core` workspace, organized by their functional responsibilities within the InterCooperative Network.

## Overview

The `icn-core` workspace contains the foundational Rust libraries that implement the InterCooperative Network protocol. These crates are designed to be modular, well-tested, and provide clear interfaces for building ICN nodes, CLI tools, and other network infrastructure.

## Core Foundation Crates

### icn-common

**Purpose**: Shared data structures, types, utilities, and error definitions used across all ICN crates.

**Key Types**:
- `Did` - Decentralized Identifier representation
- `Cid` - Content Identifier for DAG operations
- `CommonError` - Comprehensive error enumeration
- Common serialization and utility functions

**Dependencies**: Minimal external dependencies (serde, bs58)
**Location**: `crates/icn-common/`

### icn-protocol

**Purpose**: Core message formats, communication protocols, and protocol-level abstractions.

**Key Types**:
- Protocol message definitions
- Communication protocol abstractions
- Protocol version negotiation types

**Dependencies**: `icn-common`
**Location**: `crates/icn-protocol/`

## Identity & Security

### icn-identity

**Purpose**: Decentralized identity (DID) management, verifiable credentials, and cryptographic operations.

**Key Features**:
- DID generation and resolution
- Cryptographic key management
- Verifiable credential handling
- Execution receipt generation and verification

**Key Types**:
- `ExecutionReceipt` - Verifiable proof of computation
- DID key generation utilities
- Credential verification types

**Dependencies**: `icn-common`, cryptographic libraries
**Location**: `crates/icn-identity/`

## Data & Storage

### icn-dag

**Purpose**: Content-addressed Directed Acyclic Graph (DAG) storage and manipulation.

**Key Features**:
- Content-addressed block storage
- DAG traversal and manipulation
- Multiple storage backend support

**Key Types**:
- `StorageService` trait - Abstract storage interface
- `InMemoryDagStore` - In-memory implementation
- `FileDagStore` - File-based implementation
- Block put/get operations

**Dependencies**: `icn-common`
**Location**: `crates/icn-dag/`

## Economic System

### icn-economics

**Purpose**: ICN's dual economic model implementation including mana (regenerating capacity credits) and tokenized assets.

**Key Features**:
- Regenerating mana system
- Token accounting and transfers
- Economic policy enforcement
- Resource metering and billing

**Key Types**:
- `ResourcePolicyEnforcer` - Mana usage enforcement
- `ManaRepositoryAdapter` - Mana ledger interface
- Economic policy types
- Token and asset management

**Dependencies**: `icn-common`, `icn-identity`
**Location**: `crates/icn-economics/`

## Governance System

### icn-governance

**Purpose**: Proposal creation, voting mechanisms, and governance parameter management.

**Key Features**:
- Proposal lifecycle management
- Voting system implementation  
- Governance parameter management
- Quorum and consensus logic

**Key Types**:
- `Proposal` - Governance proposal representation
- `Vote` - Individual vote representation
- `GovernanceModule` - Main governance interface
- In-memory and persistent implementations

**Dependencies**: `icn-common`, `icn-identity`
**Location**: `crates/icn-governance/`

## Networking & Communication

### icn-network

**Purpose**: Peer-to-peer networking abstractions and libp2p-based implementation.

**Key Features**:
- P2P network service abstraction
- libp2p integration (Kademlia, Gossipsub)
- Peer discovery and messaging
- Network message routing

**Key Types**:
- `NetworkService` trait - Abstract network interface
- `NetworkMessage` enum - Network message types
- Libp2p service implementation
- Peer discovery and management

**Dependencies**: `icn-common`, `icn-protocol`, libp2p
**Location**: `crates/icn-network/`

## Compute & Execution

### icn-runtime

**Purpose**: Node host runtime, WebAssembly execution environment, and job orchestration.

**Key Features**:
- Host ABI for WASM execution
- Runtime context management
- Job lifecycle orchestration
- Resource metering and limits

**Key Types**:
- `RuntimeContext` - Runtime state management
- `JobManager` - Job execution coordination
- Host ABI functions (`host_*` functions)
- WASM execution environment

**Key Files**:
- `src/abi.rs` - Host ABI definitions
- `src/context.rs` - Runtime context
- `src/lib.rs` - Main runtime logic

**Dependencies**: `icn-common`, `icn-mesh`, `icn-economics`, WASM runtime
**Location**: `crates/icn-runtime/`

### icn-mesh

**Purpose**: Distributed compute mesh implementation including job submission, bidding, and execution.

**Key Features**:
- Mesh job lifecycle management
- Executor selection and bidding
- Job scheduling and assignment
- Fault tolerance and error handling

**Key Types**:
- `ActualMeshJob` - Mesh job representation
- `MeshJobBid` - Executor bid representation
- `JobState` - Job lifecycle states
- Executor selection algorithms

**Key Functions**:
- `select_executor` - Executor selection logic
- `score_bid` - Bid scoring algorithm
- Job state transition management

**Dependencies**: `icn-common`, `icn-identity`, `icn-economics`
**Location**: `crates/icn-mesh/`

## Contract Language

### icn-ccl

**Purpose**: Cooperative Contract Language (CCL) compiler that transforms governance policies into executable WASM modules.

**Key Features**:
- CCL grammar parsing
- WASM compilation target
- Policy validation and optimization
- Runtime integration

**Status**: In development - will utilize CCL grammar from legacy CoVM
**Location**: `icn-ccl/` (repository root)

## API & Interface Layer

### icn-api

**Purpose**: Service interfaces, DTOs, and API trait definitions for external consumption.

**Key Features**:
- REST/JSON-RPC API definitions
- Data Transfer Objects (DTOs)
- Service trait definitions
- API versioning support

**Role**: Single source of truth for external API contracts
**Dependencies**: `icn-common`
**Location**: `crates/icn-api/`

## Application Binaries

### icn-node

**Purpose**: Main ICN node daemon binary that integrates all core services.

**Key Features**:
- HTTP API server implementation
- Service coordination and lifecycle
- Configuration management
- Persistent storage integration

**Dependencies**: All core crates, HTTP server framework
**Location**: `crates/icn-node/`

### icn-cli

**Purpose**: Command-line interface for interacting with ICN nodes and networks.

**Key Features**:
- Node interaction commands
- DAG operations (put/get)
- Network operations (peer discovery, messaging)
- Mesh job management

**Dependencies**: `icn-api`, `icn-common`
**Location**: `crates/icn-cli/`

## Mesh Job Execution Pipeline

The canonical mesh job lifecycle involves multiple crates working together:

1. **Submission** (`icn-runtime`, `icn-mesh`)
   - Job submitted via Host ABI
   - Mana verification via `icn-economics`
   - Job added to pending queue

2. **Bidding** (`icn-mesh`, `icn-network`)  
   - Job announced to network
   - Executors submit bids
   - Reputation and resource verification

3. **Assignment** (`icn-runtime`, `icn-mesh`)
   - Executor selection algorithm
   - Job state transition to assigned
   - Executor notification

4. **Execution** (External executor)
   - Job execution by assigned node
   - Result production and signing

5. **Anchoring** (`icn-runtime`, `icn-dag`, `icn-identity`)
   - Receipt validation and storage
   - DAG anchoring of results
   - Reputation system updates

## Testing and Development

All crates include comprehensive testing:
- Unit tests for individual components
- Integration tests for cross-crate interactions  
- End-to-end tests for complete workflows
- Property-based testing where applicable

## API Stability

- **Internal APIs**: Between core crates, may change with proper deprecation
- **External APIs**: Defined in `icn-api`, follow semantic versioning
- **Host ABI**: Runtime ABI functions maintain backward compatibility

## Error Handling

All crates follow a consistent error handling philosophy:
- Return `Result<T, CommonError>` for recoverable errors
- Specific error variants in `CommonError` enum
- Context preservation through error propagation
- No panics in library code

## Documentation

Each crate maintains:
- README.md with purpose and usage
- Comprehensive rustdoc for public APIs
- Code examples and integration guides
- Architecture decision records where relevant 