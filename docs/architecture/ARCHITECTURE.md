# Technical Architecture

## System Overview

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                            C-Address Toolkit                                  │
├──────────────────────────────────────────────────────────────────────────────┤
│                                                                               │
│   ┌──────────────┐    ┌──────────────┐    ┌───────────┐    ┌─────────────┐  │
│   │   SDK (TS)   │    │ SDK (Python) │    │  Docs/SEP │    │  Relayer    │  │
│   └──────┬───────┘    └──────┬───────┘    └───────────┘    │  Service    │  │
│          │                   │                              └──────┬──────┘  │
│          └─────────┬─────────┘                                     │         │
│                    ▼                                               │         │
│          ┌─────────────────┐                                       │         │
│          │ Reference Wallet │                                      │         │
│          │   (Next.js PWA)  │                                      │         │
│          └────────┬────────┘                                       │         │
│                   │                                                │         │
├───────────────────┼────────────────────────────────────────────────┼─────────┤
│                   ▼              Stellar Infrastructure            ▼         │
│                                                                               │
│   ┌────────────┐  ┌────────────┐  ┌───────────────┐  ┌──────────────────┐   │
│   │ PasskeyKit │  │Smart Wallet│  │ G-to-C Proxy  │  │ Horizon Streaming│   │
│   │            │  │            │  │   Contract    │  │       API        │   │
│   └─────┬──────┘  └─────┬──────┘  └───────┬───────┘  └────────┬─────────┘   │
│         │               │                 │                    │             │
│         └───────────────┴─────────────────┴────────────────────┘             │
│                                   │                                          │
│                                   ▼                                          │
│                         ┌──────────────────┐                                 │
│                         │  Soroban/Stellar │                                 │
│                         │     Network      │                                 │
│                         └──────────────────┘                                 │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## G-to-C Proxy Flow

The proxy system enables CEXs, fiat on-ramps, and legacy wallets to fund C-addresses without any changes on their end.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              G-to-C Proxy Flow                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│    ┌─────────────┐                                                          │
│    │    User     │                                                          │
│    │  (C-Addr)   │                                                          │
│    └──────┬──────┘                                                          │
│           │ 1. Request proxy address                                        │
│           ▼                                                                 │
│    ┌─────────────┐      ┌─────────────────────────────────────────────┐    │
│    │     SDK     │─────▶│  Proxy Contract                             │    │
│    └─────────────┘      │  ┌─────────────────────────────────────┐   │    │
│                         │  │ proxy_g = hash(c_address + secret)  │   │    │
│                         │  └─────────────────────────────────────┘   │    │
│                         └──────────────────────┬──────────────────────┘    │
│                                                │                            │
│           ┌────────────────────────────────────┘                            │
│           │ 2. Returns deterministic G-address                              │
│           ▼                                                                 │
│    ┌─────────────┐                                                          │
│    │  Proxy G    │◀──── User shares this address                            │
│    │  Address    │      (looks like normal G-address)                       │
│    └──────┬──────┘                                                          │
│           │                                                                 │
│           │ 3. CEX/On-ramp sends funds                                      │
│           │    (standard Stellar payment)                                   │
│           ▼                                                                 │
│    ┌─────────────┐      ┌─────────────────────────────────────────────┐    │
│    │   Horizon   │─────▶│  Relayer Service                            │    │
│    │  Streaming  │      │  • Monitors proxy addresses                 │    │
│    │     API     │      │  • Detects incoming payments                │    │
│    └─────────────┘      │  • Stateless (no key storage)               │    │
│                         └──────────────────────┬──────────────────────┘    │
│                                                │                            │
│           ┌────────────────────────────────────┘                            │
│           │ 4. Relayer submits forward request                              │
│           ▼                                                                 │
│    ┌─────────────────────────────────────────────────────────────────┐     │
│    │  Proxy Contract                                                  │     │
│    │  ┌───────────────────────────────────────────────────────────┐  │     │
│    │  │ • Derives keypair on-demand (no storage)                  │  │     │
│    │  │ • Signs forwarding transaction inside contract context    │  │     │
│    │  │ • Destination locked to original C-address                │  │     │
│    │  └───────────────────────────────────────────────────────────┘  │     │
│    └──────────────────────────────┬──────────────────────────────────┘     │
│                                   │                                         │
│           ┌───────────────────────┘                                         │
│           │ 5. Funds arrive at C-address                                    │
│           │    (< 30 seconds end-to-end)                                    │
│           ▼                                                                 │
│    ┌─────────────┐                                                          │
│    │  C-Address  │                                                          │
│    │   Wallet    │                                                          │
│    └─────────────┘                                                          │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Proxy Security Model

> **See also:** [PROXY_CONTRACT_SPEC.md](./PROXY_CONTRACT_SPEC.md) for complete technical specification including cryptographic details and threat analysis.

### Architecture Clarification

The proxy system uses **deterministic key derivation** with a **stateless relayer**:

1. **Registry Contract (Soroban):** Stores ONLY the mapping `C-address → Proxy G-address`. No secrets stored.
2. **Master Seed:** A 32-byte secret held by relayer operators (in HSM for production, or self-managed).
3. **Key Derivation:** `proxy_keypair = HKDF(master_seed, c_address, "stellar-proxy-v1")`
4. **Relayer:** Monitors Horizon, derives keys on-demand, signs forwards, wipes keys from memory.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           Security Architecture                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   WHAT'S STORED                         WHAT'S DERIVED ON-DEMAND            │
│   ──────────────                        ────────────────────────            │
│                                                                             │
│   ┌─────────────────┐                   ┌─────────────────────────────┐    │
│   │ Registry        │                   │     At Forward Time          │    │
│   │ (Soroban)       │                   │                              │    │
│   │ ┌─────────────┐ │                   │  Master Seed (held by        │    │
│   │ │  C-Address  │ │                   │  relayer operator)           │    │
│   │ │  → Proxy G  │ │                   │         │                    │    │
│   │ │   Mapping   │ │                   │         ▼                    │    │
│   │ └─────────────┘ │                   │  ┌───────────────────────┐  │    │
│   │                 │                   │  │  proxy_keypair =      │  │    │
│   │  NO secrets     │    derives ───▶   │  │  HKDF(seed, c_addr)   │  │    │
│   │  stored here    │                   │  └───────────────────────┘  │    │
│   └─────────────────┘                   │         │                    │    │
│                                         │         ▼                    │    │
│   ┌─────────────────┐                   │  ┌───────────────────────┐  │    │
│   │ Relayer         │                   │  │  Sign forward tx      │  │    │
│   │ ┌─────────────┐ │                   │  │  Submit to Horizon    │  │    │
│   │ │ Master Seed │ │                   │  │  WIPE key from memory │  │    │
│   │ │ (in memory  │ │                   │  └───────────────────────┘  │    │
│   │ │  or HSM)    │ │                   │                              │    │
│   │ └─────────────┘ │                   └─────────────────────────────┘    │
│   └─────────────────┘                                                       │
│                                                                             │
│   ┌─────────────────────────────────────────────────────────────────────┐  │
│   │                        Security Properties                           │  │
│   ├─────────────────────────────────────────────────────────────────────┤  │
│   │  ✓ No private keys stored (derived on-demand, wiped after use)      │  │
│   │  ✓ Relayer cannot redirect funds (destination locked in registry)   │  │
│   │  ✓ Deterministic: same C-address always → same proxy G-address      │  │
│   │  ✓ Open source: anyone can run their own relayer with their seed    │  │
│   │  ✓ Funds forwarded within ~30 seconds end-to-end                    │  │
│   │  ✓ Self-hostable: full control for operators who want it            │  │
│   └─────────────────────────────────────────────────────────────────────┘  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
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

### 2. G-to-C Proxy Contract + Relayer

#### Proxy Contract (Soroban)

```
contracts/proxy/
├── src/
│   ├── lib.rs              # Contract entry point
│   ├── proxy.rs            # Proxy address generation
│   ├── forward.rs          # Forwarding logic
│   └── types.rs            # Data structures
├── tests/
│   └── integration.rs      # E2E tests
└── Cargo.toml
```

**Contract Interface:**

```rust
// Generate deterministic proxy G-address for a C-address
fn get_proxy_address(env: Env, c_address: Address) -> Address;

// Forward funds from proxy to destination C-address
// Called by relayer, signed internally by contract
fn forward(env: Env, proxy_address: Address, amount: i128, asset: Address);

// Register a new C-address → proxy mapping
fn register(env: Env, c_address: Address) -> Address;
```

#### Relayer Service

```
services/relayer/
├── src/
│   ├── main.ts             # Entry point
│   ├── monitor.ts          # Horizon streaming listener
│   ├── forwarder.ts        # Transaction builder
│   └── config.ts           # Configuration
├── Dockerfile
├── docker-compose.yml      # Self-hosting setup
└── README.md
```

**Relayer Architecture:**

```
┌─────────────────────────────────────────────────────────────────┐
│                      Relayer Service                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   ┌──────────────┐     ┌──────────────┐     ┌──────────────┐   │
│   │   Horizon    │────▶│   Monitor    │────▶│  Forwarder   │   │
│   │   Stream     │     │   Service    │     │   Service    │   │
│   └──────────────┘     └──────────────┘     └──────┬───────┘   │
│                                                     │           │
│   • Subscribes to         • Filters for            │           │
│     payment events          registered proxies     │           │
│   • Real-time updates     • Validates amounts      ▼           │
│                                              ┌──────────────┐   │
│                                              │    Proxy     │   │
│                                              │   Contract   │   │
│                                              └──────────────┘   │
│                                                                  │
│   Stateless: No database, no key storage, restarts safely       │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

### 3. Reference Wallet

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

### 4. Onboarding Standards

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

### 5. Integration Examples

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
| Proxy Forward Time | < 30s |

---

## User Journey

### New User Onboarding + First Funding

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        Complete User Journey                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ONBOARDING (No seed phrase, no G-address)                                  │
│  ─────────────────────────────────────────                                  │
│                                                                              │
│  ┌─────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐      │
│  │  Visit  │───▶│   Create    │───▶│  Register   │───▶│  Receive    │      │
│  │  Wallet │    │  Username   │    │   Passkey   │    │  C-Address  │      │
│  └─────────┘    └─────────────┘    │ (Face ID /  │    └──────┬──────┘      │
│                                     │  Touch ID)  │           │             │
│                                     └─────────────┘           │             │
│                                                               │             │
│  FUNDING (CEX withdrawal example)                             │             │
│  ────────────────────────────────                             │             │
│                                                               ▼             │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────┐     │
│  │   Request   │───▶│   Receive   │───▶│   Copy to   │───▶│  Funds  │     │
│  │   Proxy     │    │   Proxy G   │    │    CEX      │    │  Arrive │     │
│  │  Address    │    │   Address   │    │  Withdrawal │    │ (< 30s) │     │
│  └─────────────┘    └─────────────┘    └─────────────┘    └─────────┘     │
│                                                                              │
│  TRANSACTING                                                                │
│  ───────────                                                                │
│                                                                              │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐  │
│  │   Select    │───▶│   Enter     │───▶│   Confirm   │───▶│   Done!     │  │
│  │   "Send"    │    │  Recipient  │    │    with     │    │  Tx on      │  │
│  │             │    │  + Amount   │    │   Passkey   │    │  Explorer   │  │
│  └─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘  │
│                                                                              │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │                         Key UX Benefits                                │  │
│  ├───────────────────────────────────────────────────────────────────────┤  │
│  │  ✓ No seed phrase to write down or lose                               │  │
│  │  ✓ No browser extension required                                      │  │
│  │  ✓ Works with any CEX (they just see a normal G-address)              │  │
│  │  ✓ Biometric authentication (Face ID, Touch ID, Windows Hello)        │  │
│  │  ✓ Mobile-first PWA (installable, works offline)                      │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## SDK Integration Example

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    Developer Integration Flow                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   // 1. Install SDK                                                         │
│   npm install @stellar/c-address-sdk                                        │
│                                                                              │
│   // 2. Create wallet with passkey                                          │
│   const { cAddress, wallet } = await createPasskeyWallet("alice");          │
│                                                                              │
│   // 3. Get proxy address for CEX deposits                                  │
│   const proxyAddress = await getProxyAddress(cAddress);                     │
│   // → Returns G-address like "GABCD..." that any CEX can send to           │
│                                                                              │
│   // 4. Fund directly (if source supports C-addresses)                      │
│   await fundCAddress(cAddress, "100", Asset.native(), sourceKeypair);       │
│                                                                              │
│   // 5. Sign transaction with passkey                                       │
│   const tx = await buildTransaction(...);                                   │
│   const signed = await wallet.sign(tx);  // Triggers Face ID / Touch ID    │
│   await submitTransaction(signed);                                          │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```
