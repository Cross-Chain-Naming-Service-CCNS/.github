# Pull Requests for ENS Integration with Walrus Sites

## Overview

The following pull requests enable the integration of **Walrus**, a Sui-based decentralized storage protocol, with the **Ethereum Name Service (ENS)**, allowing EVM developers to host and resolve Walrus Sites (decentralized websites) under ENS domains (e.g., `yourname.eth`) on the Sepolia testnet and eventually mainnet.

The integration introduces:
- A custom `walrus-site-ns` multicodec to store hex-encoded Walrus site object IDs in the ENS contenthash field
- Conversion of these IDs to base36 for URL-friendly subnames (e.g., `[base36_id].your-portal.eth.limo`)
- Updates to ENS libraries and tools to support resolution in browsers via a Chrome extension
- Portal infrastructure serving content with web workers for efficient fetching

---

## Pull Requests

### 1. Multicodec Registry Update

**Repository:** `multiformats/multicodec`  
**Link:** https://github.com/multiformats/multicodec/pull/391  
**PR:** #391  
**Status:** Awaiting review; mock protoCode used for testing

#### Changes

- Added a new multicodec entry for `walrus-site-ns` with protoCode `0xb59910` (or finalized code post-review)
- Format: `<varint protoCode><hex-encoded site object ID>` for encoding Walrus site object IDs (Sui-based, representing full websites)

#### Purpose

Enables standardized encoding/decoding of Walrus site IDs in ENS contenthash fields, ensuring compatibility with multiformat standards.

---

### 2. Content-Hash Library Enhancement

**Repository:** `ensdomains/content-hash`  
**Link:** https://github.com/ensdomains/content-hash/pull/16  
**PR:** #16  
**Status:** Under review

#### Changes

- **Added `walrus-ns` profile to `src/profiles.ts`:**
  - **Encode:** Converts hex site ID (e.g., `0x1a2b3c4d...`) to bytes using `hexStringToBytes`
  - **Decode:** Converts bytes back to hex (`0x${bytesToHexString(bytes)}`)
- Updated `src/index.ts` to export the new profile
- Added tests in `tests/content-hash.test.ts` to verify encoding/decoding
- Updated `README.md` to document `walrus-ns` usage

#### Purpose

Extends the `@ensdomains/content-hash` library to support encoding/decoding Walrus site object IDs for ENS contenthash.

---

### 3. ENS App UI Support

**Repository:** `ensdomains/ens-app-v3`  
**Link:** https://github.com/ensdomains/ens-app-v3/pull/1064  
**PR:** #1064  
**Status:** Under review

#### Changes

- Updated `package.json` to depend on the modified `@ensdomains/content-hash`
- Modified `src/utils/contenthash.ts` to:
  - Add `walrus-ns` to `contentHashToProtocols`
  - Validate hex site IDs with regex: `/^0x[a-fA-F0-9]+$/`
- Enhanced `src/features/records/` to support inputting hex site IDs in the UI (e.g., `0x1a2b3c4d...`) for setting contenthash
- Added end-to-end tests in `tests/e2e/makeName.spec.ts` for Walrus contenthash setting

#### Purpose

Enables users to set and manage Walrus site object IDs as contenthash records in the ENS Manager App, with proper validation and UI integration.

---

### 4. ENSJS Library Update

**Repository:** `ensdomains/ensjs`  
**Link:** https://github.com/ensdomains/ensjs/pull/260  
**PR:** #260  
**Status:** Under review

#### Changes

- Updated `package.json` to use the modified `@ensdomains/content-hash`
- Ensured `src/functions/contenthash.ts` supports `walrus-ns` decoding via the updated library
- Added tests in `tests/` to verify `getContenthash` returns the correct hex site ID (e.g., `0x1a2b3c4d...`)

#### Purpose

Ensures the `ensjs` library can resolve Walrus site contenthashes, maintaining compatibility with ENS tools and clients.

---

## Project Context

These PRs are part of a broader effort to enable EVM developers to host **Walrus Sites** (decentralized websites on Sui) under ENS domains.

### Workflow

1. **Hosting**  
   Developers use Walrus' `site-builder` CLI to publish a site, generating a hex site object ID (e.g., `0x1a2b3c4d...`)

2. **Base36 Subnames**  
   The hex ID is converted to base36 (e.g., `k2t6wyfsu4pfw...`) for URL-friendly subnames (e.g., `[base36_id].your-portal.eth.limo`)

3. **Portal Infrastructure**  
   A decentralized Walrus portal resolves subnames to site content using Walrus SDK and Sui RPC

4. **Chrome Extension**  
   A Manifest V3 extension intercepts `.eth` navigation, queries contenthash on Sepolia, decodes to hex ID, converts to base36, builds the portal URL, and renders content using web workers for performance

5. **Testing**  
   Validated on Sepolia with a test domain (`walrustest.eth`) and sample sites

---

## Next Steps

### 1. Review and Merge

Engage with maintainers to address feedback and finalize protoCode allocation

### 2. Mainnet Migration

Adapt for Ethereum mainnet and Walrus mainnet for production use

### 3. Ecosystem Adoption

- Propose an **ENSIP** in `ensdomains/ensips` to formalize `walrus-site-ns` support
- Integrate with major ENS-compatible wallets and browsers

### 4. Documentation

Update `ensdomains/docs` with Walrus integration guides and developer tutorials

---

## Technical Benefits

This integration bridges **Ethereum (ENS)** and **Sui (Walrus)** ecosystems to provide:

- ✅ **Seamless cross-chain integration** between naming and storage layers
- ✅ **Decentralized website hosting** with censorship resistance
- ✅ **Human-readable domain names** for decentralized content
- ✅ **Standardized encoding** via multiformat specifications
- ✅ **Browser-native resolution** through Chrome extension
- ✅ **Scalable infrastructure** for global content delivery

---

## Repository Links

| Repository | PR Link |
|------------|---------|
| **Multicodec Registry** | https://github.com/multiformats/multicodec/pull/391 |
| **Content-Hash Library** | https://github.com/ensdomains/content-hash/pull/16 |
| **ENS Manager UI** | https://github.com/ensdomains/ens-app-v3/pull/1064 |
| **ENSJS Library** | https://github.com/ensdomains/ensjs/pull/260 |

---

## Contact & Community

**GitHub Organization:** [Cross-Chain-Naming-Service-CCNS](https://github.com/Cross-Chain-Naming-Service-CCNS)  
**Documentation:** [CCNS GitHub](https://github.com/Cross-Chain-Naming-Service-CCNS/.github)

---

*This integration represents a significant step forward in cross-chain Web3 infrastructure, enabling truly decentralized websites with memorable, human-readable addresses.*
