# Roadmap

## Overview

```
RFP Submission    Month 1    Month 2    Month 3    Month 4    Month 5
     |              |          |          |          |          |
     ▼              |----------|----------|----------|----------|
[Interest Form]    [  Tranche 1  ][    Tranche 2    ][     Tranche 3      ]
     |                SDK Core      Wallet + Docs       Mainnet Launch
     ▼                                                        ↓
[Build Form]                                            Meridian 2025
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

## Tranche 1: SDK Core

**Budget:** $20,000
**Duration:** ~6 weeks
**Team:** Jose + Contractor 1

### Milestones

| Week | Milestone |
|------|-----------|
| 1 | Project setup, contractor onboarding |
| 2 | C-Address resolution module |
| 3 | Direct funding module |
| 4 | PasskeyKit integration |
| 5 | Testing, documentation |
| 6 | npm publish, submission prep |

### Deliverables

| Deliverable | Success Criteria | Status |
|-------------|------------------|--------|
| C-Address Funding SDK (TypeScript) | npm package published | Not started |
| C-Address Resolution Module | Resolve C-address → contract | Not started |
| PasskeyKit Integration | SDK works with passkey-kit | Not started |
| Unit Test Suite | 80%+ coverage | Not started |
| Basic Documentation | README + API reference | Not started |

### Checkpoint

- [ ] SDK installs and runs locally
- [ ] Can resolve a C-address on testnet
- [ ] Can fund a C-address on testnet
- [ ] Tests pass in CI
- [ ] Documentation reviewed

---

## Tranche 2: Wallet + Docs

**Budget:** $30,000
**Duration:** ~8 weeks
**Team:** Jose + Contractor 1 + Contractor 2

### Milestones

| Week | Milestone |
|------|-----------|
| 1-2 | Python bindings development |
| 2-3 | Wallet UI scaffolding |
| 4-5 | Onboarding flow implementation |
| 5-6 | Funding flow implementation |
| 6-7 | Standards documentation |
| 7-8 | SEP draft, demo video |

### Deliverables

| Deliverable | Success Criteria | Status |
|-------------|------------------|--------|
| Reference Wallet v1 (Testnet) | Working onboard/fund/transact | Not started |
| Python SDK Bindings | PyPI package published | Not started |
| Onboarding Standards Doc v1 | Developer guide + patterns | Not started |
| SEP Draft | Submitted to stellar/stellar-protocol | Not started |
| Demo Video | End-to-end flow recorded | Not started |

### Checkpoint

- [ ] Wallet deploys to Vercel staging
- [ ] Can create passkey wallet and receive C-address
- [ ] Can fund C-address from external source
- [ ] Python SDK installs and works
- [ ] SEP draft submitted as PR

---

## Tranche 3: Mainnet Launch

**Budget:** $40,000
**Duration:** ~8 weeks
**Team:** Jose + Contractor 1 + Contractor 2

### Milestones

| Week | Milestone |
|------|-----------|
| 1-2 | Mainnet preparation, security review |
| 2-3 | Integration example: Exchange |
| 3-4 | Integration example: Fiat on-ramp |
| 4-5 | Integration example: AI Agent |
| 5-6 | Final documentation |
| 6-7 | Production deployment |
| 7-8 | Community handoff, Meridian prep |

### Deliverables

| Deliverable | Success Criteria | Status |
|-------------|------------------|--------|
| Reference Wallet v2 (Mainnet) | Production with monitoring | Not started |
| Integration Examples (3+) | Exchange, fiat, AI agent | Not started |
| Final Documentation | Complete developer portal | Not started |
| SDK v1.0 Stable | TS + Python stable releases | Not started |
| Migration Guide | G-address → C-address path | Not started |
| Community Handoff Plan | Maintenance docs, contrib guide | Not started |

### Checkpoint

- [ ] Wallet live on mainnet
- [ ] All 3 integration examples working
- [ ] Documentation site deployed
- [ ] SDK v1.0.0 tagged and released
- [ ] Contribution guide published

---

## Post-Launch

### Meridian 2025 Presentation

- [ ] Prepare demo
- [ ] Prepare slides
- [ ] Coordinate with SDF

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
