# C-Address Toolkit: Design Document

**Date:** 2025-02-14
**Updated:** 2025-02-17
**Author:** Jose Carlos Toscano
**Status:** Approved (v2 - with G-to-C Proxy Contract)

---

## Overview

**Project Name:** C-Address Toolkit: Direct Funding & Onboarding Infrastructure

**One-liner:** A production-grade SDK, proxy contract, and reference wallet enabling direct C-address funding without traditional G-addresses, built by a team with proven Smart Wallet expertise.

**Target RFP:** C-Address Tooling & Onboarding (Q1 2026)

**Budget:** $120,000

**Timeline:** 5 months

---

## Positioning

### Why Us

We are a software development company specializing in blockchain infrastructure and developer tooling. Our team has the capacity to manage multiple projects concurrently, with a track record of on-time delivery on prior SCF engagements.

### Authority & Credentials

| Evidence | Detail |
|----------|--------|
| Smart Wallet Experience | Strooper Wallet built at Stellar hackathon 2024 |
| Current SCF Awardee | $108K AI Agent Kit (SCF 37) - fully delivered |
| Hackathon Track Record | 5 hackathons, 3 wins, 1 third place |
| PasskeyKit Contributor | Open source contribution to official repo |

### Key Narrative

> "The C-address funding gap exists because current tooling assumes G-addresses. Through our work on the Stellar AI Agent Kit's policy signer infrastructure, we've already solved adjacent problems. This project applies that expertise to create the missing onboarding layer—including a new proxy contract that bridges the G-address and C-address worlds."

---

## Technical Architecture

### Components

#### 1. G-to-C Proxy Contract (NEW)

Soroban smart contract enabling seamless G-to-C funding.

**How it works:**
1. Contract generates deterministic proxy G-address for each C-address: `proxy_g = hash(c_address + salt)`
2. User shares proxy G-address for CEX withdrawals, on-ramp deposits, etc.
3. When funds arrive at proxy G-address, contract automatically forwards to destination C-address
4. Memo field preserved for compliance/tracking

**Key Features:**
- Deterministic address generation (same C-address always maps to same proxy G-address)
- Automatic forwarding within 1 ledger
- Compatible with any G-address-based system (CEX, on-ramps, legacy wallets)
- Open source (MIT/Apache)

#### 2. C-Address Funding SDK (npm + Python)

Core library for direct C-address funding flows.

```
stellar-c-address-sdk/
├── typescript/          # Primary SDK
│   ├── src/
│   │   ├── funding.ts   # Direct funding + proxy integration
│   │   ├── proxy.ts     # Proxy address generation
│   │   ├── discovery.ts # C-address resolution
│   │   └── passkey.ts   # PasskeyKit integration
│   └── package.json
└── python/              # Python bindings (FastMCP compatible)
    └── stellar_c_address/
```

**Key Features:**
- Generate proxy G-addresses for any C-address
- Fund C-addresses directly from any source (exchange, wallet, fiat on-ramp)
- Resolve C-addresses to underlying contract addresses
- Integrate with PasskeyKit for seamless auth
- TypeScript-first with Python bindings

#### 3. Reference Wallet (Web App)

Production-grade example implementation.

**Tech Stack:**
- Next.js + React (PWA)
- PasskeyKit for authentication (Face ID, Touch ID, Windows Hello)
- Freighter/WalletConnect fallback
- Testnet + Mainnet support

**User Flows:**
1. **Onboard** - Create wallet with passkey, get C-address, no seed phrase
2. **Fund** - Share proxy G-address for CEX/on-ramp deposits, funds arrive at C-address automatically
3. **Transact** - Sign with passkey, execute via smart wallet

**Freighter Parity Features:**
- Display all SEP-41 tokens held
- Transaction history
- Mobile-responsive (installable PWA)

#### 4. Onboarding Standards Documentation

- Developer Guide: Integration tutorials with code examples
- Onboarding Patterns: Best practices for C-address UX
- SEP Draft: Proposed standard for C-address discovery/funding/proxy protocols
- On-ramp Integration Guide: SEP-compliant patterns for fiat on-ramp providers
- Migration Guide: Moving users from G-address to C-address flows

#### 5. Integration Examples

- Exchange integration example (CEX withdrawal → proxy → C-address)
- Fiat on-ramp integration guide (SEP-compliant)
- Existing wallet upgrade path
- AI Agent integration (leverages current toolkit)

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                      C-Address Toolkit                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │   SDK (TS)   │  │ SDK (Python) │  │   Docs/SEP   │          │
│  └──────┬───────┘  └──────┬───────┘  └──────────────┘          │
│         │                 │                                      │
│         └────────┬────────┘                                      │
│                  ▼                                               │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │                   Reference Wallet                         │  │
│  │                    (Next.js PWA)                           │  │
│  └───────────────────────────┬───────────────────────────────┘  │
│                              │                                   │
├──────────────────────────────┼───────────────────────────────────┤
│                              ▼         Stellar Infrastructure    │
│                                                                  │
│  ┌────────────────┐  ┌────────────────┐  ┌──────────────────┐  │
│  │  G-to-C Proxy  │  │   PasskeyKit   │  │   Smart Wallet   │  │
│  │   Contract     │  │                │  │                  │  │
│  │    (NEW)       │  │                │  │                  │  │
│  └───────┬────────┘  └───────┬────────┘  └────────┬─────────┘  │
│          │                   │                    │             │
│          └───────────────────┴────────────────────┘             │
│                              │                                   │
│                              ▼                                   │
│                    ┌──────────────────┐                         │
│                    │ Soroban / Stellar │                         │
│                    │     Network       │                         │
│                    └──────────────────┘                         │
└─────────────────────────────────────────────────────────────────┘

G-to-C Funding Flow:
┌─────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────┐
│   CEX   │────▶│  Proxy G    │────▶│   Proxy     │────▶│ C-Addr  │
│ or Ramp │     │  Address    │     │  Contract   │     │ Wallet  │
└─────────┘     └─────────────┘     └─────────────┘     └─────────┘
```

---

## Budget

**Total: $120,000**

### Tranche Structure

| Tranche | Payment | Amount | Focus |
|---------|---------|--------|-------|
| #0 | 10% | $12,000 | Upon acceptance |
| #1 | 23% | $28,000 | SDK Core + Proxy Contract |
| #2 | 30% | $36,000 | Testnet - Wallet + Docs |
| #3 | 37% | $44,000 | Mainnet - Polish + Launch |

### Budget by Category

| Category | Amount | % |
|----------|--------|---|
| G-to-C Proxy Contract | $18,000 | 15% |
| SDK Development (TS + Python) | $35,000 | 29% |
| Reference Wallet | $32,000 | 27% |
| Documentation + SEP | $15,000 | 13% |
| Integration Examples | $10,000 | 8% |
| Infrastructure + DevOps | $5,000 | 4% |
| Contingency | $5,000 | 4% |

---

## Tranche Deliverables

### Tranche 1: SDK Core + Proxy ($28,000)

| Deliverable | Success Criteria | Budget |
|-------------|------------------|--------|
| C-Address Funding SDK (TypeScript) | npm package published, fund C-address | $8,000 |
| C-Address Resolution Module | Resolve C-address → contract address | $5,000 |
| PasskeyKit Integration | SDK works with existing passkey-kit | $4,000 |
| Unit Test Suite | 80%+ coverage, CI pipeline | $3,000 |
| **G-to-C Proxy Contract** | **Deployed on testnet, auto-forwards funds** | **$8,000** |

### Tranche 2: Wallet + Docs ($36,000)

| Deliverable | Success Criteria | Budget |
|-------------|------------------|--------|
| Reference Wallet v1 (Testnet) | Working PWA with onboard/fund/transact | $12,000 |
| Token Display + History | SEP-41 tokens, transaction history | $6,000 |
| Python SDK Bindings | PyPI package published | $7,000 |
| Onboarding Standards Doc v1 | Developer guide + integration patterns | $5,000 |
| SEP Draft | Submitted to stellar/stellar-protocol | $4,000 |
| Ecosystem Wallet Feedback | 2+ wallet feedback sessions documented | $2,000 |

### Tranche 3: Mainnet Launch ($44,000)

| Deliverable | Success Criteria | Budget |
|-------------|------------------|--------|
| Reference Wallet v2 (Mainnet) | Production deployment + proxy on mainnet | $12,000 |
| G-to-C Funding Flows | CEX→proxy→C-address demo + on-ramp guide | $8,000 |
| Integration Examples (3+) | Exchange, on-ramp guide, AI agent examples | $6,000 |
| SDK v1.0 Stable | npm + PyPI stable releases | $4,000 |
| Developer Portal | Complete docs site with search | $6,000 |
| Multi-Wallet Sign-in | Metamask/Phantom integration (wishlist) | $4,000 |
| Open Source + Handoff | MIT/Apache, contribution guide, runbook | $4,000 |

**Note:** Security audit conducted via SCF-provided audit credits. Proxy contract and SDK undergo third-party review before mainnet.

---

## Timeline

```
Month 1    Month 2    Month 3    Month 4    Month 5
|----------|----------|----------|----------|----------|
[  Tranche 1  ][    Tranche 2    ][     Tranche 3      ]
 SDK + Proxy    Wallet + Docs       Mainnet Launch
```

---

## Differentiation

### Unique Advantages

1. **Professional capacity** - Software development company, not a side project
2. **Completed SCF delivery** - AI Agent Kit fully built, 100% on-time
3. **New smart contract** - Proxy contract directly addresses RFP requirement
4. **Smart Wallet expertise** - Direct experience with PasskeyKit and C-address infrastructure

### Key Differentiators

**Professional Capacity:**
> "As a software development company, we bring professional project management and proven delivery workflows. This isn't a learning exercise—it's our business."

**Smart Contract Deliverable:**
> "The G-to-C Proxy Contract is the key innovation. It creates a bridge between the existing G-address world (CEXs, on-ramps, legacy wallets) and the new C-address world, making adoption frictionless."

**AI Agent Integration (Unique Angle):**
> "The integration examples will include AI agent funding flows—something we can credibly deliver given our MCP toolkit work."

---

## Success Metrics

### Technical

| Metric | Target |
|--------|--------|
| SDK npm weekly downloads | 500+ within 3 months |
| Reference wallet MAU | 100+ testnet users |
| Proxy contract transactions | 50+ successful forwards on testnet |
| Documentation coverage | 100% of public APIs |
| Test coverage | 80%+ across SDK and contract |

### Ecosystem

| Metric | Target |
|--------|--------|
| Integration examples | 3+ working examples |
| SEP draft | Submitted to stellar/stellar-protocol |
| External integrations | 1+ third-party using SDK/proxy |

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
| Proxy contract security | SCF audit credits; testnet validation before mainnet |
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
| "Active SCF project" | All development complete. T2 in review, T3 code ready. Only administrative closeout remains. C-Address Toolkit starts after AI Agent Kit closes—no resource overlap. |
| "Is $120K justified?" | Includes novel smart contract ($18K) + relayer service + full-stack SDK + reference wallet. Itemized per deliverable. Below $149.7K precedent for comparable scope. |
| "Where's the smart contract?" | Deliverable 1.5: G-to-C Proxy Contract + Relayer—core innovation enabling CEX/on-ramp compatibility without requiring those services to change. |
| "How does auto-forwarding work?" | Hybrid architecture: Horizon streaming detects payments, relayer submits forwards signed with contract-derived keys. Open source, self-hostable. |
| "Freighter parity realistic?" | Scoped to token display + history + send/receive. Not a full dApp browser or hardware wallet. Reference implementation, not consumer wallet replacement. |
| "Maintenance?" | 6-month post-launch support + community handoff + contribution guide |
| "Why you?" | Completed SCF delivery ($108K, 100% on-time) + direct Smart Wallet experience + PasskeyKit contributor |

---

## Next Steps

1. ~~Initialize repository structure~~
2. ~~Write technical architecture document~~
3. Create RFP Interest Form draft
4. ~~Prepare supporting materials (traction evidence, team credentials)~~
5. Submit Interest Form

---

*Document approved: 2025-02-14*
*Updated: 2025-02-17 - Added G-to-C Proxy Contract, increased budget to $120K*
*Updated: 2025-02-18 - Added proxy mechanism details, Freighter parity clarification, active project overlap explanation*
