# Tranche 2: Wallet + Standards

**Budget:** $36,000
**Focus:** Reference implementation and ecosystem standards
**Duration:** ~8 weeks
**Completion Date:** 15/01/2027

---

## Team

| Role | Person | Allocation |
|------|--------|------------|
| Project Lead | Jose Toscano | Architecture, SEP, SDF coordination |
| Smart Contract & Backend | BlockchainOracle | Python SDK bindings |
| Product & UX | Dan Garcia | Wallet implementation |

---

## Deliverables

### 1. Reference Wallet v1 (Testnet) — $12,000

**Success Criteria:** Deployed to Vercel, user can create wallet, view C-address, and receive test funds

| Task | Status | Notes |
|------|--------|-------|
| Next.js project setup (PWA) | Not started | |
| Landing page | Not started | |
| Passkey registration flow (Face ID, Touch ID, Windows Hello) | Not started | |
| C-address display | Not started | |
| Proxy address generation + display | Not started | |
| Funding flow (receive via proxy) | Not started | |
| Basic send transaction flow | Not started | |
| Testnet deployment to Vercel | Not started | |

**Evidence Required:**
- [ ] Deployed URL (testnet)
- [ ] User flow screenshots
- [ ] Video walkthrough

---

### 2. Token Display + Transaction History — $6,000

**Success Criteria:** Wallet shows token balances and recent transactions on testnet

| Task | Status | Notes |
|------|--------|-------|
| SEP-41 token balance fetching | Not started | |
| Token list display | Not started | |
| Transaction history fetching | Not started | |
| Transaction list display | Not started | |
| Refresh/polling mechanism | Not started | |

**Evidence Required:**
- [ ] Screenshot showing token balances
- [ ] Screenshot showing transaction history

---

### 3. Python SDK Bindings — $7,000

**Success Criteria:** Published on PyPI, example script working

| Task | Status | Notes |
|------|--------|-------|
| Package structure (stellar_c_address) | Not started | |
| Funding module port | Not started | |
| Resolution module port | Not started | |
| Proxy address module port | Not started | |
| Type hints (Pydantic) | Not started | |
| Tests | Not started | |
| PyPI publish | Not started | |

**Evidence Required:**
- [ ] PyPI package link
- [ ] Python example script
- [ ] Test coverage report

---

### 4. Onboarding Standards Documentation — $5,000

**Success Criteria:** Documentation site deployed, covers SDK + wallet + proxy integration

| Task | Status | Notes |
|------|--------|-------|
| Documentation site setup (GitBook/Docusaurus) | Not started | |
| SDK quickstart guide | Not started | |
| Proxy integration patterns | Not started | |
| Wallet integration guide | Not started | |
| UX best practices | Not started | |
| Common pitfalls + troubleshooting | Not started | |

**Evidence Required:**
- [ ] Documentation site link
- [ ] Peer review confirmation

---

### 5. SEP Draft (C-Address Discovery/Funding) — $4,000

**Success Criteria:** PR submitted to stellar/stellar-protocol repository

| Task | Status | Notes |
|------|--------|-------|
| SEP structure following template | Not started | |
| C-address format specification | Not started | |
| Discovery protocol (resolve C-address → contract) | Not started | |
| Proxy address generation standard | Not started | |
| Funding protocol (standard tx format) | Not started | |
| On-ramp integration patterns | Not started | |
| Reference implementation link | Not started | |
| Submit PR | Not started | |

**Evidence Required:**
- [ ] GitHub PR link to stellar/stellar-protocol
- [ ] SEP number (if assigned)

---

### 6. Ecosystem Wallet Feedback — $2,000

**Success Criteria:** Written feedback from at least 2 wallet teams documented in public GitHub issue or discussion, with specific SDK/standards changes traceable to their input

| Task | Status | Notes |
|------|--------|-------|
| Reach out to Lobstr team | Not started | |
| Reach out to xBull team | Not started | |
| Reach out to Freighter team | Not started | |
| Schedule feedback sessions | Not started | |
| Document feedback | Not started | |
| Incorporate feedback into SDK/standards | Not started | |

**Evidence Required:**
- [ ] GitHub issue/discussion with feedback
- [ ] Changes traceable to wallet team input

---

## Checkpoint Checklist

Before submitting Tranche 2 for review:

- [ ] Wallet deploys to Vercel staging
- [ ] Can create passkey wallet and receive C-address
- [ ] Can fund C-address via proxy from external source
- [ ] Token balances and tx history display correctly
- [ ] Can send transaction from wallet
- [ ] Python SDK installs via `pip install stellar-c-address`
- [ ] Documentation site deployed
- [ ] SEP draft PR submitted
- [ ] Feedback from at least 2 wallet teams documented

---

## Submission

**Form Link:** TBD
**Video Link:** TBD
**Submitted:** TBD
**Approved:** TBD
