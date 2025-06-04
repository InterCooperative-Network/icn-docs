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

All identities receive a base regeneration rate ensuring minimum network participation. Active, positive participation increases regeneration through reputation multipliers, while capacity limits prevent excessive accumulation.

### Spending Model

#### Mesh Job Costs
Different job types have different mana costs based on:
- CPU seconds required
- Memory usage
- Storage requirements
- Network bandwidth

#### Governance Costs
Participation in governance requires mana:
- **Proposal Submission**: High cost to prevent spam
- **Voting**: Moderate cost to ensure engagement
- **Delegation**: Low cost for accessibility

## Economic Policy Framework

### Network-Wide Policies
Applied globally across all identities for base regeneration rates, capacity limits, and minimum job costs.

### Identity-Specific Modifiers
Applied based on reputation and participation history:
- Excellent reputation: 200% regeneration rate
- Good reputation: 150% regeneration rate
- Neutral reputation: 100% regeneration rate (default)
- Poor reputation: 50% regeneration rate

### Cooperative Policies
Applied within cooperative contexts, including member bonuses, shared capacity options, and bulk operation discounts.

## Integration with Other Systems

### Mesh Computing
All mesh job submissions require mana verification before processing. Job costs are calculated based on resource requirements and charged upon submission.

### Governance System
Proposal submission and voting require appropriate mana expenditure to ensure serious participation and prevent spam.

### Identity and Reputation
Reputation changes directly affect mana regeneration rates, creating incentives for positive network participation.

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

The mana system creates a sustainable, equitable foundation for resource management in the InterCooperative Network, enabling fair access while preventing abuse and encouraging positive participation. 