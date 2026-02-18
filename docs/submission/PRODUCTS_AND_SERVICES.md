# Products & Services

Current Stellar tooling assumes G-addresses for funding flows. This creates a gap: developers building with Smart Wallets and PasskeyKit cannot easily enable external deposits to C-addresses from exchanges, fiat on-ramps, or other wallets.

**G-to-C Proxy Contract:** Soroban smart contract that generates deterministic proxy G-addresses for each C-address. When funds arrive at a proxy G-address (from CEX withdrawal, on-ramp, or any G-address source), the contract automatically forwards to the destination C-address. This makes the G-address step transparent to end users and compatible with existing infrastructure.

**C-Address Funding SDK (TypeScript + Python):** Core library enabling direct C-address funding. Includes proxy address generation, transaction building, address resolution, and PasskeyKit integration. Published on npm and PyPI.

**Reference Wallet (Next.js PWA):** Production-grade Progressive Web App with native passkey support (Face ID, Touch ID, Windows Hello). Installable on mobile home screens with app-like experience. Displays all SEP-41 tokens and transaction history (Freighter parity). TypeScript SDK compatible with React Native for teams building native mobile apps.

**Onboarding Standards + SEP Draft:** Developer guide with integration patterns for exchanges and on-ramps, plus a proposed SEP for C-address discovery and funding protocols submitted to stellar/stellar-protocol. Includes SEP-compliant patterns for fiat on-ramp providers.

**Integration Examples:** Working code for exchange deposits (via proxy), fiat on-ramp integration guide, and AI agent funding flows. The SDK supports Stellar's SEP-41 token standard for multi-asset handling, with guidance for bridge providers integrating C-address support.
