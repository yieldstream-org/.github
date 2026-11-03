<div align="center">

<img src="https://raw.githubusercontent.com/yieldstream-org/yieldstream-app/main/app/icon.svg" alt="YieldStream logo" width="96" height="96" />

# YieldStream

**Token distribution on Stellar — one 32-byte commitment, however many recipients.**

[![Contract CI](https://github.com/yieldstream-org/yieldstream-contract/actions/workflows/ci.yml/badge.svg)](https://github.com/yieldstream-org/yieldstream-contract/actions/workflows/ci.yml)
[![SDK CI](https://github.com/yieldstream-org/yieldstream-sdk/actions/workflows/ci.yml/badge.svg)](https://github.com/yieldstream-org/yieldstream-sdk/actions/workflows/ci.yml)
[![App CI](https://github.com/yieldstream-org/yieldstream-app/actions/workflows/ci.yml/badge.svg)](https://github.com/yieldstream-org/yieldstream-app/actions/workflows/ci.yml)
[![License](https://img.shields.io/badge/license-MIT%20OR%20Apache--2.0-blue.svg)](#license)
[![Network](https://img.shields.io/badge/network-Stellar%20testnet-7B2BF9)](https://stellar.expert/explorer/testnet/contract/CC25WDHZ7SQC2UMZJHIPYM2U5ROYFXVDZMP3THMIL3IJY6HOCGGX2FPG)

[LIVE SITE](https://yieldstream-protocol.vercel.app) · [DOCS](https://yieldstream-protocol.vercel.app/docs) · [TESTNET CONTRACT](https://stellar.expert/explorer/testnet/contract/CC25WDHZ7SQC2UMZJHIPYM2U5ROYFXVDZMP3THMIL3IJY6HOCGGX2FPG) · [CONTRIBUTE](https://github.com/yieldstream-org/yieldstream-contract/issues)

</div>

---

## The problem, in plain terms

A project on Stellar that wants to pay 10,000 wallets — an airdrop, retroactive
yield, a contributor payout — normally has to send 10,000 transactions and keep
10,000 records on the ledger. The issuer pays for all of it up front, whether
or not anyone claims.

YieldStream reverses that. The recipient list is hashed into a single 32-byte
Merkle root. The issuer commits that root on-chain and escrows the token pool.
Each recipient then proves they belong to the list with a short cryptographic
path — about 14 hashes for a 10,000-person list — and pays for their own claim.
Whatever is unclaimed when the campaign expires sweeps back to the issuer in one
call.

The issuer pays for one commitment. The ledger stores a root, not a roster —
the recipient list never goes on-chain. Each *claim* does write its own ledger
entry today; [contract issue
#13](https://github.com/yieldstream-org/yieldstream-contract/issues/13) tracks
packing those markers into a bitmap so a 10,000-claim campaign costs ~40 entries
instead of 10,000.

## The three repos

| Repo | Language | What lives there |
|---|---|---|
| [`yieldstream-contract`](https://github.com/yieldstream-org/yieldstream-contract) | Rust / Soroban | The distributor contract: campaign creation, escrow, proof verification, claims, cancel, sweep. RFC 6962 domain separation, sorted-pair proofs, 21 tests, pinned JSON test vectors. |
| [`yieldstream-sdk`](https://github.com/yieldstream-org/yieldstream-sdk) | TypeScript | Client for building trees off-chain, generating proofs, and talking to the contract. Conformance-pinned to the contract's vectors. |
| [`yieldstream-app`](https://github.com/yieldstream-org/yieldstream-app) | Next.js / Tailwind | Protocol site, the [documentation site](https://yieldstream-protocol.vercel.app/docs), issuer dashboard, and the wallet-connected claim UI. |

They connect in this direction:

```
recipient CSV → yieldstream-sdk (build Merkle tree)
                      ↓  32-byte root + proofs
              yieldstream-contract (escrow + verify + pay out)
                      ↑
              yieldstream-app (issuer uploads, recipient claims)
```

The SDK's hashing core and the app's dashboard are deliberately unimplemented —
they are the work the contributor backlog exists to fund. The contract is the
finished piece and is deployed.

## Current state

Honest version, so nobody wastes a cycle on it:

- **Contract**: functional, testnet-deployed, full lifecycle exercised on-chain.
  **Not audited.** Do not custody real funds with it yet.
- **SDK**: scaffold with conformance tests written as the acceptance criteria.
  Core hashing is open contributor work.
- **App**: landing page live; dashboard and claim UI open contributor work.
- **Mainnet**: not deployed. Planned after a first external review.

## Contributing

Work is split into three complexity tiers, each worth a fixed reward in the
Drips Wave program:

| Tier | GitHub label | Typical scope |
|---|---|---|
| Trivial | [`tier-100`](https://github.com/yieldstream-org/yieldstream-contract/labels/tier-100) | Docs, a single validation check, UI copy, one test vector |
| Medium | [`tier-150`](https://github.com/yieldstream-org/yieldstream-contract/labels/tier-150) | A bounded feature inside one module |
| High | [`tier-200`](https://github.com/yieldstream-org/yieldstream-contract/labels/tier-200) | Storage redesign, cross-repo features, new subsystems |

Read an issue, comment to request assignment, then open a PR. Every repo runs
CI on the branch, and the review bar is stated in each issue's acceptance
criteria: `cargo fmt --check`, `clippy -D warnings`, and the full test suite for
Rust; typecheck, tests, and build for TypeScript.

Start here: [contract backlog](https://github.com/yieldstream-org/yieldstream-contract/issues) ·
[SDK backlog](https://github.com/yieldstream-org/yieldstream-sdk/issues) ·
[app backlog](https://github.com/yieldstream-org/yieldstream-app/issues)

Participation across all three repos is governed by the
[code of conduct](https://github.com/yieldstream-org/yieldstream-contract/blob/main/CODE_OF_CONDUCT.md).

## Maintainers

| | |
|---|---|
| Owner / lead | [@CHASE3020](https://github.com/CHASE3020) |
| Security reports | Use [private vulnerability disclosure](https://github.com/yieldstream-org/yieldstream-contract/security/advisories/new) — see each repo's `SECURITY.md` |
| Triage commitment | Respond to applications and PRs within 48h; review within 5 days |

## License

Dual MIT / Apache-2.0 across all three repositories.
