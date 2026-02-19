# Roadmap

## Overview

```
RFP Submission    Month 1    Month 2    Month 3    Month 4    Month 5
     |              |          |          |          |          |
     ▼              |----------|----------|----------|----------|
[Interest Form]    [  Tranche 1  ][    Tranche 2    ][     Tranche 3      ]
     |              SDK + Proxy    Wallet + Standards   Mainnet Launch
     ▼
[Build Form]
     |
     ▼
[Approval]
```

---

## Pre-Project Phase

### RFP Submission

| Task | Status | Target |
|------|--------|--------|
| Design document | Done | 2025-02-14 |
| Technical architecture | Done | 2025-02-14 |
| Repository setup | Done | 2025-02-14 |
| Interest Form draft | Not started | Week of 2025-02-17 |
| Submit Interest Form | Not started | Q1 2026 cycle |
| Build Form preparation | Not started | Upon invitation |
| Submit Build Form | Not started | Upon invitation |

### Dependencies

- [ ] AI Agent Kit Tranche 2 approved (~14 business days)
- [ ] AI Agent Kit Tranche 3 submitted
- [ ] RFP Interest Form invitation received
- [ ] Build Form approved

---

## Tranche 1: SDK Core + Proxy

**Budget:** $24,000
**Duration:** ~6 weeks
**Team:** Jose + BlockchainOracle

### Milestones

| Week | Milestone |
|------|-----------|
| 1 | Project setup, contractor onboarding |
| 2 | C-Address resolution module |
| 3 | Direct funding module + Smart Wallet auth |
| 4 | G-to-C Proxy Contract development |
| 5 | Relayer service + Testing |
| 6 | npm publish, submission prep |

### Deliverables

| Deliverable | Success Criteria | Budget |
|-------------|------------------|--------|
| C-Address Funding SDK (TypeScript) | npm package published | $7,000 |
| C-Address Resolution & Discovery Module | <500ms resolution | $4,000 |
| Smart Wallet Authentication Layer | Integration tests passing | $3,000 |
| Test Suite + CI/CD | 80%+ coverage | $3,000 |
| G-to-C Proxy Contract + Relayer | Deployed on testnet, <30s forwarding | $7,000 |

### Checkpoint

- [ ] SDK installs and runs locally
- [ ] Can resolve a C-address on testnet
- [ ] Can fund a C-address on testnet
- [ ] Proxy contract deployed, relayer running
- [ ] Tests pass in CI
- [ ] Documentation reviewed

---

## Tranche 2: Wallet + Standards

**Budget:** $36,000
**Duration:** ~8 weeks
**Team:** Jose + BlockchainOracle + Dan Garcia

### Milestones

| Week | Milestone |
|------|-----------|
| 1-2 | Reference Wallet v1 scaffolding |
| 2-3 | Passkey onboarding + C-address display |
| 3-4 | Token display + transaction history |
| 4-5 | Python SDK bindings |
| 5-6 | Onboarding Standards documentation |
| 6-7 | SEP draft + ecosystem wallet outreach |
| 7-8 | Feedback incorporation, submission prep |

### Deliverables

| Deliverable | Success Criteria | Budget |
|-------------|------------------|--------|
| Reference Wallet v1 (Testnet) | Deployed to Vercel, passkey onboarding works | $12,000 |
| Token Display + Transaction History | SEP-41 tokens, tx history visible | $6,000 |
| Python SDK Bindings | Published on PyPI | $7,000 |
| Onboarding Standards Documentation | Developer guide deployed | $5,000 |
| SEP Draft (C-Address Discovery/Funding) | PR submitted to stellar/stellar-protocol | $4,000 |
| Ecosystem Wallet Feedback | Feedback from 2+ wallet teams documented | $2,000 |

### Checkpoint

- [ ] Wallet deploys to Vercel staging
- [ ] Can create passkey wallet and receive C-address
- [ ] Can fund C-address via proxy from external source
- [ ] Token balances and tx history display correctly
- [ ] Python SDK installs and works
- [ ] SEP draft submitted as PR
- [ ] Feedback from at least 2 wallet teams incorporated

---

## Tranche 3: Mainnet Launch

**Budget:** $48,000
**Duration:** ~8 weeks
**Team:** Jose + BlockchainOracle + Dan Garcia

### Milestones

| Week | Milestone |
|------|-----------|
| 1-2 | Mainnet preparation, security review via SCF audit credits |
| 2-3 | Reference Wallet v2 production deployment |
| 3-4 | G-to-C Funding Flows + On-ramp Integration Guide |
| 4-5 | Integration examples (exchange, fiat, AI agent) |
| 5-6 | Multi-wallet sign-in, final documentation |
| 6-7 | SDK v1.0 stable release |
| 7-8 | Community handoff, open source finalization |

### Deliverables

| Deliverable | Success Criteria | Budget |
|-------------|------------------|--------|
| Reference Wallet v2 (Mainnet) | Production with Sentry + monitoring | $14,000 |
| G-to-C Funding Flows + On-ramp Integration Guide | CEX→proxy→C-address demo on mainnet | $10,000 |
| Integration Examples (3+) | Exchange, fiat on-ramp, AI agent repos | $6,000 |
| SDK v1.0 Stable | v1.0.0 on npm + PyPI | $4,000 |
| Developer Portal + Final Documentation | Complete docs site with search | $6,000 |
| Multi-Wallet Sign-in | Metamask/Phantom/Rabby integration | $4,000 |
| Open Source + Community Handoff | MIT license, CONTRIBUTING.md, maintenance runbook | $4,000 |

### Checkpoint

- [ ] Wallet live on mainnet with real transactions
- [ ] Proxy contract deployed to mainnet
- [ ] All 3 integration examples working with docs
- [ ] Documentation site deployed with search
- [ ] SDK v1.0.0 tagged and released
- [ ] Multi-wallet sign-in working
- [ ] Contribution guide published

---

## Post-Launch

### Maintenance Commitment (6 months)

- [ ] Bug fixes within 48 hours
- [ ] Security patches within 24 hours
- [ ] Community support in Discord
- [ ] Monthly usage reports

---

## Risk Checkpoints

| Checkpoint | Trigger | Action |
|------------|---------|--------|
| Contractor delay | 1 week behind | Bring in backup contractor |
| PasskeyKit breaking change | API change | Coordinate with Jane Wang |
| Scope creep | New feature request | Document for v2, stay focused |
| Approval delay | 2+ weeks past expected | Continue prep work, don't block |
