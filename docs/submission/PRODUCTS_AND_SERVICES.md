# Products & Services

Current Stellar tooling assumes G-addresses for funding flows. This creates a gap: developers building with Smart Wallets and PasskeyKit cannot easily enable external deposits to C-addresses from exchanges, fiat on-ramps, or other wallets. CEXs and on-ramps only support G-addresses—they cannot send directly to C-addresses.

**G-to-C Proxy Contract + Relayer:** Soroban smart contract that generates deterministic proxy G-addresses for each C-address, plus an open-source relayer service that monitors and forwards funds.

*Technical Implementation:*

1. **Proxy Address Generation:** User requests proxy via SDK. Contract derives deterministic keypair from `hash(c_address + salt)`, generating a standard G-address any CEX can send to.

2. **Forwarding Mechanism:** Relayer service monitors proxy addresses via Horizon streaming API. When payment detected, relayer constructs and submits forwarding transaction signed with contract-derived keypair.

3. **Fee Handling:** Small forwarding fee (0.01 XLM default) covers network fees and reserve pool. Configurable; can be zero for sponsored deployments.

4. **Security Model:** Proxy keypairs are derived deterministically using a contract-controlled secret salt—only the Soroban contract can compute the private key at forwarding time. No private keys are stored; they're derived on-demand within the contract's execution context. The relayer never holds keys—it only submits unsigned transaction templates that the contract signs internally. Destination is cryptographically locked to the original C-address (cannot be redirected by anyone, including the relayer operator).

```
CEX/On-ramp ──▶ Proxy G-Address ──▶ Relayer detects ──▶ Forward to C-Address
                (holds briefly)      (via Horizon)       (within seconds)
```

**C-Address Funding SDK (TypeScript + Python):** Core library enabling direct C-address funding. Includes proxy address generation, transaction building, address resolution, and PasskeyKit integration. Published on npm and PyPI.

**Reference Wallet (Next.js PWA):** Production-grade Progressive Web App demonstrating C-address onboarding and funding flows. Core features at Freighter parity:

| Feature | Included | Not Included (Out of Scope) |
|---------|----------|----------------------------|
| Token balances (SEP-41) | ✅ | dApp browser/connections |
| Transaction history | ✅ | Hardware wallet support |
| Send/receive flows | ✅ | Multi-signature accounts |
| Passkey auth (Face ID, Touch ID) | ✅ | Account recovery via seed phrase |
| Mobile installable (PWA) | ✅ | Native iOS/Android apps |
| Testnet + Mainnet | ✅ | Ledger/Trezor integration |

The reference wallet demonstrates SDK capabilities—not a full consumer wallet replacement. Teams building production wallets can fork or integrate the SDK directly.

**Onboarding Standards + SEP Draft:** Developer guide with integration patterns for exchanges and on-ramps, plus a proposed SEP for C-address discovery and funding protocols submitted to stellar/stellar-protocol. Includes SEP-compliant patterns for fiat on-ramp providers.

**Integration Examples:** Working code for exchange deposits (via proxy), fiat on-ramp integration guide, and AI agent funding flows. The SDK supports Stellar's SEP-41 token standard for multi-asset handling, with guidance for bridge providers integrating C-address support.
