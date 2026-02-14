# Technical Architecture

## System Overview

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

## Component Details

### 1. C-Address Funding SDK

#### TypeScript Package

**Package:** `@stellar/c-address-sdk`

```
packages/typescript/
├── src/
│   ├── index.ts           # Main exports
│   ├── funding/
│   │   ├── direct.ts      # Direct C-address funding
│   │   ├── validation.ts  # Address validation
│   │   └── types.ts       # Type definitions
│   ├── discovery/
│   │   ├── resolve.ts     # C-address resolution
│   │   └── lookup.ts      # Contract lookup
│   ├── passkey/
│   │   ├── integration.ts # PasskeyKit wrapper
│   │   └── auth.ts        # Authentication flows
│   └── utils/
│       ├── stellar.ts     # Stellar SDK helpers
│       └── errors.ts      # Error handling
├── tests/
│   ├── funding.test.ts
│   ├── discovery.test.ts
│   └── passkey.test.ts
├── package.json
├── tsconfig.json
└── README.md
```

**Core APIs:**

```typescript
// Direct funding
async function fundCAddress(
  cAddress: string,
  amount: string,
  asset: Asset,
  source: Keypair | PasskeyWallet
): Promise<TransactionResult>

// Resolution
async function resolveCAddress(
  cAddress: string
): Promise<ContractInfo>

// Validation
function isValidCAddress(address: string): boolean

// PasskeyKit integration
async function createPasskeyWallet(
  username: string
): Promise<{ cAddress: string; wallet: PasskeyWallet }>
```

#### Python Package

**Package:** `stellar-c-address`

```
packages/python/
├── stellar_c_address/
│   ├── __init__.py
│   ├── funding.py
│   ├── discovery.py
│   ├── passkey.py
│   └── types.py
├── tests/
│   ├── test_funding.py
│   └── test_discovery.py
├── setup.py
└── README.md
```

---

### 2. Reference Wallet

**Tech Stack:**
- Next.js 14 (App Router)
- React 18
- TailwindCSS
- PasskeyKit
- Stellar SDK

```
apps/wallet/
├── app/
│   ├── layout.tsx
│   ├── page.tsx              # Landing
│   ├── onboard/
│   │   └── page.tsx          # Passkey creation
│   ├── wallet/
│   │   ├── page.tsx          # Dashboard
│   │   ├── fund/
│   │   │   └── page.tsx      # Receive funds
│   │   └── send/
│   │       └── page.tsx      # Send funds
│   └── api/
│       └── [...routes]/      # Backend APIs
├── components/
│   ├── PasskeyAuth.tsx
│   ├── WalletCard.tsx
│   ├── FundingQR.tsx
│   └── TransactionList.tsx
├── lib/
│   ├── stellar.ts
│   ├── passkey.ts
│   └── c-address.ts          # Uses SDK
├── public/
├── package.json
└── next.config.js
```

**User Flows:**

1. **Onboarding**
   ```
   Landing → Create Account → Register Passkey → Generate C-Address → Dashboard
   ```

2. **Funding**
   ```
   Dashboard → Fund → Display C-Address + QR → Wait for Deposit → Confirm
   ```

3. **Transacting**
   ```
   Dashboard → Send → Enter Amount/Recipient → Sign with Passkey → Submit → Confirm
   ```

---

### 3. Onboarding Standards

```
docs/standards/
├── developer-guide.md        # Integration tutorials
├── onboarding-patterns.md    # UX best practices
├── sep-draft.md              # Proposed SEP for C-address discovery
└── migration-guide.md        # G-address to C-address migration
```

**SEP Draft Scope:**
- C-address format specification
- Discovery protocol (how to resolve C-address to contract)
- Funding protocol (standard transaction format)
- Metadata standard (wallet info, capabilities)

---

### 4. Integration Examples

```
examples/
├── exchange-integration/
│   ├── README.md
│   ├── deposit-flow.ts       # Simulated exchange deposit
│   └── withdrawal-flow.ts    # Simulated withdrawal to C-address
├── fiat-onramp/
│   ├── README.md
│   └── onramp-flow.ts        # Fiat → C-address flow
├── wallet-upgrade/
│   ├── README.md
│   └── migration.ts          # G-address wallet to C-address
└── ai-agent/
    ├── README.md
    └── agent-funding.ts      # AI agent using MCP to fund C-address
```

---

## Infrastructure

### Development

- **Monorepo:** Turborepo or Nx
- **Package Manager:** pnpm
- **CI/CD:** GitHub Actions
- **Testing:** Vitest (TS), pytest (Python)

### Deployment

| Component | Platform |
|-----------|----------|
| Reference Wallet | Vercel |
| Documentation | Vercel / GitHub Pages |
| SDK (npm) | npmjs.com |
| SDK (Python) | PyPI |

### Environments

| Environment | Purpose |
|-------------|---------|
| Local | Development |
| Testnet | Staging + demos |
| Mainnet | Production (Tranche 3) |

---

## Dependencies

### Core

| Package | Version | Purpose |
|---------|---------|---------|
| @stellar/stellar-sdk | latest | Stellar interactions |
| @stellar/passkey-kit | latest | Passkey authentication |
| next | 14.x | Wallet framework |
| typescript | 5.x | Type safety |

### Development

| Package | Purpose |
|---------|---------|
| vitest | Testing |
| eslint | Linting |
| prettier | Formatting |
| turbo | Monorepo |

---

## Security Considerations

1. **No Private Key Storage** - All signing via PasskeyKit
2. **C-Address Validation** - Strict format checking before operations
3. **Transaction Simulation** - Simulate before submit
4. **Rate Limiting** - API rate limits on wallet backend
5. **Audit Trail** - Log all funding operations

---

## API Design Principles

1. **TypeScript-first** - Full type safety, Python follows
2. **Async/Await** - All network operations async
3. **Error Handling** - Typed errors with actionable messages
4. **Backwards Compatible** - Semver, deprecation warnings
5. **Tree-shakeable** - Modular exports

---

## Performance Targets

| Metric | Target |
|--------|--------|
| SDK Bundle Size | < 50KB gzipped |
| Wallet Initial Load | < 2s |
| Transaction Submit | < 5s |
| C-Address Resolution | < 500ms |
