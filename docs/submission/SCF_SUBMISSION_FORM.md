# SCF Award - Submission Form

## Submission Information

| Field | Value |
|-------|-------|
| **Project** | C-Address Toolkit |
| **Round** | SCF #42 (March 15, 2026) |
| **Build Award Track** | RFP |
| **Submission Title** | C-Address Funding SDK & Proxy Contract |
| **Project Type** | Other Developer Tool |
| **Project URL** | https://github.com/stratoc4ster/c-address-toolkit |
| **Technical Architecture Document** | https://github.com/stratoc4ster/c-address-toolkit/blob/main/docs/plans/2025-02-14-c-address-toolkit-design.md |
| **GitHub URL** | https://github.com/stratoc4ster/c-address-toolkit |
| **Video URL** | *(optional)* |
| **Ambassador Affiliation** | No |
| **Budget** | $120,000 |
| **RFP Track Specifics** | C-Address Tooling & Onboarding |

---

## Products & Services

Current Stellar tooling assumes G-addresses for funding flows. This creates a gap: developers building with Smart Wallets cannot easily enable external deposits to C-addresses from exchanges, fiat on-ramps, or other wallets. CEXs and on-ramps only support G-addresses—they cannot send directly to C-addresses.

**Why Proxy?** CEXs and fiat on-ramps won't add native C-address support in the near term—it requires changes to their withdrawal systems, compliance flows, and address validation logic. The proxy approach enables C-address funding *today* without requiring any changes from external services. Users get a standard G-address they can share anywhere, while funds automatically arrive at their C-address. This is the pragmatic bridge that unlocks Smart Wallet adoption while the ecosystem matures.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         G-to-C Proxy Flow                               │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   CEX / On-ramp         Proxy G-Address           C-Address (Smart Wallet)
│        │                      │                          │              │
│        │   1. Withdraw        │                          │              │
│        │─────────────────────▶│                          │              │
│        │   (standard G-addr)  │                          │              │
│        │                      │                          │              │
│        │                      │  2. Relayer detects      │              │
│        │                      │     (Horizon stream)     │              │
│        │                      │                          │              │
│        │                      │  3. Forward funds        │              │
│        │                      │─────────────────────────▶│              │
│        │                      │  (contract signs tx)     │              │
│        │                      │                          │              │
│   No changes needed     Deterministic            User receives funds    │
│   to CEX/on-ramp        (no key storage)         in < 30 seconds        │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

**G-to-C Proxy Contract + Relayer:** Soroban smart contract that generates deterministic proxy G-addresses for each C-address, plus an open-source relayer service that monitors and forwards funds. User requests proxy via SDK. Contract derives deterministic keypair from hash(c_address + contract_secret_salt), generating a standard G-address any CEX can send to. Relayer monitors proxy addresses via Horizon streaming API, constructs and submits forwarding transactions. **Security:** Proxy keypairs are derived on-demand within the contract's execution context using a contract-controlled secret—no private keys are ever stored or exposed. The relayer submits unsigned transaction templates; signing occurs inside the contract. Destination is cryptographically locked to the original C-address. Stateless, self-hostable, open source.

**C-Address Funding SDK (TypeScript + Python):** Core library enabling direct C-address funding. Includes proxy address generation, transaction building, address resolution, and Smart Wallet authentication integration. Published on npm and PyPI.

**Reference Wallet (Next.js PWA):** Production-grade Progressive Web App demonstrating C-address onboarding and funding flows. Core features at Freighter parity: token balances (SEP-41), transaction history, send/receive flows, passkey auth (Face ID, Touch ID), mobile installable (PWA), testnet + mainnet support.

**Onboarding Standards + SEP Draft:** Developer guide with integration patterns for exchanges and on-ramps, plus a proposed SEP for C-address discovery and funding protocols submitted to stellar/stellar-protocol.

**Integration Examples:** Working code for exchange deposits (via proxy), fiat on-ramp integration guide, and AI agent funding flows.

---

## Traction Evidence

**1+ Year C-Address Experience:** Building with Smart Wallets and C-addresses since their introduction. Firsthand experience with the funding gap this RFP addresses—struggled to enable external deposits, CEX withdrawals, and on-ramp flows to C-addresses. This proposal comes from real pain, not speculation.

**Current SCF Awardee (SCF 37):** Delivered 2 CLI plugins that turn Soroban smart contracts into MCP servers, plus an initial POC for building policies through a CLI plugin for Smart Wallet policy signers—directly working with C-addresses. All tranches delivered on time.

**Smart Wallet Experience:** Built Strooper Wallet at Meridian 2024 (London)—a Telegram miniapp demonstrating passkey-based Smart Wallet onboarding when smart wallets were first introduced. Contributed to the official PasskeyKit repository.

**Hackathon Track Record:** 5 Stellar hackathons, 3 wins including Build Better on Stellar 2024 (EntryX) and 3rd place Build Better 2025 (Soroban to MCP).

**Resource Allocation:** C-Address Toolkit development begins after AI Agent Kit final tranche approval. No timeline overlap.

**Relevant Links:**
- Strooper Wallet: https://www.youtube.com/watch?v=_3oqEzu0hKQ
- EntryX: https://dev.to/josectoscano/entryx-decentralized-ticketing-platform-3ak1
- Soroban to MCP: https://dorahacks.io/buidl/25271

---

## Resubmission Feedback

*(First submission - not applicable)*

---

## RFP Track Deliverables & Budget

**Total Budget:** $120,000

| Tranche | Percentage | Amount |
|---------|------------|--------|
| #0 (Upon approval) | 10% | $12,000 |
| #1 | 20% | $24,000 |
| #2 | 30% | $36,000 |
| #3 (Mainnet launch) | 40% | $48,000 |

---

## Tranche #1 Deliverables — $24,000

**Completion Date:** 15/11/2026

**[Deliverable 1.1] C-Address Funding SDK (TypeScript)**
- Brief description: Core npm package enabling direct C-address funding without G-addresses. Includes transaction building, address validation, and fee estimation.
- How to measure completion: Published on npm, can fund a C-address on testnet from any source.
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
- Brief description: Soroban smart contract for deterministic proxy G-address generation, plus open-source relayer service. Contract derives keypairs from hash(c_address + salt). Relayer monitors proxy addresses via Horizon streaming API and submits forwarding transactions. Stateless, self-hostable architecture.
- How to measure completion: Contract deployed on testnet, relayer running, can receive funds at proxy G-address and verify arrival at C-address within 30 seconds.
- Budget: $7,000

---

## Tranche #2 Deliverables — $36,000

**Completion Date:** 15/01/2027

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

---

## Tranche #3 Deliverables — $48,000

**Completion Date:** 15/03/2027

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

---

## Team

**Jose Carlos Toscano** — Project Lead & Blockchain Engineer
- Over 1 year hands-on experience building with Smart Wallets and C-addresses—directly encountered the funding gap this RFP addresses
- Current SCF Build Award recipient (SCF 37) delivering CLI plugins that turn Soroban contracts into MCP servers, plus a policy-building POC for Smart Wallet policy signers using C-addresses
- PasskeyKit contributor with direct Smart Wallet infrastructure experience
- 5 Stellar hackathons, 3 wins including Build Better on Stellar 2024 (EntryX)
- GitHub: [JoseCToscano](https://github.com/JoseCToscano)
- Telegram: @josectoscano

**BlockchainOracle** — Smart Contract & Backend Engineer
- Blockchain engineer with extensive Stellar and Soroban development experience
- Prior collaborator on SCF-funded projects with proven delivery record
- Telegram: @BlockchainOracle_dev

**Dan Garcia** — Product & UX Engineer
- Product engineer specializing in seamless user experiences for complex blockchain interactions
- Focus on making Web3 onboarding accessible to mainstream users
- LinkedIn: [chl03ks](https://www.linkedin.com/in/chl03ks/)

---

## Legal Acknowledgements

- [x] **Official Rules and Terms** — Acknowledge and Accept
- [x] **Restrictions on Use of Funds** — Acknowledge and Accept
- [x] **Sanctions Compliance** — Acknowledge and Accept
- [x] **Marketing and Publication Rights** — Acknowledge and Accept
- [x] **Disclaimers and Limitations** — Acknowledge and Accept
- [x] **General Conditions** — Acknowledge and Accept

---

*Document generated: February 2026*
