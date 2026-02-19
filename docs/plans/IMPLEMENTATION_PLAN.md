# C-Address Toolkit: Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Submit a winning SCF Build Award RFP for C-Address Tooling & Onboarding ($120,000) and execute through Meridian 2025.

**Architecture:** Two-phase approach: (1) RFP submission phase to secure funding, (2) Development execution phase to deliver SDK, wallet, and documentation.

**Tech Stack:** TypeScript, Python, Next.js, PasskeyKit, Stellar SDK, Soroban

---

## Project Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         C-ADDRESS TOOLKIT PROJECT                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  PHASE 1: RFP SUBMISSION                 PHASE 2: DEVELOPMENT               │
│  ~~~~~~~~~~~~~~~~~~~~~~~~                ~~~~~~~~~~~~~~~~~~~~~               │
│                                                                              │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌───────┐ │
│  │Interest │→ │ Build   │→ │Approval │→ │Tranche 1│→ │Tranche 2│→ │Tranche│ │
│  │  Form   │  │  Form   │  │         │  │SDK Core │  │Wallet+  │  │   3   │ │
│  │         │  │         │  │         │  │         │  │  Docs   │  │Mainnet│ │
│  └─────────┘  └─────────┘  └─────────┘  └─────────┘  └─────────┘  └───────┘ │
│       │            │            │            │            │            │     │
│       ▼            ▼            ▼            ▼            ▼            ▼     │
│    Week 1       Week 3       Week 6      Month 2      Month 4      Month 5  │
│                                                                    ↓        │
│                                                              MERIDIAN 2025  │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Milestones

| ID | Milestone | Target Date | Deliverable | Success Criteria |
|----|-----------|-------------|-------------|------------------|
| M1 | Interest Form Submitted | Week 1 | SCF Interest Form | Confirmation email received |
| M2 | Build Form Submitted | Week 3 | Full proposal | SCF acknowledgment |
| M3 | RFP Approved | Week 6 | Award letter | Tranche 0 ($10K) received |
| M4 | Tranche 1 Complete | Month 2 | SDK Core | npm package live, T1 approved |
| M5 | Tranche 2 Complete | Month 4 | Wallet + Docs | Testnet wallet live, T2 approved |
| M6 | Tranche 3 Complete | Month 5 | Mainnet Launch | Production live, T3 approved |
| M7 | Meridian 2025 | Month 5+ | Presentation | Demo delivered |

---

## Phase 1: RFP Submission

### Timeline

```
Week 1              Week 2              Week 3              Week 4-6
|-------------------|-------------------|-------------------|-------------------|
[Interest Form Prep ][Interest Form Sub ][Build Form Prep   ][Build Form + Review]
     & Submit            & Wait              & Draft              & Approval
```

---

### Task 1: Prepare Interest Form Content

**Files:**
- Create: `docs/submission/INTEREST_FORM_DRAFT.md`

**Step 1: Draft Interest Form responses**

Create the Interest Form draft with all required fields:

```markdown
# SCF Interest Form Draft

## Basic Information
- Project Name: C-Address Toolkit
- Track: RFP Track
- RFP: C-Address Tooling & Onboarding (Q1 2026)

## Applicant
- Name: Jose Carlos Toscano
- Email: toscano0210@gmail.com
- Organization: [Your Company Name]
- Role: Founder / Lead Developer

## Project Summary (150 words max)
[Draft summary emphasizing: direct C-address funding, reference wallet,
passkey-kit expertise, completed SCF 37 delivery]

## Relevant Experience
[List: PasskeyKit contributor, SCF 37 awardee, hackathon wins,
Strooper Wallet, npm traction]

## Why This RFP
[Explain fit: adjacent tooling already built, SDF relationships,
unique positioning]
```

**Step 2: Review against RFP requirements**

Cross-check draft against `08-rfp-track.md` requirements:
- [ ] Addresses active RFP
- [ ] Shows relevant experience
- [ ] Demonstrates technical capability

**Step 3: Commit**

```bash
git add docs/submission/INTEREST_FORM_DRAFT.md
git commit -m "docs: draft Interest Form content for SCF RFP"
```

---

### Task 2: Gather Traction Evidence

**Files:**
- Create: `docs/submission/TRACTION_EVIDENCE.md`

**Step 1: Compile evidence document**

```markdown
# Traction & Credentials Evidence

## SCF Track Record
- SCF 37: Stellar AI Agent Kit ($108K) - APPROVED
  - Tranche 1: Approved
  - Tranche 2: Submitted [DATE]
  - Tranche 3: Development complete
  - Link: https://communityfund.stellar.org/dashboard/submissions/recmbfFnpx6eVThqw

## Open Source Contributions
- PasskeyKit: [Link to contribution PR]
- Stellar CLI PR #1985: [Link]

## npm Traction
- 4 packages published
- 2,047 downloads (7 weeks)
- 837 peak weekly downloads
- Links: [package URLs]

## Hackathon Record
| Event | Date | Project | Result |
|-------|------|---------|--------|
| Build Better on Stellar | Summer 2024 | EntryX | WINNER |
| HackMeridian 2024 | Oct 2024 | Strooper Wallet | Participated |
| Freelii Hackathon | 2024 | [Project] | WINNER |
| X Video Challenge | 2024 | Tap to Crypto | WINNER |
| Build Better 2025 | 2025 | Soroban to MCP | 3RD PLACE |

## SDF Endorsements
- Nick Gilbert (VP Product): "key infrastructure"
- John Canneto: Project champion, internal advocacy
- Jane Wang: CLI plugin collaboration

## Contractor Delivery Proof
- Tranche 2: 37,693 lines delivered
- 4 PRs merged in ~2 weeks
- Video demos recorded
```

**Step 2: Collect screenshot/links**

- [ ] npm package pages
- [ ] GitHub contribution graph
- [ ] SCF submission pages
- [ ] Hackathon winner announcements

**Step 3: Commit**

```bash
git add docs/submission/TRACTION_EVIDENCE.md
git commit -m "docs: compile traction evidence for RFP submission"
```

---

### Task 3: Submit Interest Form

**Files:**
- Update: `docs/submission/INTEREST_FORM_DRAFT.md` → mark as submitted

**Step 1: Navigate to SCF portal**

URL: https://communityfund.stellar.org

**Step 2: Complete Interest Form**

Copy content from `INTEREST_FORM_DRAFT.md` into form fields.

**Step 3: Submit and record confirmation**

```markdown
# Interest Form Submission Record

- Submitted: [DATE]
- Confirmation: [Screenshot or email]
- Expected response: 1-2 weeks
```

**Step 4: Commit**

```bash
git add docs/submission/
git commit -m "docs: Interest Form submitted to SCF"
```

---

### Task 4: Draft Build Form - Project Description

**Files:**
- Create: `docs/submission/BUILD_FORM_DRAFT.md`

**Step 1: Draft project description section**

```markdown
# SCF Build Form Draft

## Project Title
C-Address Toolkit: Direct Funding & Onboarding Infrastructure

## Executive Summary (250 words)

The C-Address Toolkit addresses a critical gap in Stellar's onboarding
infrastructure: the inability to fund C-addresses (smart wallet addresses)
directly without first creating a traditional G-address.

As the owner of a software development company and a passkey-kit contributor,
I've already built adjacent infrastructure through the Stellar AI Agent Kit
(SCF 37, $108K - fully delivered). This project applies that expertise to
create the missing onboarding layer.

**Deliverables:**
1. C-Address Funding SDK (TypeScript + Python)
2. Reference Wallet (Next.js web app)
3. Onboarding Standards Documentation + SEP Draft
4. Integration Examples (exchange, fiat on-ramp, AI agent)

**Why Me:**
- Only passkey-kit contributor applying for this RFP
- Proven SCF delivery: AI Agent Kit complete, in approval queue
- Software company with established contractor model (37K lines in 2 weeks)
- SDF endorsement: Nick Gilbert called my work "key infrastructure"

**Budget:** $120,000 over 5 months
**Target:** Meridian 2025

## Problem Statement

Current Stellar tooling assumes users start with G-addresses. This creates
friction for smart wallet adoption:
- Users must understand traditional keypairs before using passkeys
- Exchanges can't deposit directly to C-addresses
- Fiat on-ramps require G-address intermediaries
- Developer tooling lacks C-address-first patterns

## Solution

A comprehensive toolkit enabling direct C-address funding flows, demonstrated
through a production-grade reference wallet, and documented through
ecosystem standards.
```

**Step 2: Commit**

```bash
git add docs/submission/BUILD_FORM_DRAFT.md
git commit -m "docs: draft Build Form project description"
```

---

### Task 5: Draft Build Form - Technical Architecture

**Files:**
- Update: `docs/submission/BUILD_FORM_DRAFT.md`

**Step 1: Add technical architecture section**

```markdown
## Technical Architecture

### System Diagram

[Include ASCII diagram from ARCHITECTURE.md]

### Component Specifications

#### 1. C-Address Funding SDK

**Technology:** TypeScript (primary), Python (bindings)
**Distribution:** npm (@stellar/c-address-sdk), PyPI (stellar-c-address)

**Core Modules:**
- `funding`: Direct C-address funding without G-address intermediary
- `discovery`: C-address resolution to contract address
- `passkey`: PasskeyKit integration for seamless auth

**API Example:**
```typescript
import { fundCAddress, createPasskeyWallet } from '@stellar/c-address-sdk';

// Create wallet with passkey
const { cAddress, wallet } = await createPasskeyWallet('user@example.com');

// Fund directly from any source
await fundCAddress(cAddress, '100', Asset.native(), sourceKeypair);
```

#### 2. Reference Wallet

**Technology:** Next.js 14, React 18, TailwindCSS
**Authentication:** PasskeyKit (WebAuthn)
**Deployment:** Vercel (testnet + mainnet)

**User Flows:**
1. Onboard: Landing → Create Account → Register Passkey → Get C-Address
2. Fund: Display C-Address → QR Code → Wait for Deposit → Confirm
3. Transact: Enter Amount → Sign with Passkey → Submit → Confirm

#### 3. Standards Documentation

**Deliverables:**
- Developer Guide: Integration tutorials
- Onboarding Patterns: UX best practices
- SEP Draft: C-address discovery/funding protocol
- Migration Guide: G-address to C-address upgrade path

#### 4. Integration Examples

**Exchange Integration:** Simulated deposit/withdrawal to C-address
**Fiat On-ramp:** Direct fiat → C-address flow
**AI Agent:** MCP-based agent funding (leverages AI Agent Kit)

### Security Considerations

- No private key storage (PasskeyKit handles all signing)
- Transaction simulation before submission
- C-address validation at all entry points
- Rate limiting on wallet backend APIs
```

**Step 2: Commit**

```bash
git add docs/submission/BUILD_FORM_DRAFT.md
git commit -m "docs: add technical architecture to Build Form"
```

---

### Task 6: Draft Build Form - Budget Breakdown

**Files:**
- Update: `docs/submission/BUILD_FORM_DRAFT.md`

**Step 1: Add itemized budget section**

```markdown
## Budget Breakdown

**Total Request:** $120,000

### By Tranche

| Tranche | Amount | Focus |
|---------|--------|-------|
| #0 (Kickoff) | $12,000 | Upon acceptance |
| #1 (MVP) | $24,000 | SDK Core |
| #2 (Testnet) | $36,000 | Wallet + Docs |
| #3 (Mainnet) | $48,000 | Production Launch |

### By Category

| Category | Amount | % | Justification |
|----------|--------|---|---------------|
| SDK Development | $42,000 | 35% | TypeScript + Python, tests, CI/CD |
| Reference Wallet | $36,000 | 30% | Next.js app, PasskeyKit integration |
| Documentation | $18,000 | 15% | Developer guide, SEP draft, tutorials |
| Integration Examples | $12,000 | 10% | 3 working examples with docs |
| Infrastructure | $6,000 | 5% | Vercel, monitoring, DevOps |
| Contingency | $6,000 | 5% | Buffer for unforeseen issues |

### Team Allocation

| Role | Rate Estimate | Allocation |
|------|---------------|------------|
| Project Lead (Jose) | Included | Architecture, review, SDF coordination |
| SDK Contractor | ~$75/hr | SDK implementation, tests |
| Frontend Contractor | ~$65/hr | Wallet implementation |

### Cost Efficiency Evidence

Previous project (AI Agent Kit Tranche 2):
- Contractor delivered 37,693 lines of code
- 4 merged PRs in ~2 weeks
- Total contractor cost: ~$15,000
- Result: On-time, quality delivery

This budget scales that proven model to a larger scope.
```

**Step 2: Commit**

```bash
git add docs/submission/BUILD_FORM_DRAFT.md
git commit -m "docs: add budget breakdown to Build Form"
```

---

### Task 7: Draft Build Form - Tranche Deliverables

**Files:**
- Update: `docs/submission/BUILD_FORM_DRAFT.md`

**Step 1: Add per-tranche deliverables**

```markdown
## Tranche Deliverables

### Tranche 1: SDK Core ($24,000)

**Duration:** 6 weeks
**Focus:** Core SDK functionality

| Deliverable | Success Criteria |
|-------------|------------------|
| C-Address Funding SDK (TypeScript) | npm package published, funds C-address without G-address |
| C-Address Resolution Module | Resolves C-address → contract address |
| PasskeyKit Integration | SDK works with existing passkey-kit |
| Unit Test Suite | 80%+ code coverage |
| Basic Documentation | README + API reference |

**Verification:**
- npm package installable and functional
- Demo script showing C-address funding on testnet
- CI pipeline with passing tests

---

### Tranche 2: Wallet + Docs ($36,000)

**Duration:** 8 weeks
**Focus:** Reference implementation and standards

| Deliverable | Success Criteria |
|-------------|------------------|
| Reference Wallet v1 (Testnet) | Working onboard/fund/transact flows |
| Python SDK Bindings | PyPI package published |
| Onboarding Standards Doc v1 | Developer guide + integration patterns |
| SEP Draft | Submitted to stellar/stellar-protocol |
| Demo Video | End-to-end flow recorded (3-5 min) |

**Verification:**
- Wallet accessible at public URL
- Python SDK installable via pip
- SEP PR visible in GitHub
- Demo video on YouTube

---

### Tranche 3: Mainnet Launch ($48,000)

**Duration:** 8 weeks
**Focus:** Production deployment and ecosystem adoption

| Deliverable | Success Criteria |
|-------------|------------------|
| Reference Wallet v2 (Mainnet) | Production deployment with monitoring |
| Integration Examples (3+) | Exchange, fiat on-ramp, AI agent |
| Final Documentation | Complete developer portal |
| SDK v1.0 Stable | TypeScript + Python stable releases |
| Migration Guide | G-address → C-address upgrade path |
| Community Handoff Plan | Contribution guide, maintenance docs |

**Verification:**
- Wallet processing real transactions on mainnet
- All 3 integration examples have working code + README
- Documentation site fully deployed
- v1.0.0 tags on npm and PyPI

---

## Timeline

| Month | Phase | Key Milestone |
|-------|-------|---------------|
| 1 | Tranche 1 | SDK published to npm |
| 2 | Tranche 1 | T1 submitted for review |
| 3 | Tranche 2 | Wallet live on testnet |
| 4 | Tranche 2 | SEP draft submitted, T2 review |
| 5 | Tranche 3 | Mainnet launch |
| 5+ | Post-launch | Meridian 2025 presentation |
```

**Step 2: Commit**

```bash
git add docs/submission/BUILD_FORM_DRAFT.md
git commit -m "docs: add tranche deliverables to Build Form"
```

---

### Task 8: Draft Build Form - Team & Maintenance

**Files:**
- Update: `docs/submission/BUILD_FORM_DRAFT.md`

**Step 1: Add team and maintenance sections**

```markdown
## Team

### Project Lead: Jose Carlos Toscano

**Role:** Architecture, PasskeyKit integration, SDF coordination, contractor management

**Credentials:**
- Owner, [Company Name] - Software development company
- PasskeyKit contributor (open source)
- SCF 37 Awardee: Stellar AI Agent Kit ($108K) - fully delivered
- 5 Stellar hackathons: 3 wins, 1 third place
- Strooper Wallet: Smart wallet at HackMeridian 2024

**Relevant Experience:**
- Policy Signer Generator: Similar CLI tooling for smart wallets
- MCP Server Generator: Developer tooling pattern
- Passkey integration: Production experience with WebAuthn

### Contractors (To Be Hired)

**SDK Developer:**
- TypeScript + Rust experience
- Stellar SDK familiarity
- Source: Stellar hackathon participant pool (proven method)

**Frontend Developer:**
- React/Next.js production experience
- Web3 wallet UX background
- Source: Professional network

### Execution Model

Proven contractor model from AI Agent Kit:
- Clear specifications provided upfront
- Daily async communication
- Code review on all PRs
- Demo videos for each feature
- Result: 37K lines delivered in ~2 weeks

---

## Maintenance Plan

### Post-Launch Support (6 months)

| Commitment | SLA |
|------------|-----|
| Bug fixes | 48 hours |
| Security patches | 24 hours |
| Community questions | Daily Discord check |
| Usage reports | Monthly |

### Community Handoff

**Documentation:**
- Contribution guide (CONTRIBUTING.md)
- Architecture decision records
- Maintenance runbook

**Process:**
- Issue templates for bugs/features
- PR templates with checklist
- CI/CD fully automated

### Long-term Sustainability

- SDK designed for minimal maintenance (stable Stellar SDK dependency)
- Reference wallet can be forked/self-hosted
- Documentation static site (low maintenance)
- SEP standard reduces need for custom solutions
```

**Step 2: Commit**

```bash
git add docs/submission/BUILD_FORM_DRAFT.md
git commit -m "docs: add team and maintenance plan to Build Form"
```

---

### Task 9: Final Build Form Review

**Files:**
- Update: `docs/submission/BUILD_FORM_DRAFT.md`

**Step 1: Cross-check against submission criteria**

Review against `03-submission-criteria.md`:

- [ ] Clear Stellar integration rationale
- [ ] Complete technical architecture
- [ ] Verifiable traction or validated need
- [ ] Three well-defined tranches
- [ ] Realistic, itemized budget
- [ ] Experienced team with relevant skills
- [ ] Measurable success metrics
- [ ] Unique value proposition
- [ ] Open-source plan
- [ ] No conflicting SDF funding

**Step 2: Cross-check against RFP requirements**

Review against `08-rfp-track.md`:

- [ ] Addresses active RFP (C-Address Tooling)
- [ ] Technical design included
- [ ] Relevant experience demonstrated
- [ ] Testable milestones defined
- [ ] Maintenance plan included

**Step 3: Proofread and finalize**

- [ ] No typos or grammatical errors
- [ ] All links working
- [ ] Budget numbers consistent
- [ ] Timeline realistic

**Step 4: Commit**

```bash
git add docs/submission/BUILD_FORM_DRAFT.md
git commit -m "docs: finalize Build Form for submission"
```

---

### Task 10: Submit Build Form

**Files:**
- Create: `docs/submission/SUBMISSION_RECORD.md`

**Step 1: Navigate to SCF portal**

URL: https://communityfund.stellar.org (use invitation link)

**Step 2: Complete Build Form**

Copy content from `BUILD_FORM_DRAFT.md` into form fields.

**Step 3: Upload supporting documents**

- Architecture diagram (export as image)
- Traction evidence (if attachments allowed)

**Step 4: Submit and record**

```markdown
# Build Form Submission Record

- Interest Form Submitted: [DATE]
- Build Form Invitation Received: [DATE]
- Build Form Submitted: [DATE]
- Confirmation: [Screenshot or email]
- Expected Review: 2-4 weeks
- Revision Window: [If applicable]
- Final Decision: [Pending]
```

**Step 5: Commit**

```bash
git add docs/submission/SUBMISSION_RECORD.md
git commit -m "docs: Build Form submitted to SCF"
```

---

## Phase 2: Development Execution

### Overview

Phase 2 begins upon RFP approval and Tranche 0 receipt.

```
APPROVAL        MONTH 1-2           MONTH 3-4           MONTH 5
    |               |                   |                   |
    ▼               ▼                   ▼                   ▼
[Tranche 0]    [Tranche 1]         [Tranche 2]         [Tranche 3]
   $10K           $20K                $30K                $40K
    |               |                   |                   |
    ▼               ▼                   ▼                   ▼
 Kickoff        SDK Core          Wallet + Docs        Mainnet
                                                           |
                                                           ▼
                                                    MERIDIAN 2025
```

---

### Task 11: Project Kickoff (Upon Approval)

**Files:**
- Create: `packages/` directory structure
- Create: `apps/` directory structure
- Update: `README.md` with development status

**Step 1: Set up monorepo structure**

```bash
# Create package structure
mkdir -p packages/typescript/src
mkdir -p packages/python/stellar_c_address
mkdir -p apps/wallet
mkdir -p examples/{exchange,fiat-onramp,ai-agent}

# Initialize package managers
cd packages/typescript && npm init -y
cd ../python && touch setup.py
cd ../../apps/wallet && npx create-next-app@latest . --typescript --tailwind --app
```

**Step 2: Hire contractors**

- [ ] Post SDK developer role
- [ ] Review hackathon participant pool
- [ ] Interview candidates
- [ ] Onboard selected contractor(s)

**Step 3: Commit**

```bash
git add .
git commit -m "chore: initialize monorepo structure for development"
```

---

### Task 12-16: Tranche 1 Execution

See `docs/deliverables/TRANCHE_1.md` for detailed task breakdown.

**Summary:**
1. SDK package setup
2. C-Address resolution module (TDD)
3. Direct funding module (TDD)
4. PasskeyKit integration (TDD)
5. Documentation + npm publish

---

### Task 17-22: Tranche 2 Execution

See `docs/deliverables/TRANCHE_2.md` for detailed task breakdown.

**Summary:**
1. Python bindings
2. Wallet UI scaffolding
3. Onboarding flow
4. Funding flow
5. Standards documentation
6. SEP draft submission

---

### Task 23-28: Tranche 3 Execution

See `docs/deliverables/TRANCHE_3.md` for detailed task breakdown.

**Summary:**
1. Mainnet preparation
2. Integration examples (3)
3. Final documentation
4. SDK v1.0 release
5. Community handoff
6. Meridian preparation

---

## Appendix A: Risk Register

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| RFP rejected | Low | High | Strong application, SDF relationships, proven track record |
| Contractor unavailable | Medium | Medium | Backup candidates from hackathon pool |
| PasskeyKit breaking change | Low | Medium | Direct relationship with Jane Wang |
| Scope creep | Medium | Medium | Fixed deliverables, SCF process enforcement |
| Timeline slip | Low | Low | Sequential timing, buffer in estimates |

---

## Appendix B: Communication Plan

| Stakeholder | Channel | Frequency |
|-------------|---------|-----------|
| SCF Team | Portal / Email | Per tranche submission |
| SDF Product (John, Jane) | Email / Discord | Monthly update |
| Contractors | Slack / GitHub | Daily async |
| Community | Discord / Twitter | Major milestones |

---

## Appendix C: Success Metrics Summary

| Category | Metric | Target |
|----------|--------|--------|
| Funding | RFP Approved | $120K awarded |
| SDK | npm downloads | 500+/week by M+3 |
| Wallet | Testnet users | 100+ MAU |
| Standards | SEP status | Draft submitted |
| Timeline | Tranche completion | On-time, no extensions |
| Ecosystem | Third-party integrations | 1+ by Tranche 3 |
