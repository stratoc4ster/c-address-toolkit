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

> **Important Note:** This toolkit is a *pragmatic bridge* for C-address adoption, not a replacement for native CEX support. Key clarifications:
>
> - **This is NOT asking CEXs to change** — The proxy approach works with existing G-address withdrawal systems. No changes required from exchanges or on-ramps.
> - **This is NOT a consumer wallet product** — The reference wallet is a *developer demonstration*, not a Freighter competitor. It shows how to build C-address experiences.
> - **The proxy does NOT store private keys** — Keypairs are derived on-demand within the contract's execution context and discarded after signing. No keys are ever stored or exposed.
> - **This is developer infrastructure** — Like TypeScript binding generators or SDK libraries, this enables other developers to build C-address-first applications.

---

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

**G-to-C Proxy Contract + Relayer:** A two-component system enabling CEX/on-ramp deposits to C-addresses:

1. **Registry Contract (Soroban):** Stores ONLY the mapping `C-address → Proxy G-address`. No secrets, no keys, no signing logic—just a lookup table.

2. **Relayer Service:** Monitors Horizon for payments to registered proxy addresses. When payment detected:
   - Derives the proxy keypair on-demand using HKDF: `keypair = HKDF(master_seed, c_address, "stellar-proxy-v1")`
   - Signs the forwarding transaction
   - Submits to Horizon
   - **Immediately wipes keypair from memory**

**Security Model:**
- **Master seed** held by relayer operator (in HSM for production, or self-managed for self-hosters)
- **No keys stored** in contract or relayer database—derived on-demand, wiped after use
- **Destination locked** in registry—relayer cannot redirect funds
- **Self-hostable**: operators can run their own relayer with their own seed
- **Deterministic**: same C-address always produces same proxy G-address (verifiable)
- **Open source**: full audit trail, anyone can verify the code

See [PROXY_CONTRACT_SPEC.md](../architecture/PROXY_CONTRACT_SPEC.md) for complete technical specification including cryptographic details, threat analysis, and implementation checklist.

**C-Address Funding SDK (TypeScript + Python):** Core library enabling direct C-address funding. Includes proxy address generation, transaction building, address resolution, and Smart Wallet authentication integration. Published on npm and PyPI.

> **Ecosystem Compatibility Note:** We're tracking the December 2025 shift toward OpenZeppelin's smart-account-kit as the recommended smart wallet standard. The SDK v1.0 will integrate with passkey-kit (production-ready, battle-tested). The architecture is designed to support future compatibility with smart-account-kit as that ecosystem matures—the proxy system and funding flows are wallet-agnostic and will work with any C-address regardless of the underlying smart wallet implementation.

**Reference Wallet (Next.js PWA):** Production-grade Progressive Web App demonstrating C-address onboarding and funding flows. Core features at Freighter parity: token balances (SEP-41), transaction history, send/receive flows, passkey auth (Face ID, Touch ID), mobile installable (PWA), testnet + mainnet support.

> **Cross-Device Passkey Strategy:** The reference wallet relies on platform-native passkey synchronization (iCloud Keychain for Apple devices, Google Password Manager for Android/Chrome). This is the standard approach recommended by FIDO Alliance and provides seamless cross-device access within each ecosystem. Limitations: cross-ecosystem access (e.g., iPhone to Windows) requires manual QR-code-based passkey sharing. The onboarding documentation will clearly communicate these platform boundaries and provide guidance for users with multi-platform needs. Future enhancement: support for adding multiple passkey signers to a single C-address (requires smart wallet policy configuration).

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

### Budget Justification

**Team Rates & Allocation:**

| Role | Hourly Rate | Allocation |
|------|-------------|------------|
| Project Lead (Jose) | $85/hr | Architecture, PasskeyKit integration, SDF coordination, code review |
| Backend Engineer (BlockchainOracle) | $75/hr | Soroban contracts, relayer service, SDK implementation |
| Product/UX Engineer (Dan Garcia) | $65/hr | Wallet UI/UX, documentation, integration examples |

**Per-Deliverable Breakdown (Key Items):**

| Deliverable | Hours | Rate | Cost | Justification |
|-------------|-------|------|------|---------------|
| SDK Core (1.1) | 80 hrs | $75 + $85 | $7,000 | 60hr implementation + 20hr architecture/review |
| Proxy Contract + Relayer (1.5) | 90 hrs | $75 | $7,000 | 50hr contract + 40hr relayer service |
| Reference Wallet v1 (2.1) | 160 hrs | $65 + $85 | $12,000 | 120hr UI + 40hr PasskeyKit integration |
| Python SDK (2.3) | 90 hrs | $75 | $7,000 | Port from TypeScript + testing |
| Wallet v2 + Mainnet (3.1) | 180 hrs | $65 + $75 | $14,000 | Production hardening + monitoring |

**Cost Efficiency Evidence:** SCF 37 (AI Agent Kit) delivered 37,693 lines of code in ~2 weeks at similar rates, demonstrating team velocity and cost-effectiveness.

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

## Post-Launch Maintenance Commitment

**6-Month Support Period** (following Tranche 3 completion)

| Issue Type | Response SLA | Resolution Target |
|------------|--------------|-------------------|
| **Security vulnerabilities** | < 24 hours | Patch within 48 hours |
| **Critical bugs** (service down) | < 24 hours | Fix within 72 hours |
| **Major bugs** (feature broken) | < 48 hours | Fix within 1 week |
| **Minor bugs** (cosmetic/edge cases) | < 1 week | Next scheduled release |

**Ongoing Commitments:**
- **Stellar Protocol Updates:** SDK and contracts updated within 2 weeks of any breaking Stellar/Soroban changes
- **Dependency Security:** Monthly audit of npm/PyPI dependencies; critical CVEs patched within 48 hours
- **Community Support:** Daily monitoring of GitHub issues and Discord questions
- **Usage Reporting:** Monthly metrics published (npm downloads, proxy transactions, active wallets)

**Handoff Strategy:**
- Complete maintenance runbook with troubleshooting guides
- Documented on-call procedures for relayer monitoring
- Community contributor onboarding (good-first-issue labels, contributor guide)
- All operational knowledge transferred to public documentation

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
- Key contributor to SCF 37 (Stellar AI Agent Kit) — delivered 37,693 lines of code in ~2 weeks
- **Delivery Evidence (AI Agent Kit):**
  - Stellar MCP Plugin: https://www.youtube.com/watch?v=m3e7y7ToztE
  - Stellar Policy Plugin: https://www.youtube.com/watch?v=O5qa3HGikd4
  - Python Bindings & Full Stack Demo: https://www.youtube.com/watch?v=rEc9aKOnTeE
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
