# Tranche Deliverables

## Budget Summary

| Tranche | Focus | Deliverables | Budget | Timeline |
|---------|-------|--------------|--------|----------|
| #0 | Approval | — | $12,000 | Upon approval |
| #1 | SDK Core + Proxy | 5 deliverables | $24,000 | 6 weeks |
| #2 | Wallet + Standards | 6 deliverables | $36,000 | 8 weeks |
| #3 | Mainnet Launch | 7 deliverables | $48,000 | 8 weeks |
| **Total** | | **18 deliverables** | **$120,000** | **~5 months** |

---

## Tranche 1: SDK Core + Proxy Infrastructure — $24,000

**[Deliverable 1.1] C-Address Funding SDK (TypeScript)**
- Brief description: Core npm package enabling direct C-address funding without G-addresses. Includes transaction building, address validation, and fee estimation.
- How to measure completion: Published on npm, can fund a C-address on testnet from any source (G-address, CEX withdrawal via proxy).
- Budget: $7,000

**[Deliverable 1.2] C-Address Resolution & Discovery Module**
- Brief description: Resolve C-addresses to underlying contract addresses. Lookup contract capabilities and supported assets.
- How to measure completion: API documented, resolution working on testnet with <500ms latency.
- Budget: $4,000

**[Deliverable 1.3] Smart Wallet Authentication Layer**
- Brief description: SDK integration with Smart Wallet authentication (passkeys) enabling seamless auth and signing flows.
- How to measure completion: Integration tests passing, example script demonstrating passkey-based funding flow.
- Budget: $3,000

**[Deliverable 1.4] Test Suite + CI/CD**
- Brief description: Unit and integration tests with 80%+ coverage. GitHub Actions CI pipeline.
- How to measure completion: Coverage report, CI badge, all tests passing.
- Budget: $3,000

**[Deliverable 1.5] G-to-C Proxy Contract + Relayer**
- Brief description: Soroban smart contract for deterministic proxy G-address generation, plus open-source relayer service. Contract derives keypairs from `hash(c_address + salt)`. Relayer monitors proxy addresses via Horizon streaming API and submits forwarding transactions signed with contract-derived keys. Stateless, self-hostable architecture.
- How to measure completion: Contract deployed on testnet, relayer running, can receive funds at proxy G-address and verify arrival at C-address within 30 seconds.
- Budget: $7,000

**Tranche 1 Completion Date:** 6 weeks from approval

---

## Tranche 2: Reference Wallet + Standards — $36,000

**[Deliverable 2.1] Reference Wallet v1 (Testnet)**
- Brief description: Next.js PWA with passkey onboarding, C-address display, and basic transaction flows. Mobile-responsive with native passkey support (Face ID, Touch ID, Windows Hello).
- How to measure completion: Deployed to Vercel, user can create wallet, view C-address, and receive test funds.
- Budget: $12,000

**[Deliverable 2.2] Token Display + Transaction History**
- Brief description: Wallet displays all SEP-41 tokens held and transaction history (Freighter parity features).
- How to measure completion: Wallet shows token balances and recent transactions on testnet.
- Budget: $6,000

**[Deliverable 2.3] Python SDK Bindings**
- Brief description: Python package mirroring TypeScript SDK functionality for backend/AI agent use cases.
- How to measure completion: Published on PyPI, example script working.
- Budget: $7,000

**[Deliverable 2.4] Onboarding Standards Documentation**
- Brief description: Developer guide with integration patterns, UX best practices, and common pitfalls.
- How to measure completion: Documentation site deployed, covers SDK + wallet + proxy integration.
- Budget: $5,000

**[Deliverable 2.5] SEP Draft (C-Address Discovery/Funding)**
- Brief description: Proposed standard for C-address discovery and funding protocols, including proxy address generation and on-ramp integration patterns.
- How to measure completion: PR submitted to stellar/stellar-protocol repository.
- Budget: $4,000

**[Deliverable 2.6] Ecosystem Wallet Feedback**
- Brief description: Structured feedback sessions with 2+ existing Stellar wallets (e.g., Lobstr, xBull, Freighter team) on SDK/standards.
- How to measure completion: Written feedback from at least 2 wallet teams documented in public GitHub issue or discussion, with specific SDK/standards changes traceable to their input.
- Budget: $2,000

**Tranche 2 Completion Date:** 8 weeks after Tranche 1

---

## Tranche 3: Mainnet Launch + Integrations — $48,000

**[Deliverable 3.1] Reference Wallet v2 (Mainnet)**
- Brief description: Production deployment with error tracking (Sentry), analytics, and uptime monitoring. Proxy contract deployed to mainnet.
- How to measure completion: Live on mainnet, real transactions working, monitoring dashboard active.
- Budget: $14,000

**[Deliverable 3.2] G-to-C Funding Flows + On-ramp Integration Guide**
- Brief description: End-to-end documentation for funding C-addresses via CEX withdrawal and fiat on-ramps. Includes SEP-compliant integration patterns for on-ramp providers, webhook handling reference, and compliance considerations.
- How to measure completion: Integration guide published, working demo of CEX withdrawal → proxy → C-address flow on mainnet.
- Budget: $10,000

**[Deliverable 3.3] Integration Examples (3+)**
- Brief description: Exchange integration, fiat on-ramp guide, AI agent funding examples with working code.
- How to measure completion: 3 example repos with READMEs and demo videos.
- Budget: $6,000

**[Deliverable 3.4] SDK v1.0 Stable (TypeScript + Python)**
- Brief description: API freeze, semantic versioning, release notes, migration guide from beta.
- How to measure completion: v1.0.0 published on npm and PyPI.
- Budget: $4,000

**[Deliverable 3.5] Developer Portal + Final Documentation**
- Brief description: Complete documentation site with SDK reference, wallet architecture, proxy contract docs, tutorials, FAQ.
- How to measure completion: Site deployed with search, all sections complete.
- Budget: $6,000

**[Deliverable 3.6] Multi-Wallet Sign-in**
- Brief description: Allow sign-in via Metamask, Phantom, or Rabby wallet. Derive Stellar address from external wallet signature and proceed through C-address funding flow.
- How to measure completion: At least one external wallet integration (Metamask, Phantom, or Rabby) working in reference wallet with documented integration guide.
- Budget: $4,000

**[Deliverable 3.7] Open Source + Community Handoff**
- Brief description: All code open source (MIT/Apache), CONTRIBUTING.md, issue templates, maintenance runbook.
- How to measure completion: All repositories MIT/Apache licensed with LICENSE file, CONTRIBUTING.md merged, GitHub issue templates configured, and maintenance runbook published in docs. At least one external contribution (issue or PR) triaged.
- Budget: $4,000

**Tranche 3 Completion Date:** 8 weeks after Tranche 2 (Mainnet launch)

**Note:** Security audit conducted via SCF-provided audit credits as part of Tranche 3 completion. Proxy contract and SDK undergo third-party review before mainnet deployment.

---

## RFP Requirements Coverage

| RFP Requirement | Deliverable |
|-----------------|-------------|
| G-to-C funding solution | 1.1, 1.5, 3.2 |
| CEX withdrawal support | 1.5, 3.2, 3.3 |
| Fiat on-ramp integration | 3.2, 3.3 |
| Viable wallet (Freighter parity) | 2.1, 2.2, 3.1 |
| Onboarding Kit/UX Flow | 2.4, 2.5 |
| Token display + history | 2.2 |
| XLM fee funding | 1.1, 1.5, 3.2 |
| Sign-in with other wallets (wishlist) | 3.6 |
| Web tooling | 2.1, 3.1 |
| Mobile tooling | 2.1 (PWA + React Native SDK compatibility) |
| Input from ecosystem wallets | 2.6 |
| Smart contracts | 1.5 (G-to-C Proxy Contract) |
| SDKs/libraries | 1.1, 1.2, 1.3, 2.3, 3.4 |
| Documentation | 2.4, 2.5, 3.2, 3.5 |
| Test suite | 1.4 |
| Audit fixes | Via SCF audit credits (Tranche 3) |
| Production-ready | 3.1 |
| Example integrations | 3.3 |
| Open source | 3.7 |

---

## Submission Criteria Checklist

| Criterion | How We Address It |
|-----------|-------------------|
| Technically detailed | 18 granular deliverables with specific completion criteria |
| Stellar integration | SDK uses Stellar SDK, new Soroban proxy contract, PasskeyKit |
| Clear use case | Solves C-address funding gap identified in RFP |
| Team capacity | PasskeyKit contributor, current SCF awardee, proven delivery |
| Realistic timeline | 5 months matches prior project velocity |
| Budget aligned | Itemized per deliverable, includes smart contract scope |
| Final tranche = mainnet | Tranche 3 is production deployment |
| Open source plan | Deliverable 3.7 explicitly covers this |
| Ecosystem input | Deliverable 2.6 adds wallet feedback sessions |
| Smart contract deliverable | Deliverable 1.5 (G-to-C Proxy Contract) |
