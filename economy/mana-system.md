# ICN Mana System

## Overview

The mana system is ICN's primary mechanism for resource allocation, Sybil resistance, and network participation management. Unlike traditional "gas" systems, mana regenerates over time and is scoped to individual identities, creating a sustainable and equitable resource management model.

## Core Concepts

### What is Mana?

Mana is a **regenerating capacity credit** that serves multiple purposes in the ICN:

- **Compute Metering**: Required for submitting and executing mesh jobs
- **Network Participation**: Needed for governance voting and proposal submission  
- **Rate Limiting**: Prevents spam and resource abuse
- **Sybil Resistance**: Identity-scoped regeneration limits attackers
- **Economic Incentives**: Encourages long-term network participation

### Key Properties

#### Regenerative
- Mana regenerates automatically over time for each identity
- Regeneration rate influenced by reputation and governance policies
- No permanent depletion - sustainable long-term usage

#### Identity-Scoped
- Each DID maintains its own mana balance
- Cannot be directly transferred between identities
- Prevents concentration of power in few actors

#### Policy-Driven
- Regeneration rates governed by network policies
- Spending requirements defined by governance
- Adaptive to network conditions and needs

## Mana Mechanics

### Regeneration Model

```rust
struct ManaAccount {
    identity: Did,
    current_balance: u64,
    max_capacity: u64,
    last_regeneration: Timestamp,
    regeneration_rate: u64, // mana per second
    reputation_multiplier: f64,
}
```

#### Base Regeneration
All identities receive a base regeneration rate ensuring minimum network participation:

```
base_regeneration_rate = network_base_rate * identity_age_factor
```

#### Reputation Multiplier
Active, positive participation increases regeneration:

```
effective_rate = base_rate * (1.0 + reputation_multiplier)
```

#### Capacity Limits
Maximum mana capacity prevents excessive accumulation:

```
max_capacity = base_capacity * (1.0 + reputation_bonus)
```

### Spending Model

#### Mesh Job Costs
Different job types have different mana costs:

```rust
enum JobType {
    Compute { 
        cpu_seconds: u64,
        memory_mb: u64,
        cost_per_cpu_second: u64,
        cost_per_mb_second: u64,
    },
    Storage {
        data_size_bytes: u64,
        duration_seconds: u64,
        cost_per_gb_hour: u64,
    },
    Network {
        bandwidth_bytes: u64,
        cost_per_gb: u64,
    },
}
```

#### Governance Costs
Participation in governance requires mana:

- **Proposal Submission**: High cost to prevent spam
- **Voting**: Moderate cost to ensure engagement
- **Delegation**: Low cost for accessibility

```rust
pub struct GovernanceCosts {
    pub proposal_submission: u64,    // 1000 mana
    pub vote_casting: u64,           // 10 mana  
    pub delegation: u64,             // 1 mana
}
```

## Implementation Architecture

### Core Components

#### ManaRepositoryAdapter
Abstract interface for mana storage and operations:

```rust
pub trait ManaRepositoryAdapter {
    async fn get_balance(&self, identity: &Did) -> Result<u64, CommonError>;
    async fn spend_mana(&self, identity: &Did, amount: u64) -> Result<(), CommonError>;
    async fn regenerate_mana(&self, identity: &Did) -> Result<u64, CommonError>;
    async fn get_max_capacity(&self, identity: &Did) -> Result<u64, CommonError>;
}
```

#### ResourcePolicyEnforcer
Enforces mana spending policies and requirements:

```rust
pub struct ResourcePolicyEnforcer {
    repository: Box<dyn ManaRepositoryAdapter>,
    policies: PolicyConfiguration,
}

impl ResourcePolicyEnforcer {
    pub async fn charge_mana(
        &self, 
        identity: &Did, 
        operation: OperationType,
        amount: u64
    ) -> Result<(), CommonError> {
        // Verify sufficient balance
        // Apply operation-specific policies
        // Execute charge
        // Update reputation if applicable
    }
}
```

### Storage Backends

#### In-Memory (Development)
```rust
pub struct InMemoryManaStore {
    accounts: Arc<RwLock<HashMap<Did, ManaAccount>>>,
}
```

#### Persistent (Production)
```rust
pub struct SledManaStore {
    db: sled::Db,
}
```

#### Distributed (Future)
```rust
pub struct DistributedManaStore {
    dag_store: Arc<dyn StorageService>,
    consensus: ConsensusProtocol,
}
```

## Economic Policy Framework

### Policy Types

#### Network-Wide Policies
Applied globally across all identities:

```yaml
network_policies:
  base_regeneration_rate: 100  # mana per hour
  base_capacity: 10000         # maximum mana
  job_base_cost: 10           # minimum cost per job
  governance_costs:
    proposal: 1000
    vote: 10
    delegation: 1
```

#### Identity-Specific Modifiers
Applied based on identity characteristics:

```yaml
identity_modifiers:
  reputation_bonus:
    excellent: 2.0    # 200% regeneration
    good: 1.5         # 150% regeneration  
    neutral: 1.0      # 100% regeneration (default)
    poor: 0.5         # 50% regeneration
  
  age_bonus:
    calculation: "min(1.0 + (age_days / 365) * 0.1, 2.0)"
    max_bonus: 2.0
```

#### Cooperative Policies
Applied within cooperative contexts:

```yaml
cooperative_policies:
  member_bonus: 1.2           # 20% bonus for coop members
  shared_capacity: true       # Allow capacity sharing
  bulk_operations: 0.9        # 10% discount for bulk jobs
```

## Integration with Other Systems

### Mesh Computing
```rust
pub async fn submit_mesh_job(
    &self,
    submitter: &Did,
    job: &MeshJob
) -> Result<JobId, CommonError> {
    // Calculate job cost
    let cost = self.calculate_job_cost(job)?;
    
    // Charge mana
    self.economy_service
        .charge_mana(submitter, OperationType::MeshJob, cost)
        .await?;
    
    // Submit job
    let job_id = self.job_manager.submit_job(job).await?;
    
    Ok(job_id)
}
```

### Governance System
```rust
pub async fn submit_proposal(
    &self,
    proposer: &Did,
    proposal: &Proposal
) -> Result<ProposalId, CommonError> {
    // Verify mana for proposal
    self.economy_service
        .charge_mana(
            proposer, 
            OperationType::GovernanceProposal, 
            self.governance_costs.proposal_submission
        )
        .await?;
    
    // Submit proposal
    self.governance_service
        .submit_proposal(proposer, proposal)
        .await
}
```

### Identity and Reputation
```rust
pub async fn update_reputation(
    &self,
    identity: &Did,
    reputation_delta: i64
) -> Result<(), CommonError> {
    // Update reputation score
    let new_reputation = self.reputation_service
        .update_score(identity, reputation_delta)
        .await?;
    
    // Recalculate mana regeneration rate
    self.economy_service
        .update_regeneration_rate(identity, new_reputation)
        .await?;
    
    Ok(())
}
```

## Anti-Gaming Mechanisms

### Sybil Resistance
- **Identity Verification**: DIDs require proof of unique identity
- **Reputation Gating**: New identities have reduced regeneration
- **Network Effects**: Value increases with legitimate participation

### Resource Abuse Prevention
- **Rate Limiting**: Mana caps prevent excessive resource consumption
- **Cost Scaling**: Expensive operations require significant mana investment
- **Monitoring**: Unusual spending patterns trigger investigation

### Fairness Enforcement
- **Equal Base Access**: All identities get minimum regeneration
- **Merit-Based Bonuses**: Reputation rewards legitimate contribution
- **Governance Oversight**: Community can adjust parameters

## Monitoring and Analytics

### Key Metrics
```rust
pub struct ManaMetrics {
    pub total_supply: u64,
    pub active_accounts: u64,
    pub average_balance: f64,
    pub regeneration_rate_distribution: HashMap<String, u64>,
    pub spending_by_category: HashMap<OperationType, u64>,
    pub policy_effectiveness: PolicyEffectivenessMetrics,
}
```

### Health Indicators
- **Supply Distribution**: Ensure no excessive concentration
- **Regeneration Efficiency**: Monitor regeneration vs spending
- **Network Utilization**: Track resource usage patterns
- **Gaming Detection**: Identify suspicious patterns

## Future Enhancements

### Planned Features
- **Dynamic Pricing**: Adjust costs based on network demand
- **Cooperative Sharing**: Enable mana sharing within cooperatives
- **Delegation Mechanisms**: Allow temporary mana delegation
- **Cross-Network Interop**: Mana recognition across federations

### Research Areas
- **Advanced Reputation Models**: More sophisticated trust metrics
- **Machine Learning Integration**: Predictive mana allocation
- **Privacy Preservation**: Zero-knowledge mana proofs
- **Environmental Incentives**: Bonus for sustainable practices

## Best Practices

### For Developers
- Always check mana balance before expensive operations
- Implement graceful degradation when mana is insufficient
- Cache mana balance to reduce lookup overhead
- Use batch operations to minimize mana costs

### For Node Operators
- Monitor mana regeneration rates and adjust policies
- Set appropriate cost structures for different operations
- Implement alerting for unusual mana spending patterns
- Regularly review and update governance policies

### For Users
- Build reputation through consistent positive participation
- Balance mana spending across different operation types
- Participate in governance to influence mana policies
- Consider joining cooperatives for potential benefits

The mana system creates a sustainable, equitable foundation for resource management in the InterCooperative Network, enabling fair access while preventing abuse and encouraging positive participation. 