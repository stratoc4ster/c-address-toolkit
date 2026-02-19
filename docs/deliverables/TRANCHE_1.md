# Tranche 1: SDK Core + Proxy

**Budget:** $24,000
**Focus:** MVP - Core SDK + G-to-C Proxy infrastructure
**Duration:** ~6 weeks
**Completion Date:** 15/11/2026

---

## Team

| Role | Person | Allocation |
|------|--------|------------|
| Project Lead | Jose Toscano | Architecture, PasskeyKit, review |
| Smart Contract & Backend | BlockchainOracle | Proxy contract, relayer, SDK |

---

## Deliverables

### 1. C-Address Funding SDK (TypeScript) — $7,000

**Success Criteria:** Published on npm, can fund a C-address on testnet from any source

| Task | Status | Notes |
|------|--------|-------|
| Package setup (tsconfig, eslint, etc.) | Not started | |
| Core funding module | Not started | |
| Transaction building | Not started | |
| Address validation + fee estimation | Not started | |
| npm publish | Not started | |

**Evidence Required:**
- [ ] npm package link
- [ ] Code repository link
- [ ] Working example script

---

### 2. C-Address Resolution & Discovery Module — $4,000

**Success Criteria:** API documented, resolution working on testnet with <500ms latency

| Task | Status | Notes |
|------|--------|-------|
| Resolution logic | Not started | |
| Contract info lookup | Not started | |
| Supported assets lookup | Not started | |
| API documentation | Not started | |

**Evidence Required:**
- [ ] API documentation
- [ ] Test showing resolution <500ms

---

### 3. Smart Wallet Authentication Layer — $3,000

**Success Criteria:** Integration tests passing, example script demonstrating passkey-based funding flow

| Task | Status | Notes |
|------|--------|-------|
| PasskeyKit wrapper | Not started | |
| Auth flow integration | Not started | |
| Signing integration | Not started | |
| Integration tests | Not started | |

**Evidence Required:**
- [ ] Integration example
- [ ] Passkey-based funding flow demo

---

### 4. Test Suite + CI/CD — $3,000

**Success Criteria:** Coverage report, CI badge, all tests passing

| Task | Status | Notes |
|------|--------|-------|
| Vitest framework setup | Not started | |
| Funding tests | Not started | |
| Resolution tests | Not started | |
| Integration tests | Not started | |
| GitHub Actions CI pipeline | Not started | |

**Evidence Required:**
- [ ] Coverage report (80%+)
- [ ] CI badge/link
- [ ] All tests passing

---

### 5. G-to-C Proxy Contract + Relayer — $7,000

**Success Criteria:** Contract deployed on testnet, relayer running, can receive funds at proxy G-address and verify arrival at C-address within 30 seconds

| Task | Status | Notes |
|------|--------|-------|
| Proxy contract development (Soroban) | Not started | |
| Deterministic keypair derivation | Not started | |
| Relayer service (Horizon streaming) | Not started | |
| Forwarding transaction logic | Not started | |
| Testnet deployment | Not started | |
| Docker setup for self-hosting | Not started | |

**Evidence Required:**
- [ ] Contract deployed on testnet
- [ ] Relayer running and monitoring
- [ ] Demo: funds at proxy G → arrive at C-address <30s
- [ ] Open source repository

---

## Checkpoint Checklist

Before submitting Tranche 1 for review:

- [ ] SDK installs via `npm install @stellar/c-address-sdk`
- [ ] Can resolve a C-address on testnet
- [ ] Can fund a C-address on testnet
- [ ] Proxy contract deployed on testnet
- [ ] Relayer service running, forwarding works
- [ ] All tests pass in CI (80%+ coverage)
- [ ] Documentation reviewed by second person

---

## Submission

**Form Link:** TBD
**Video Link:** TBD
**Submitted:** TBD
**Approved:** TBD
