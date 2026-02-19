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
| Current SCF Awardee | SCF 37 - CLI plugins for Soroban→MCP + Smart Wallet policy signer POC |
| C-Address Experience | 1+ year building with Smart Wallets and C-addresses |
| Smart Wallet Experience | Strooper Wallet (Meridian 2024), PasskeyKit contributor |
| Hackathon Track Record | 5 hackathons, 3 wins, 1 third place |

### Key Narrative

> "The C-address funding gap exists because current tooling assumes G-addresses. Through our work on the Stellar AI Agent Kit's policy signer infrastructure, we've already solved adjacent problems. This project applies that expertise to create the missing onboarding layer—including a new proxy contract that bridges the G-address and C-address worlds."

---

## Technical Architecture

### Components

#### 1. G-to-C Proxy Contract (NEW)

Soroban smart contract enabling seamless G-to-C funding.

**Why Proxy?** CEXs and fiat on-ramps won't add native C-address support in the near term—it requires changes to their withdrawal systems, compliance flows, and address validation logic. The proxy approach enables C-address funding *today* without requiring any changes from external services. Users get a standard G-address they can share anywhere, while funds automatically arrive at their C-address. This is the pragmatic bridge that unlocks Smart Wallet adoption while the ecosystem matures.

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
- Integrate with Smart Wallet authentication (passkeys) for seamless auth
- TypeScript-first with Python bindings

#### 3. Reference Wallet (Web App)

Production-grade example implementation.

**Tech Stack:**
- Next.js + React (PWA)
- Passkey authentication (Face ID, Touch ID, Windows Hello)
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
| #1 | 20% | $24,000 | SDK Core + Proxy Contract |
| #2 | 30% | $36,000 | Testnet - Wallet + Standards |
| #3 | 40% | $48,000 | Mainnet - Launch + Integrations |

---

## Tranche Deliverables

### Tranche 1: SDK Core + Proxy ($24,000)

| Deliverable | Success Criteria | Budget |
|-------------|------------------|--------|
| C-Address Funding SDK (TypeScript) | npm package published, fund C-address | $7,000 |
| C-Address Resolution & Discovery Module | <500ms resolution on testnet | $4,000 |
| Smart Wallet Authentication Layer | Integration tests passing | $3,000 |
| Test Suite + CI/CD | 80%+ coverage, CI pipeline | $3,000 |
| **G-to-C Proxy Contract + Relayer** | **Deployed on testnet, <30s forwarding** | **$7,000** |

### Tranche 2: Wallet + Standards ($36,000)

| Deliverable | Success Criteria | Budget |
|-------------|------------------|--------|
| Reference Wallet v1 (Testnet) | Deployed to Vercel, passkey onboarding | $12,000 |
| Token Display + Transaction History | SEP-41 tokens, tx history visible | $6,000 |
| Python SDK Bindings | PyPI package published | $7,000 |
| Onboarding Standards Documentation | Developer guide deployed | $5,000 |
| SEP Draft (C-Address Discovery/Funding) | PR submitted to stellar/stellar-protocol | $4,000 |
| Ecosystem Wallet Feedback | 2+ wallet teams documented | $2,000 |

### Tranche 3: Mainnet Launch ($48,000)

| Deliverable | Success Criteria | Budget |
|-------------|------------------|--------|
| Reference Wallet v2 (Mainnet) | Production with Sentry + monitoring | $14,000 |
| G-to-C Funding Flows + On-ramp Integration Guide | CEX→proxy→C-address demo on mainnet | $10,000 |
| Integration Examples (3+) | Exchange, on-ramp, AI agent repos | $6,000 |
| SDK v1.0 Stable | v1.0.0 on npm + PyPI | $4,000 |
| Developer Portal + Final Documentation | Complete docs site with search | $6,000 |
| Multi-Wallet Sign-in | Metamask/Phantom/Rabby integration | $4,000 |
| Open Source + Community Handoff | MIT/Apache, CONTRIBUTING.md, runbook | $4,000 |

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
4. **Smart Wallet expertise** - Direct experience with passkey authentication and C-address infrastructure

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
| Contractor availability | Existing contacts; backup via hackathon network |
| Scope creep | Fixed deliverables per tranche; SCF process enforces |
| Smart Wallet API changes | Monitor Stellar developer channels; early roadmap access |
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
| "Why you?" | 1+ year C-address experience, SCF 37 on-time delivery, PasskeyKit contributor |

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
