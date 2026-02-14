# C-Address Toolkit: Design Document

**Date:** 2025-02-14
**Author:** Jose Carlos Toscano
**Status:** Approved

---

## Overview

**Project Name:** C-Address Toolkit: Direct Funding & Onboarding Infrastructure

**One-liner:** A production-grade SDK and reference wallet enabling direct C-address funding without traditional G-addresses, built by a passkey-kit contributor with proven Smart Wallet expertise.

**Target RFP:** C-Address Tooling & Onboarding (Q1 2026)

**Budget:** $100,000

**Timeline:** 5 months, targeting Meridian 2025

---

## Positioning

### Why Me

I lead a software development company specializing in blockchain infrastructure and developer tooling. We handle multiple concurrent projects through a mix of employees, contractors, and freelancers. The Stellar AI Agent Kit is one of several active engagements - not a side project. Our delivery model is proven: for Tranche 2 alone, we shipped 37,000+ lines of production code across 4 merged PRs in under two weeks.

### Authority & Credentials

| Evidence | Detail |
|----------|--------|
| PasskeyKit Contributor | Open source contribution to official repo |
| Smart Wallet Pioneer | Strooper Wallet at HackMeridian 2024 |
| Current SCF Awardee | $108K AI Agent Kit (SCF 37) - fully delivered |
| SDF Endorsement | Nick Gilbert: "key infrastructure" |
| Hackathon Track Record | 5 hackathons, 3 wins, 1 third place |
| npm Traction | 2,047 downloads in 7 weeks (MCP packages) |

### Key Narrative

> "The C-address funding gap exists because current tooling assumes G-addresses. As a passkey-kit contributor building the Stellar AI Agent Kit's policy signer infrastructure, I've already solved adjacent problems. This project applies that expertise to create the missing onboarding layer."

---

## Technical Architecture

### Components

#### 1. C-Address Funding SDK (npm + Python)

Core library for direct C-address funding flows.

```
stellar-c-address-sdk/
├── typescript/          # Primary SDK
│   ├── src/
│   │   ├── funding.ts   # Direct funding without G-address
│   │   ├── discovery.ts # C-address resolution
│   │   └── passkey.ts   # PasskeyKit integration
│   └── package.json
└── python/              # Python bindings (FastMCP compatible)
    └── stellar_c_address/
```

**Key Features:**
- Fund C-addresses directly from any source (exchange, wallet, fiat on-ramp)
- Resolve C-addresses to underlying contract addresses
- Integrate with PasskeyKit for seamless auth
- TypeScript-first with Python bindings

#### 2. Reference Wallet (Web App)

Production-grade example implementation.

**Tech Stack:**
- Next.js + React
- PasskeyKit for authentication
- Freighter/WalletConnect fallback
- Testnet + Mainnet support

**User Flows:**
1. **Onboard** - Create wallet with passkey, get C-address, no seed phrase
2. **Fund** - Receive funds directly to C-address from any source
3. **Transact** - Sign with passkey, execute via smart wallet

#### 3. Onboarding Standards Documentation

- Developer Guide: Integration tutorials with code examples
- Onboarding Patterns: Best practices for C-address UX
- SEP Draft: Proposed standard for C-address discovery/funding
- Migration Guide: Moving users from G-address to C-address flows

#### 4. Integration Examples

- Exchange integration example (simulated deposit flow)
- Fiat on-ramp integration example
- Existing wallet upgrade path
- AI Agent integration (leverages current toolkit)

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────┐
│                    C-Address Toolkit                     │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  ┌──────────────┐    ┌──────────────┐    ┌───────────┐  │
│  │   SDK (TS)   │    │  SDK (Python)│    │  Docs/SEP │  │
│  └──────┬───────┘    └──────┬───────┘    └───────────┘  │
│         │                   │                            │
│         └─────────┬─────────┘                            │
│                   ▼                                      │
│         ┌─────────────────┐                              │
│         │ Reference Wallet │                             │
│         │   (Next.js PWA)  │                             │
│         └────────┬────────┘                              │
│                  │                                       │
├──────────────────┼───────────────────────────────────────┤
│                  ▼           Stellar Infrastructure      │
│  ┌────────────┐  ┌────────────┐  ┌───────────────────┐  │
│  │ PasskeyKit │  │Smart Wallet│  │ Soroban Contracts │  │
│  └────────────┘  └────────────┘  └───────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

---

## Budget

**Total: $100,000**

### Tranche Structure

| Tranche | Payment | Amount | Focus |
|---------|---------|--------|-------|
| #0 | 10% | $10,000 | Upon acceptance |
| #1 | 20% | $20,000 | MVP - SDK Core |
| #2 | 30% | $30,000 | Testnet - Wallet + Docs |
| #3 | 40% | $40,000 | Mainnet - Polish + Launch |

### Budget by Category

| Category | Amount | % |
|----------|--------|---|
| SDK Development (TS + Python) | $35,000 | 35% |
| Reference Wallet | $30,000 | 30% |
| Documentation + SEP | $15,000 | 15% |
| Integration Examples | $10,000 | 10% |
| Infrastructure + DevOps | $5,000 | 5% |
| Contingency | $5,000 | 5% |

---

## Tranche Deliverables

### Tranche 1: SDK Core ($20,000)

| Deliverable | Success Criteria |
|-------------|------------------|
| C-Address Funding SDK (TypeScript) | npm package published, fund C-address without G-address |
| C-Address Resolution Module | Resolve C-address → contract address |
| PasskeyKit Integration | SDK works with existing passkey-kit |
| Unit Test Suite | 80%+ coverage |
| Basic Documentation | README + API reference |

**Team:** You (architecture, PasskeyKit, review) + Contractor 1 (implementation)

### Tranche 2: Wallet + Docs ($30,000)

| Deliverable | Success Criteria |
|-------------|------------------|
| Reference Wallet v1 (Testnet) | Working web app with onboard/fund/transact flows |
| Python SDK Bindings | PyPI package published |
| Onboarding Standards Doc v1 | Developer guide + integration patterns |
| SEP Draft | Submitted to stellar/stellar-protocol |
| Demo Video | End-to-end flow recorded |

**Team:** You (architecture, SEP, SDF coordination) + Contractor 1 (Python) + Contractor 2 (frontend)

### Tranche 3: Mainnet Launch ($40,000)

| Deliverable | Success Criteria |
|-------------|------------------|
| Reference Wallet v2 (Mainnet) | Production deployment with monitoring |
| Integration Examples (3+) | Exchange, fiat on-ramp, AI agent examples |
| Final Documentation | Complete developer portal |
| SDK v1.0 Stable | TypeScript + Python stable releases |
| Migration Guide | G-address → C-address upgrade path |
| Community Handoff Plan | Maintenance docs, contribution guide |

**Team:** You (coordination, community, review) + Contractor 1 (SDK, examples) + Contractor 2 (wallet, infra)

---

## Timeline

```
Month 1    Month 2    Month 3    Month 4    Month 5
|----------|----------|----------|----------|----------|
[  Tranche 1  ][    Tranche 2    ][     Tranche 3      ]
   SDK Core      Wallet + Docs       Mainnet Launch
                                              ↓
                                        Meridian 2025
```

---

## Team Structure

### Project Lead: Jose Carlos Toscano

| Responsibility | Allocation |
|----------------|------------|
| Technical architecture & code review | 30% |
| PasskeyKit/Smart Wallet integration | 25% |
| SDF coordination & SEP authorship | 20% |
| Contractor management | 15% |
| Documentation oversight | 10% |

### Contractor 1: SDK Developer

- Strong TypeScript + Rust experience
- Stellar SDK familiarity
- Test-driven development
- Scope: SDK core, Python bindings, integration examples
- Budget: ~$35-40K across tranches

### Contractor 2: Frontend Developer

- React/Next.js production experience
- Web3 wallet UX experience
- PasskeyKit/WebAuthn familiarity
- Scope: Reference wallet implementation
- Budget: ~$25-30K across Tranches 2-3

---

## Differentiation

### Unique Advantages

1. **Software company owner** - Professional capacity, not a side project
2. **Only passkey-kit contributor** applying for this RFP
3. **Completed SCF delivery** - AI Agent Kit fully built, in approval queue
4. **SDF endorsement** - Nick Gilbert: "key infrastructure"
5. **Proven contractor model** - 37K lines delivered in ~2 weeks

### Key Differentiators

**Professional Capacity:**
> "As the owner of a software development company, I bring professional project management, established contractor relationships, and proven delivery workflows. This isn't a learning exercise - it's our business."

**Continuity of Infrastructure:**
> "This project extends the Smart Wallet infrastructure I'm already building in the AI Agent Kit. The C-Address Toolkit uses the same passkey-kit integration patterns, creating ecosystem coherence rather than fragmented tooling."

**AI Agent Integration (Unique Angle):**
> "The integration examples will include AI agent funding flows - something no other applicant can credibly deliver, given my MCP toolkit work."

---

## Success Metrics

### Technical

| Metric | Target |
|--------|--------|
| SDK npm weekly downloads | 500+ within 3 months |
| Reference wallet MAU | 100+ testnet users |
| Documentation coverage | 100% of public APIs |
| Test coverage | 80%+ across SDK |

### Ecosystem

| Metric | Target |
|--------|--------|
| Integration examples | 3+ working examples |
| SEP draft | Submitted to stellar/stellar-protocol |
| External integrations | 1+ third-party using SDK |

### Process

| Metric | Target |
|--------|--------|
| Tranche completion | On-time, no extensions |
| Scope changes | Zero unilateral changes |
| Communication | Monthly progress updates |

---

## Risk Mitigation

| Risk | Mitigation |
|------|------------|
| Contractor availability | Existing contact from Tranche 2; backup via hackathon pool |
| Scope creep | Fixed deliverables per tranche; SCF process enforces |
| PasskeyKit changes | Direct relationship with Jane Wang; early roadmap access |
| Timeline slip | Sequential timing after AI Agent Kit; no overlap |

---

## Submission Strategy

### Current AI Agent Kit Status

| Tranche | Status |
|---------|--------|
| Tranche 1 | Approved |
| Tranche 2 | Submitted, in review (~14 business days) |
| Tranche 3 | Work complete, pending T2 approval to submit |

### Timeline

| Action | Timing |
|--------|--------|
| Submit RFP Interest Form | Now |
| T2 approved | ~14 business days |
| T3 submitted | After T2 approval |
| RFP Build Form | Upon invitation |
| Project start | After T3 approval |

### Preempting Reviewer Concerns

| Concern | Response |
|---------|----------|
| "Active SCF project" | Development complete. T2 in review, T3 ready. Admin closeout only. |
| "Is $100K justified?" | Itemized: $35K SDK + $30K wallet + $15K docs + $10K examples + $5K infra |
| "Maintenance?" | 6-month post-launch support + community handoff + contribution guide |
| "Why you?" | Only passkey-kit contributor + SDF endorsement + completed SCF delivery |

---

## Next Steps

1. Initialize repository structure
2. Write technical architecture document
3. Create RFP Interest Form draft
4. Prepare supporting materials (traction evidence, team credentials)
5. Submit Interest Form

---

*Document approved: 2025-02-14*
