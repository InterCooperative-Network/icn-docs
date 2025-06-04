# ICN CLI Command Reference

## Overview

The ICN Command Line Interface (CLI) provides a comprehensive set of tools for interacting with ICN nodes, managing mesh jobs, participating in governance, and administering network resources. This guide covers all available commands, their options, and practical usage examples.

## Installation and Setup

### Installing the CLI

```bash
# From source (recommended for development)
git clone https://github.com/InterCooperative-Network/icn-core.git
cd icn-core
cargo build --release
cp target/release/icn-cli /usr/local/bin/

# Or build and install directly
cargo install --path crates/icn-cli
```

### Configuration

The CLI can be configured via environment variables, configuration files, or command-line flags.

#### Environment Variables
```bash
export ICN_NODE_URL="http://localhost:3000"
export ICN_DID="did:key:your-identity"
export ICN_LOG_LEVEL="info"
```

#### Configuration File
Create `~/.icn/config.toml`:
```toml
[node]
url = "http://localhost:3000"
timeout = 30

[identity]
did = "did:key:your-identity"
key_file = "~/.icn/keys/private.pem"

[logging]
level = "info"
format = "json"
```

## Command Structure

All ICN CLI commands follow this general structure:
```
icn-cli [GLOBAL_OPTIONS] <COMMAND> [COMMAND_OPTIONS] [ARGUMENTS]
```

### Global Options

| Option | Short | Description | Default |
|--------|-------|-------------|---------|
| `--node-url` | `-n` | ICN node URL to connect to | `http://localhost:3000` |
| `--did` | `-d` | Your DID for authentication | From config file |
| `--verbose` | `-v` | Verbose output | `false` |
| `--quiet` | `-q` | Quiet output (errors only) | `false` |
| `--output` | `-o` | Output format (json, yaml, table) | `table` |
| `--config` | `-c` | Path to config file | `~/.icn/config.toml` |
| `--help` | `-h` | Show help information | |

## Node Management Commands

### icn-cli info

Get basic information about the connected node.

```bash
icn-cli info [OPTIONS]
```

**Options:**
- `--detailed`: Show detailed node information including capabilities and version

**Examples:**
```bash
# Basic node information
icn-cli info

# Detailed information
icn-cli info --detailed

# JSON output
icn-cli info --output json
```

**Sample Output:**
```
Node Information
================
Node ID:    did:key:z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK
Version:    0.1.0
Status:     Online
Uptime:     2d 14h 32m
Peers:      47 connected
Mana:       8,432 available
```

### icn-cli status

Check the operational status of the node and its services.

```bash
icn-cli status [OPTIONS]
```

**Options:**
- `--services`: Show status of individual services
- `--health`: Run health checks
- `--watch`: Continuously monitor status

**Examples:**
```bash
# Basic status
icn-cli status

# Service-level status
icn-cli status --services

# Health check
icn-cli status --health

# Continuous monitoring
icn-cli status --watch
```

## DAG Operations

### icn-cli dag put

Store data in the content-addressed DAG.

```bash
icn-cli dag put [OPTIONS] <DATA>
```

**Options:**
- `--file, -f`: Read data from file instead of argument
- `--format`: Data format (raw, json, cbor)
- `--pin`: Pin the data to prevent garbage collection
- `--links`: Specify links to other DAG nodes

**Examples:**
```bash
# Store simple data
icn-cli dag put "Hello, ICN!"

# Store from file
icn-cli dag put --file document.json --format json

# Store with links
icn-cli dag put --file data.json --links "QmHash1,QmHash2"

# Pin important data
icn-cli dag put --file important.dat --pin
```

### icn-cli dag get

Retrieve data from the DAG by Content ID (CID).

```bash
icn-cli dag get [OPTIONS] <CID>
```

**Options:**
- `--output-file, -o`: Write output to file
- `--format`: Output format (raw, json, cbor)
- `--follow-links`: Recursively fetch linked data

**Examples:**
```bash
# Get data by CID
icn-cli dag get QmYourContentID

# Save to file
icn-cli dag get QmYourContentID --output-file retrieved.json

# Follow links
icn-cli dag get QmYourContentID --follow-links
```

### icn-cli dag list

List DAG blocks stored on the node.

```bash
icn-cli dag list [OPTIONS]
```

**Options:**
- `--limit, -l`: Maximum number of blocks to show
- `--filter`: Filter by content type or metadata
- `--pinned-only`: Show only pinned blocks

**Examples:**
```bash
# List all blocks
icn-cli dag list

# List recent blocks
icn-cli dag list --limit 10

# List pinned blocks only
icn-cli dag list --pinned-only
```

## Mesh Computing

### icn-cli mesh submit

Submit a job to the distributed compute mesh.

```bash
icn-cli mesh submit [OPTIONS] <JOB_SPEC>
```

**Options:**
- `--file, -f`: Read job specification from file
- `--priority`: Job priority (1-10)
- `--deadline`: Job deadline (ISO 8601 format)
- `--max-cost`: Maximum mana cost for job
- `--requirements`: Resource requirements (JSON)

**Examples:**
```bash
# Submit simple job
icn-cli mesh submit '{"type":"compute","code":"fn main() { println!(\"Hello\"); }"}'

# Submit from file
icn-cli mesh submit --file job.json

# Submit with constraints
icn-cli mesh submit --file job.json --priority 8 --max-cost 1000 --deadline "2024-12-31T23:59:59Z"
```

**Job Specification Format:**
```json
{
  "type": "compute",
  "code": "wasm_binary_base64",
  "args": ["arg1", "arg2"],
  "resources": {
    "cpu_seconds": 60,
    "memory_mb": 512,
    "storage_mb": 100
  },
  "environment": {
    "key": "value"
  }
}
```

### icn-cli mesh status

Check the status of submitted mesh jobs.

```bash
icn-cli mesh status [OPTIONS] [JOB_ID]
```

**Options:**
- `--all`: Show all jobs for your DID
- `--filter`: Filter by job status (pending, running, completed, failed)
- `--follow, -f`: Follow job progress in real-time

**Examples:**
```bash
# Check specific job
icn-cli mesh status job_id_12345

# Check all your jobs
icn-cli mesh status --all

# Monitor running jobs
icn-cli mesh status --filter running --follow
```

### icn-cli mesh cancel

Cancel a pending or running mesh job.

```bash
icn-cli mesh cancel [OPTIONS] <JOB_ID>
```

**Options:**
- `--force`: Force cancellation of running jobs
- `--reason`: Reason for cancellation

**Examples:**
```bash
# Cancel pending job
icn-cli mesh cancel job_id_12345

# Force cancel running job
icn-cli mesh cancel job_id_12345 --force --reason "Resource constraint"
```

### icn-cli mesh results

Retrieve results from completed mesh jobs.

```bash
icn-cli mesh results [OPTIONS] <JOB_ID>
```

**Options:**
- `--output-file, -o`: Save results to file
- `--format`: Output format (json, raw, pretty)
- `--include-logs`: Include execution logs

**Examples:**
```bash
# Get job results
icn-cli mesh results job_id_12345

# Save results to file
icn-cli mesh results job_id_12345 --output-file results.json

# Include execution logs
icn-cli mesh results job_id_12345 --include-logs
```

## Network Operations

### icn-cli network peers

Manage and query network peer connections.

```bash
icn-cli network peers [OPTIONS] [COMMAND]
```

**Subcommands:**
- `list`: List connected peers
- `connect <peer_id>`: Connect to a specific peer
- `disconnect <peer_id>`: Disconnect from a peer
- `info <peer_id>`: Get detailed peer information

**Examples:**
```bash
# List all connected peers
icn-cli network peers list

# Connect to specific peer
icn-cli network peers connect /ip4/192.168.1.100/tcp/4001/p2p/QmPeerID

# Get peer information
icn-cli network peers info QmPeerID

# Disconnect from peer
icn-cli network peers disconnect QmPeerID
```

### icn-cli network discover

Discover peers in the network.

```bash
icn-cli network discover [OPTIONS]
```

**Options:**
- `--timeout`: Discovery timeout in seconds
- `--count`: Number of peers to discover
- `--filter`: Filter by capabilities or attributes

**Examples:**
```bash
# Basic peer discovery
icn-cli network discover

# Discover with timeout
icn-cli network discover --timeout 30

# Discover compute-capable peers
icn-cli network discover --filter "capability:compute"
```

### icn-cli network send

Send direct messages to network peers.

```bash
icn-cli network send [OPTIONS] <PEER_ID> <MESSAGE>
```

**Options:**
- `--file, -f`: Send file contents as message
- `--encrypted`: Encrypt message for recipient
- `--urgent`: Mark message as urgent

**Examples:**
```bash
# Send simple message
icn-cli network send QmPeerID "Hello from ICN!"

# Send file
icn-cli network send QmPeerID --file message.json

# Send encrypted message
icn-cli network send QmPeerID "Secret data" --encrypted
```

## Governance Commands

### icn-cli gov proposals

Manage governance proposals.

```bash
icn-cli gov proposals [OPTIONS] [COMMAND]
```

**Subcommands:**
- `list`: List active proposals
- `create`: Create new proposal
- `view <proposal_id>`: View proposal details
- `vote <proposal_id> <vote>`: Vote on proposal

**Examples:**
```bash
# List active proposals
icn-cli gov proposals list

# View specific proposal
icn-cli gov proposals view prop_12345

# Vote on proposal
icn-cli gov proposals vote prop_12345 yes

# Create parameter proposal
icn-cli gov proposals create --file parameter_proposal.json
```

### icn-cli gov vote

Cast votes on governance proposals.

```bash
icn-cli gov vote [OPTIONS] <PROPOSAL_ID> <VOTE>
```

**Options:**
- `--reason`: Provide reason for vote
- `--delegate`: Vote on behalf of delegators
- `--weight`: Vote weight (if applicable)

**Vote Options:** `yes`, `no`, `abstain`

**Examples:**
```bash
# Simple vote
icn-cli gov vote prop_12345 yes

# Vote with reason
icn-cli gov vote prop_12345 no --reason "Insufficient impact analysis"

# Delegate vote
icn-cli gov vote prop_12345 yes --delegate
```

### icn-cli gov delegate

Manage voting delegation.

```bash
icn-cli gov delegate [OPTIONS] [COMMAND]
```

**Subcommands:**
- `to <delegate_did>`: Delegate votes to another DID
- `revoke`: Revoke existing delegation
- `status`: Check current delegation status

**Examples:**
```bash
# Delegate votes
icn-cli gov delegate to did:key:z6MkDelegateDID

# Revoke delegation
icn-cli gov delegate revoke

# Check delegation status
icn-cli gov delegate status
```

## Economic Operations

### icn-cli mana

Manage mana balance and transactions.

```bash
icn-cli mana [OPTIONS] [COMMAND]
```

**Subcommands:**
- `balance`: Check current mana balance
- `history`: View mana transaction history
- `regeneration`: Check regeneration rate and capacity

**Examples:**
```bash
# Check mana balance
icn-cli mana balance

# View transaction history
icn-cli mana history --limit 50

# Check regeneration info
icn-cli mana regeneration
```

### icn-cli tokens

Manage tokenized assets (future feature).

```bash
icn-cli tokens [OPTIONS] [COMMAND]
```

**Subcommands:**
- `balance`: Check token balances
- `transfer`: Transfer tokens
- `history`: View token transaction history

**Examples:**
```bash
# Check token balances
icn-cli tokens balance

# Transfer tokens
icn-cli tokens transfer --to did:key:recipient --amount 100 --token COOP_TOKEN

# View transfer history
icn-cli tokens history
```

## Identity Management

### icn-cli identity

Manage decentralized identity and keys.

```bash
icn-cli identity [OPTIONS] [COMMAND]
```

**Subcommands:**
- `create`: Create new DID and keypair
- `show`: Display current identity information
- `export`: Export identity for backup
- `import`: Import identity from backup

**Examples:**
```bash
# Create new identity
icn-cli identity create --name "My ICN Identity"

# Show current identity
icn-cli identity show

# Export for backup
icn-cli identity export --file identity_backup.json

# Import from backup
icn-cli identity import --file identity_backup.json
```

### icn-cli keys

Manage cryptographic keys.

```bash
icn-cli keys [OPTIONS] [COMMAND]
```

**Subcommands:**
- `generate`: Generate new keypair
- `list`: List available keys
- `rotate`: Rotate existing keys

**Examples:**
```bash
# Generate new keypair
icn-cli keys generate --type ed25519

# List keys
icn-cli keys list

# Rotate primary key
icn-cli keys rotate --confirm
```

## Utility Commands

### icn-cli config

Manage CLI configuration.

```bash
icn-cli config [OPTIONS] [COMMAND]
```

**Subcommands:**
- `show`: Display current configuration
- `set <key> <value>`: Set configuration value
- `unset <key>`: Remove configuration value
- `reset`: Reset to default configuration

**Examples:**
```bash
# Show configuration
icn-cli config show

# Set node URL
icn-cli config set node.url "https://mainnet.intercooperative.network"

# Unset log level
icn-cli config unset logging.level

# Reset configuration
icn-cli config reset --confirm
```

### icn-cli logs

View and manage logs.

```bash
icn-cli logs [OPTIONS]
```

**Options:**
- `--follow, -f`: Follow log output
- `--level`: Filter by log level
- `--component`: Filter by component
- `--since`: Show logs since timestamp

**Examples:**
```bash
# View recent logs
icn-cli logs --since "1 hour ago"

# Follow error logs
icn-cli logs --level error --follow

# Component-specific logs
icn-cli logs --component mesh --level debug
```

## Advanced Usage

### Scripting with ICN CLI

The CLI is designed to be script-friendly with consistent JSON output and proper exit codes.

```bash
#!/bin/bash
set -e

# Check node status
if ! icn-cli status --quiet; then
    echo "Node is not available"
    exit 1
fi

# Submit job and get job ID
JOB_ID=$(icn-cli mesh submit --file job.json --output json | jq -r '.job_id')

# Monitor job until completion
while true; do
    STATUS=$(icn-cli mesh status "$JOB_ID" --output json | jq -r '.status')
    case "$STATUS" in
        "completed")
            echo "Job completed successfully"
            icn-cli mesh results "$JOB_ID" --output-file "results_${JOB_ID}.json"
            break
            ;;
        "failed")
            echo "Job failed"
            exit 1
            ;;
        *)
            echo "Job status: $STATUS"
            sleep 5
            ;;
    esac
done
```

### Batch Operations

Process multiple operations efficiently:

```bash
# Submit multiple jobs from directory
for job_file in jobs/*.json; do
    echo "Submitting $job_file"
    icn-cli mesh submit --file "$job_file"
done

# Vote on multiple proposals
icn-cli gov proposals list --output json | \
    jq -r '.proposals[].id' | \
    while read proposal_id; do
        echo "Voting on $proposal_id"
        icn-cli gov vote "$proposal_id" yes
    done
```

### Configuration Profiles

Manage multiple environments:

```bash
# Development environment
icn-cli --config ~/.icn/dev.toml mesh submit --file job.json

# Staging environment  
icn-cli --config ~/.icn/staging.toml mesh submit --file job.json

# Production environment
icn-cli --config ~/.icn/prod.toml mesh submit --file job.json
```

## Error Handling and Troubleshooting

### Common Error Codes

| Exit Code | Description |
|-----------|-------------|
| 0 | Success |
| 1 | General error |
| 2 | Network connection error |
| 3 | Authentication error |
| 4 | Insufficient mana |
| 5 | Permission denied |
| 6 | Resource not found |
| 7 | Invalid input |

### Debugging Commands

```bash
# Verbose output for debugging
icn-cli --verbose mesh submit --file job.json

# Check connectivity
icn-cli network peers list --output json

# Validate configuration
icn-cli config show --verbose

# Test authentication
icn-cli identity show
```

### Getting Help

```bash
# General help
icn-cli --help

# Command-specific help
icn-cli mesh --help
icn-cli mesh submit --help

# List all available commands
icn-cli --help | grep "COMMANDS:" -A 20
```

## Integration Examples

### CI/CD Integration

```yaml
# GitHub Actions example
name: Deploy to ICN
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Install ICN CLI
        run: |
          curl -L https://releases.intercooperative.network/icn-cli-latest.tar.gz | tar xz
          sudo mv icn-cli /usr/local/bin/
          
      - name: Configure ICN
        run: |
          icn-cli config set node.url "$ICN_NODE_URL"
          icn-cli config set identity.did "$ICN_DID"
        env:
          ICN_NODE_URL: ${{ secrets.ICN_NODE_URL }}
          ICN_DID: ${{ secrets.ICN_DID }}
          
      - name: Deploy application
        run: |
          icn-cli mesh submit --file deploy.json
```

### Monitoring Integration

```bash
#!/bin/bash
# Health check script for monitoring systems

# Check node health
if ! icn-cli status --health --quiet; then
    echo "CRITICAL: ICN node health check failed"
    exit 2
fi

# Check mana balance
MANA=$(icn-cli mana balance --output json | jq -r '.balance')
if [ "$MANA" -lt 1000 ]; then
    echo "WARNING: Low mana balance: $MANA"
    exit 1
fi

echo "OK: ICN node healthy, mana: $MANA"
exit 0
```

The ICN CLI provides a comprehensive interface for all network operations, from basic node management to complex mesh computing and governance participation. Use this reference to efficiently interact with the InterCooperative Network from the command line. 