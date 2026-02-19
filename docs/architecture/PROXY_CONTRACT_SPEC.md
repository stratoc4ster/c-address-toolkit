# G-to-C Proxy Contract: Technical Specification

**Version:** 1.0
**Date:** 2025-02-19
**Status:** Design Complete

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Architecture Overview](#architecture-overview)
3. [Why This Architecture](#why-this-architecture)
4. [Component Details](#component-details)
   - [Proxy Registry Contract](#1-proxy-registry-contract-soroban)
   - [Key Derivation Scheme](#2-key-derivation-scheme)
   - [Relayer Service](#3-relayer-service)
   - [Relayer Reliability & Recovery](#4-relayer-reliability--recovery)
   - [Master Seed Management](#5-master-seed-management)
   - [Relayer Funding & Economics](#6-relayer-funding--economics)
5. [Security Model](#security-model)
   - [Threat Analysis](#threat-analysis)
   - [Minimum Forward Threshold](#minimum-forward-threshold)
   - [What the Relayer CANNOT Do](#what-the-relayer-cannot-do)
   - [Fund Recovery](#fund-recovery)
6. [Flow Diagrams](#flow-diagrams)
7. [Implementation Checklist](#implementation-checklist)
8. [FAQ](#faq)
9. [Appendix: Cryptographic Details](#appendix-cryptographic-details)

---

## Executive Summary

This document provides a detailed technical specification for the G-to-C Proxy Contract system, addressing how the proxy enables CEX/on-ramp deposits to C-addresses without requiring those services to change.

**Key Insight:** CEXs can only send to G-addresses (Ed25519 public keys starting with `G`). Soroban contracts have C-addresses (starting with `C`). The proxy system bridges this gap using deterministic key derivation and a stateless relayer.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           G-to-C Proxy System                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ┌──────────────┐      ┌───────────────────┐      ┌──────────────────┐    │
│   │   SDK        │      │  Proxy Registry   │      │     Relayer      │    │
│   │              │─────▶│   (Soroban)       │◀─────│    Service       │    │
│   │              │      │                   │      │                  │    │
│   │ • Request    │      │ • Maps C→G        │      │ • Monitors       │    │
│   │   proxy addr │      │ • Stores mappings │      │   Horizon        │    │
│   │ • Validate   │      │ • No secrets      │      │ • Derives keys   │    │
│   │   addresses  │      │   stored          │      │ • Signs forwards │    │
│   └──────────────┘      └───────────────────┘      └────────┬─────────┘    │
│                                                              │              │
│                                                              ▼              │
│                                                    ┌──────────────────┐    │
│                                                    │   Master Seed    │    │
│                                                    │   (Secure Store) │    │
│                                                    └──────────────────┘    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Why This Architecture

### The Constraint

CEXs and fiat on-ramps have deeply integrated systems that:
1. Validate withdrawal addresses using Stellar G-address format (Ed25519)
2. Cannot send directly to C-addresses (contract addresses)
3. Will not change their systems for individual projects

### The Solution

Create a **deterministic mapping** from C-addresses to G-addresses that:
1. Allows users to share a normal-looking G-address with CEXs
2. Automatically forwards funds to their C-address
3. Requires no changes from CEXs/on-ramps

---

## Component Details

### 1. Proxy Registry Contract (Soroban)

The registry is a simple Soroban contract that:
- Stores the mapping: `C-address → Proxy G-address`
- Does NOT store any secrets or private keys
- Is read-only for the relayer (no sensitive operations)

**Contract Interface:**

```rust
/// Register a C-address and get its deterministic proxy G-address
pub fn register(env: Env, c_address: Address) -> String {
    // Compute deterministic G-address from C-address
    // Store mapping for lookup
    // Return the proxy G-address (public key)
}

/// Look up the proxy G-address for a C-address
pub fn get_proxy(env: Env, c_address: Address) -> Option<String> {
    // Return the proxy G-address if registered
}

/// Look up the C-address for a proxy G-address (reverse lookup)
pub fn get_destination(env: Env, proxy_g: String) -> Option<Address> {
    // Return the C-address that should receive funds
}

/// Get total registered proxies (for monitoring)
pub fn get_stats(env: Env) -> ProxyStats {
    // Return count and other metrics
}
```

**What the Contract DOES NOT Do:**
- Store private keys
- Sign transactions
- Hold funds
- Perform any cryptographic signing operations

The contract is purely a **registry** - a mapping table.

---

### 2. Key Derivation Scheme

The security of the system relies on deterministic key derivation:

```
                    ┌─────────────────────────────────────────┐
                    │           Key Derivation                 │
                    ├─────────────────────────────────────────┤
                    │                                          │
    Master Seed     │   proxy_keypair = derive_keypair(       │
        │           │       HKDF(                              │
        │           │           master_seed,                   │
        │           │           c_address.to_bytes(),          │
        │           │           "stellar-proxy-v1"             │
        │           │       )                                  │
        │           │   )                                      │
        ▼           │                                          │
    ┌─────────┐     │   public_key → G-address (shared)       │
    │ 32-byte │────▶│   secret_key → Never stored (derived)    │
    │  secret │     │                                          │
    └─────────┘     └─────────────────────────────────────────┘
```

**Derivation Properties:**

| Property | Description |
|----------|-------------|
| Deterministic | Same C-address always produces same proxy G-address |
| One-way | Cannot derive master seed from proxy keypair |
| Isolated | Compromising one proxy doesn't reveal others |
| Stateless | Keys derived on-demand, not stored |

**Implementation (TypeScript):**

```typescript
import { hkdf } from '@noble/hashes/hkdf';
import { sha256 } from '@noble/hashes/sha256';
import { Keypair } from '@stellar/stellar-sdk';

function deriveProxyKeypair(masterSeed: Uint8Array, cAddress: string): Keypair {
  // Derive 32 bytes using HKDF
  const derivedKey = hkdf(
    sha256,
    masterSeed,
    Buffer.from(cAddress, 'utf-8'),  // Salt: the C-address
    Buffer.from('stellar-proxy-v1'),   // Info: version identifier
    32                                  // Output: 32 bytes for Ed25519 seed
  );

  // Create Stellar keypair from derived seed
  return Keypair.fromRawEd25519Seed(Buffer.from(derivedKey));
}

// Example usage:
const masterSeed = /* securely loaded from HSM/vault */;
const cAddress = 'CDLZFC3SYJYDZT7K67VZ75HPJVIEUVNIXF47ZG2FB2RMQQVU2HHGCYSC';
const proxyKeypair = deriveProxyKeypair(masterSeed, cAddress);
console.log('Proxy G-address:', proxyKeypair.publicKey());
// → G-address that CEXs can send to
```

---

### 3. Relayer Service

The relayer is a stateless service that:
1. Monitors Horizon for payments to registered proxy addresses
2. Derives the signing keypair on-demand when a payment is detected
3. Signs and submits the forwarding transaction
4. Discards the keypair from memory immediately after use

**Architecture:**

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           Relayer Service                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ┌──────────────┐     ┌──────────────────┐     ┌──────────────────────┐   │
│   │   Horizon    │     │    Payment       │     │    Forward           │   │
│   │   Stream     │────▶│    Detector      │────▶│    Builder           │   │
│   │              │     │                  │     │                      │   │
│   │ SSE endpoint │     │ • Filter by      │     │ • Build payment tx   │   │
│   │ /payments    │     │   proxy addrs    │     │ • Include memo       │   │
│   └──────────────┘     │ • Validate amt   │     │ • Deduct fee         │   │
│                        └──────────────────┘     └──────────┬───────────┘   │
│                                                             │               │
│                                                             ▼               │
│                        ┌──────────────────┐     ┌──────────────────────┐   │
│                        │    Submission    │◀────│    Key Derivation    │   │
│                        │    Service       │     │                      │   │
│                        │                  │     │ • Derive keypair     │   │
│                        │ • Submit to      │     │ • Sign transaction   │   │
│                        │   Horizon        │     │ • Wipe from memory   │   │
│                        │ • Retry logic    │     │                      │   │
│                        └──────────────────┘     └──────────────────────┘   │
│                                                                              │
│   Stateless: No database, restarts safely, horizontally scalable            │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Security Measures:**

| Measure | Description |
|---------|-------------|
| Memory-only keys | Derived keypairs exist only in RAM during signing |
| Immediate cleanup | Keys are zeroed out after use (secure wipe) |
| No key storage | Relayer stores nothing - only the master seed exists |
| Audit logging | All forwards logged with transaction hashes |
| Rate limiting | Prevents abuse of forwarding service |

---

### 4. Relayer Reliability & Recovery

The relayer must handle Horizon stream disconnections gracefully. Network issues, Horizon maintenance, and temporary outages are expected.

**Reconnection Strategy:**

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      Horizon Stream Recovery Logic                           │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ┌──────────┐    Disconnect    ┌──────────────┐    Backoff    ┌─────────┐ │
│   │ CONNECTED│───────────────▶│ RECONNECTING │──────────────▶│ WAITING │ │
│   └────┬─────┘                 └──────┬───────┘               └────┬────┘ │
│        │                              │                            │       │
│        │ ◀──────── Success ───────────┘                            │       │
│        │                                                           │       │
│        │ ◀─────────────── Retry ──────────────────────────────────┘       │
│                                                                              │
│   Backoff Schedule:                                                         │
│   Attempt 1: 1 second                                                       │
│   Attempt 2: 2 seconds                                                      │
│   Attempt 3: 4 seconds                                                      │
│   Attempt 4: 8 seconds                                                      │
│   Attempt 5+: 30 seconds (max)                                              │
│                                                                              │
│   After 10 consecutive failures: Alert + Continue retrying                  │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Implementation (TypeScript):**

```typescript
class HorizonStreamManager {
  private cursor: string = 'now';
  private retryCount: number = 0;
  private readonly maxBackoff: number = 30000; // 30 seconds

  async startStream(): Promise<void> {
    while (true) {
      try {
        await this.connectAndListen();
        this.retryCount = 0; // Reset on successful connection
      } catch (error) {
        this.retryCount++;
        const backoff = this.calculateBackoff();

        console.error(`Stream disconnected. Retry ${this.retryCount} in ${backoff}ms`, error);

        if (this.retryCount >= 10) {
          await this.sendAlert('Horizon stream: 10 consecutive failures');
        }

        await this.sleep(backoff);
      }
    }
  }

  private calculateBackoff(): number {
    // Exponential backoff with jitter, capped at maxBackoff
    const base = Math.min(1000 * Math.pow(2, this.retryCount - 1), this.maxBackoff);
    const jitter = Math.random() * 1000; // 0-1 second jitter
    return base + jitter;
  }

  private async connectAndListen(): Promise<void> {
    const stream = server.payments()
      .forAccount(proxyAddress)
      .cursor(this.cursor)
      .stream({
        onmessage: (payment) => {
          this.cursor = payment.paging_token; // Track position
          this.handlePayment(payment);
        },
        onerror: (error) => {
          throw error; // Trigger reconnection
        }
      });
  }
}
```

**Gap Detection & Catchup:**

On reconnection, the relayer must check for payments that arrived during the outage:

```typescript
async function catchupMissedPayments(lastCursor: string): Promise<void> {
  // Fetch all payments since last known cursor
  const payments = await server.payments()
    .forAccount(proxyAddress)
    .cursor(lastCursor)
    .limit(200)
    .order('asc')
    .call();

  for (const payment of payments.records) {
    // Check if already forwarded (idempotency)
    if (!(await isAlreadyForwarded(payment.id))) {
      await processPayment(payment);
    }
  }
}
```

**Monitoring & Alerting:**

| Metric | Alert Threshold | Action |
|--------|-----------------|--------|
| Stream disconnections/hour | > 10 | Page on-call |
| Forwarding latency (p99) | > 60 seconds | Investigate |
| Failed forwards/hour | > 5 | Page on-call |
| Memory usage | > 80% | Scale or investigate |
| Last successful forward | > 1 hour (with pending) | Alert |

**Health Check Endpoint:**

```typescript
// GET /health
{
  "status": "healthy",
  "stream": {
    "connected": true,
    "lastEvent": "2025-02-19T10:30:00Z",
    "cursor": "12345678"
  },
  "stats": {
    "forwardsToday": 42,
    "failedToday": 0,
    "avgLatencyMs": 2500
  }
}
```

---

### 5. Master Seed Management

The master seed is the only secret in the system. It must be:

**Production Deployment:**

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        Master Seed Security                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   Option A: Hardware Security Module (HSM)                                  │
│   ┌─────────────────┐                                                       │
│   │  AWS CloudHSM   │ ◀── Master seed never leaves HSM                     │
│   │  or Azure HSM   │ ◀── Key derivation happens inside HSM                │
│   └─────────────────┘ ◀── FIPS 140-2 Level 3 compliance                    │
│                                                                              │
│   Option B: Secrets Manager + Secure Enclave                                │
│   ┌─────────────────┐     ┌─────────────────┐                              │
│   │  AWS Secrets    │────▶│  Relayer in     │                              │
│   │  Manager        │     │  Secure Enclave │                              │
│   └─────────────────┘     └─────────────────┘                              │
│                                                                              │
│   Option C: Self-Hosted (Open Source)                                       │
│   ┌─────────────────┐                                                       │
│   │  User-managed   │ ◀── For self-hosters who want full control           │
│   │  seed file      │ ◀── Documented security best practices               │
│   └─────────────────┘ ◀── Docker secrets / env variable                    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Seed Generation:**

```typescript
import { randomBytes } from 'crypto';

// Generate master seed (do this ONCE, store securely)
const masterSeed = randomBytes(32);
console.log('Master seed (hex):', masterSeed.toString('hex'));
// Store this in HSM/Secrets Manager - NEVER in code or git
```

**Production Deployment Decision:**

The hosted relayer deployment follows a phased security approach:

| Phase | Environment | Security Tier |
|-------|-------------|---------------|
| **Testnet** | AWS Secrets Manager + Nitro Enclave | Strong (~$50/month) |
| **Mainnet** | **AWS CloudHSM** | Maximum (FIPS 140-2 Level 3) |

**Testnet (Tranche 1-2):** Option B - AWS Secrets Manager + Secure Enclave

| Aspect | Implementation |
|--------|----------------|
| **Seed Storage** | AWS Secrets Manager with automatic rotation disabled |
| **Runtime Environment** | AWS Nitro Enclave (isolated memory, no external access) |
| **Access Control** | IAM role-based access, audit logging via CloudTrail |
| **Backup** | Encrypted backup in separate AWS account with break-glass procedure |

**Mainnet (Tranche 3):** Option A - HSM-backed key storage

| Aspect | Implementation |
|--------|----------------|
| **Seed Storage** | AWS CloudHSM (FIPS 140-2 Level 3 certified) |
| **Key Operations** | HKDF derivation occurs inside HSM—seed never leaves hardware |
| **Access Control** | HSM partition with MFA-protected admin access |
| **Audit** | CloudTrail + HSM audit logs for all key operations |
| **Cost** | ~$1,500/month (justified for mainnet security) |

**Rationale:**
- Testnet: Secrets Manager + Enclave is cost-effective for development/testing
- Mainnet: **HSM is required**—real user funds demand hardware-level security
- The hosted mainnet relayer will use HSM-backed key storage (non-negotiable)
- Nitro Enclaves provide attestation (cryptographic proof of what code is running)
- Self-hosters can choose their own security tier based on their risk tolerance

**Self-Hoster Guidance:**

For teams running their own relayer:

```yaml
# docker-compose.yml example
services:
  relayer:
    image: stellar-c-toolkit/relayer:latest
    environment:
      # Option 1: Environment variable (simplest, less secure)
      - MASTER_SEED=${MASTER_SEED}
      # Option 2: Docker secrets (recommended)
      - MASTER_SEED_FILE=/run/secrets/master_seed
    secrets:
      - master_seed
    deploy:
      resources:
        limits:
          memory: 256M  # Limit memory to reduce key exposure window

secrets:
  master_seed:
    external: true  # Created via: docker secret create master_seed ./seed.txt
```

---

### 6. Relayer Funding & Economics

The relayer needs XLM to pay transaction fees when forwarding payments. This section documents the funding model to ensure long-term sustainability.

**The Problem:** The OpenZeppelin relayer on mainnet ran out of XLM, causing forwarding failures. We address this explicitly.

**Three-Tier Funding Model:**

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         Relayer Funding Models                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   Tier 1: HOSTED RELAYER (Reference Implementation)                         │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │ Funding Source: Small forwarding fee deducted from each transfer    │   │
│   │                                                                      │   │
│   │ Fee Structure:                                                       │   │
│   │   • Base network fee: ~0.00001 XLM (Stellar network)                │   │
│   │   • Relayer fee: 0.01 XLM (covers operations + reserve)             │   │
│   │   • Total deducted: ~0.01 XLM per forward                           │   │
│   │                                                                      │   │
│   │ Sustainability:                                                      │   │
│   │   • 10,000 forwards = 100 XLM revenue                               │   │
│   │   • Covers infrastructure (~$50/month) + reserve                    │   │
│   │   • Fee is transparent and documented in SDK                        │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
│   Tier 2: SELF-HOSTED RELAYER                                               │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │ Funding Source: Operator funds their own relayer account            │   │
│   │                                                                      │   │
│   │ Requirements:                                                        │   │
│   │   • Minimum reserve: 100 XLM recommended                            │   │
│   │   • Operator monitors balance via health check endpoint             │   │
│   │   • Operator sets their own fee structure (including 0 fee)         │   │
│   │                                                                      │   │
│   │ Use Case: Enterprises, privacy-focused users, high-volume apps      │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
│   Tier 3: SPONSORED RELAYER                                                  │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │ Funding Source: Protocol/dApp subsidizes forwarding for users       │   │
│   │                                                                      │   │
│   │ Examples:                                                            │   │
│   │   • DEX subsidizes deposits for liquidity providers                 │   │
│   │   • NFT platform covers fees for first-time users                   │   │
│   │   • Gaming protocol sponsors all in-game deposits                   │   │
│   │                                                                      │   │
│   │ Implementation: Sponsor tops up designated relayer account          │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Hosted Relayer Sustainability Measures:**

| Measure | Implementation |
|---------|----------------|
| **Low balance alert** | Alert at < 50 XLM reserve |
| **Critical alert** | Page on-call at < 10 XLM |
| **Auto-pause** | Stop accepting new proxies at < 5 XLM |
| **Reserve target** | Maintain 500 XLM minimum |
| **Fee adjustment** | Can increase fee if costs rise (with notice) |

**Fee Transparency:**

The SDK will clearly document fees:

```typescript
// SDK shows fee before user confirms
const estimate = await sdk.estimateForward(amount);
// {
//   grossAmount: "100.0000000",
//   relayerFee: "0.0100000",
//   networkFee: "0.0000100",
//   netAmount: "99.9899900"  // What arrives at C-address
// }
```

---

## Security Model

### Threat Analysis

| Threat | Mitigation |
|--------|------------|
| **Relayer compromise** | Attacker gets master seed → Can derive all proxy keys. **Mitigation:** HSM prevents seed extraction. Self-hosters accept this risk with their own infrastructure. |
| **Proxy key compromise** | Single proxy keypair leaked → Only that one C-address at risk. **Mitigation:** Key derivation is isolated per C-address. |
| **Man-in-the-middle** | Attacker intercepts forward → Cannot modify destination (locked in registry). **Mitigation:** Destination lookup is on-chain and immutable. |
| **Relayer unavailability** | Service goes down → Funds stuck in proxy. **Mitigation:** Multiple relayers can run with same seed. Funds are recoverable. |
| **Registry corruption** | Wrong mapping stored → Funds sent to wrong address. **Mitigation:** Mappings are deterministic and verifiable. SDK validates before use. |
| **Dust attack DoS** | Attacker floods proxy addresses with tiny payments → Relayer exhausts resources processing. **Mitigation:** Minimum forward threshold (see below). |

### Minimum Forward Threshold

To prevent denial-of-service via dust payments, the relayer enforces a minimum forward amount:

**Default Configuration:**

| Setting | Value | Rationale |
|---------|-------|-----------|
| `MIN_FORWARD_AMOUNT` | 0.5 XLM | Covers fees + prevents abuse |
| `DUST_ACCUMULATE` | true | Accumulate dust until threshold met |
| `DUST_ALERT_THRESHOLD` | 10 dust payments/hour | Alert on potential attack |

**Configurable Per-Deployment:**

Self-hosters can adjust the threshold based on their use case:

```yaml
# relayer-config.yml
forwarding:
  min_amount: 0.5        # Default: 0.5 XLM
  # min_amount: 0.1      # Lower for micro-payment use cases
  # min_amount: 5.0      # Higher for high-value-only relayer

  dust_handling:
    accumulate: true     # Hold dust until min_amount reached
    max_accumulation: 100  # Max payments to accumulate before alert
```

**Dust Payment Handling:**

When a payment below threshold is received:
1. Payment is logged but NOT forwarded immediately
2. Balance accumulates in proxy address
3. When accumulated balance exceeds threshold, forward occurs
4. User is notified via SDK that dust is accumulating

```typescript
// SDK can check accumulated dust
const proxyStatus = await sdk.getProxyStatus(cAddress);
// {
//   proxyAddress: "GABCD...",
//   pendingBalance: "0.3500000",  // Below threshold
//   minForwardAmount: "0.5000000",
//   status: "accumulating"
// }
```

### What the Relayer CANNOT Do

Even with the master seed, the relayer:
- Cannot change destinations (locked in registry contract)
- Cannot steal funds in transit (forwards are atomic)
- Cannot create fake mappings (requires user SDK call)
- Cannot access C-address wallets (only forwards TO them)

### Fund Recovery

If the relayer is unavailable, funds can be recovered:

```typescript
// Emergency recovery: User or operator can manually forward
const masterSeed = /* loaded from backup */;
const cAddress = 'CDLZ...';
const proxyKeypair = deriveProxyKeypair(masterSeed, cAddress);

// Check proxy balance
const proxyAccount = await server.loadAccount(proxyKeypair.publicKey());
const balance = proxyAccount.balances.find(b => b.asset_type === 'native');

// Manually forward
const tx = new TransactionBuilder(proxyAccount, { fee: '100' })
  .addOperation(Operation.payment({
    destination: cAddress, // C-address
    asset: Asset.native(),
    amount: balance.balance,
  }))
  .setTimeout(30)
  .build();

tx.sign(proxyKeypair);
await server.submitTransaction(tx);
```

---

## Flow Diagrams

### Registration Flow

```
User                     SDK                    Registry Contract
  │                       │                           │
  │  1. "I want to        │                           │
  │     receive funds"    │                           │
  │──────────────────────▶│                           │
  │                       │                           │
  │                       │  2. register(c_address)   │
  │                       │──────────────────────────▶│
  │                       │                           │
  │                       │                           │ 3. Compute:
  │                       │                           │    proxy_g = derive_public_key(
  │                       │                           │      hash(c_address + version)
  │                       │                           │    )
  │                       │                           │
  │                       │                           │ 4. Store mapping:
  │                       │                           │    c_address → proxy_g
  │                       │                           │
  │                       │  5. Return proxy_g        │
  │                       │◀──────────────────────────│
  │                       │                           │
  │  6. "Share this       │                           │
  │     G-address with    │                           │
  │     exchanges"        │                           │
  │◀──────────────────────│                           │
  │                       │                           │
  │  proxy_g = "GABCD..." │                           │
  │  (looks like normal   │                           │
  │   G-address to CEX)   │                           │
```

### Forwarding Flow

```
CEX          Proxy G-addr       Horizon        Relayer           C-Address
 │                │                │              │                  │
 │  1. Withdraw   │                │              │                  │
 │───────────────▶│                │              │                  │
 │  (standard     │                │              │                  │
 │   XLM payment) │                │              │                  │
 │                │                │              │                  │
 │                │  2. Payment    │              │                  │
 │                │    event       │              │                  │
 │                │───────────────▶│              │                  │
 │                │                │              │                  │
 │                │                │  3. Stream   │                  │
 │                │                │    to        │                  │
 │                │                │    relayer   │                  │
 │                │                │─────────────▶│                  │
 │                │                │              │                  │
 │                │                │              │ 4. Lookup destination
 │                │                │              │    in registry
 │                │                │              │                  │
 │                │                │              │ 5. Derive keypair
 │                │                │              │    (in memory)
 │                │                │              │                  │
 │                │                │              │ 6. Build forward tx
 │                │                │              │                  │
 │                │                │              │ 7. Sign with
 │                │                │              │    derived key
 │                │                │              │                  │
 │                │                │              │ 8. Submit to      │
 │                │                │              │    Horizon        │
 │                │                │              │─────────────────▶│
 │                │                │              │                  │
 │                │                │              │ 9. Wipe keypair   │
 │                │                │              │    from memory    │
 │                │                │              │                  │
 │                │                │              │                  │ 10. Funds
 │                │                │              │                  │     received!
 │                │                │              │                  │
 │                │                │              │                  │
   ◀───────────────────── < 30 seconds end-to-end ─────────────────▶
```

---

## Implementation Checklist

### Contract (Soroban)

- [ ] `register(c_address)` - Register new proxy mapping
- [ ] `get_proxy(c_address)` - Lookup proxy G-address
- [ ] `get_destination(proxy_g)` - Reverse lookup for relayer
- [ ] `get_stats()` - Monitoring metrics
- [ ] Unit tests for all functions
- [ ] Integration tests with testnet

### Relayer

- [ ] Horizon SSE stream listener
- [ ] Payment detection and filtering
- [ ] Key derivation module (HKDF)
- [ ] Transaction builder
- [ ] Secure memory handling (key wiping)
- [ ] Retry logic with exponential backoff
- [ ] Health check endpoint
- [ ] Metrics/logging
- [ ] Docker deployment
- [ ] HSM integration (production)

### SDK

- [ ] `getProxyAddress(cAddress)` - Get or create proxy
- [ ] `validateProxyMapping(cAddress, proxyG)` - Verify on-chain
- [ ] `estimateForwardFee()` - Calculate relay costs
- [ ] TypeScript types
- [ ] Python bindings

---

## FAQ

**Q: Why not have the contract sign transactions directly?**

A: Soroban contracts cannot sign arbitrary Stellar transactions. Contracts can only authorize their own operations (like contract-to-contract calls). To move funds from a G-address (which is an Ed25519 keypair account), you need the private key to sign.

**Q: Is the relayer centralized?**

A: The reference relayer is a single service, but:
1. Anyone can run their own with the same master seed
2. Multiple relayers can run simultaneously
3. The protocol is open source
4. Users can manually recover funds if needed

**Q: What if someone derives my proxy keypair?**

A: They would need the master seed. Without it, they cannot derive any keys. The HKDF derivation is cryptographically secure.

**Q: What about fees?**

A: The relayer deducts network fees from the forwarded amount. This is transparent and documented. Users see the net amount arrive at their C-address.

**Q: Can I use this without trusting your relayer?**

A: Yes. Self-hosters can:
1. Generate their own master seed
2. Run their own relayer
3. Have full control over their proxy system

---

## Appendix: Cryptographic Details

### HKDF Parameters

| Parameter | Value |
|-----------|-------|
| Hash | SHA-256 |
| IKM | Master seed (32 bytes) |
| Salt | C-address (UTF-8 bytes) |
| Info | "stellar-proxy-v1" |
| Length | 32 bytes |

### Ed25519 Key Derivation

The 32-byte HKDF output is used directly as the Ed25519 seed:

```
HKDF output (32 bytes) → Ed25519 seed → Keypair
                                      ├─▶ Public key → G-address
                                      └─▶ Secret key → Signing
```

### Transaction Signing

Standard Stellar Ed25519 signing using the derived keypair. No special cryptography - just standard Stellar SDK methods.

---

*Document last updated: 2025-02-19*
