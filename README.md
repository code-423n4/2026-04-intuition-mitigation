# Intuition Mitigation Review
- Total Prize Pool: $4,000 in USDC
  - Warden awards: $3,000 in USDC
  - Judge awards: $1,000 in USDC
- [Warden guidelines for C4 mitigation reviews](https://code4rena.notion.site/Guidelines-for-C4-mitigation-reviews-ed10fc5cfbf640bd8dcec66f38b343c4)
- Starts April 2nd, 2026 20:00 UTC 
- Ends April 6th, 2026 20:00 UTC

## Important note 

Each warden must submit a mitigation review for *every* individual item listed in the `Scope` section below. **Incomplete or insufficient mitigation reviews will not be eligible for awards.**

## Overview of changes 

These mitigation PRs are intentionally narrow and should be reviewed only against the findings listed in `Scope`.

Key context by PR:

- **PR 143 (`S-149`, `S-145`)**
  - Scope is limited to `AtomWallet` signature-validation hardening.
  - `S-149`: 77-byte signatures now bind `(userOpHash, validUntil, validAfter)` into the signed digest so validity-window metadata cannot be tampered with by relayers/bundlers.
  - `S-145`: malformed / invalid signature paths now fail validation without reverting, aligning with intended ERC-4337 validation-failure semantics.
  - 65-byte signatures intentionally preserve existing “unbounded validity window” behavior.
  - Legacy 77-byte signatures that did not sign over timing metadata are intentionally no longer valid.

- **PR 144 (`S-112`, `S-595`)**
  - This PR is one combined mitigation set for the same reward-accounting family.
  - `S-112` is the primary root-cause fix: boundary-exclusive epoch accounting removes closed-epoch mutability at the boundary.
  - `S-595` is addressed both by that root-cause fix and by an explicit per-epoch claim-budget guardrail.
  - Review should focus on whether prior-epoch reward eligibility can still be changed at the epoch boundary and whether total claimed rewards can exceed epoch emissions.

- **PR 10 (`S-324`)**
  - Scope is limited to downstream ETH refund handling in `TrustSwapAndBridgeRouter`.
  - The change is intentionally minimal: the router can now accept ETH dust refunded by the downstream SwapRouter refund path.
  - Review should focus on whether swaps can still be made to revert through that refund path.
  - Broader integration/composability questions outside the listed finding are out of scope for this mitigation review.

General note:
- Parent-contest invalid / intended-behavior findings are not reopened here unless the mitigation itself introduces a new issue in scope.
- Please evaluate each PR against the listed finding(s) only, plus any directly introduced mitigation regressions.

## Scope

### Mitigation of Medium Severity Issues

Mitigations of all Medium issues listed here will be considered in-scope:

| Fix | Mitigation of | Notes | 
| ----------- | ------------- | ----------- |
| [PR 143](https://github.com/0xIntuition/intuition-contracts-v2/pull/143) | [S-149: Unsigned validity window metadata](https://code4rena.com/audits/2026-03-intuition/submissions/S-149) | same PR as S-145|
| [PR 144](https://github.com/0xIntuition/intuition-contracts-v2/pull/144) | [S-112: redacted - see below]](https://code4rena.com/audits/2026-03-intuition/submissions/S-112) | |
| [PR 144](https://github.com/0xIntuition/intuition-contracts-v2/pull/144) | [S-595: redacted - see below](https://code4rena.com/audits/2026-03-intuition/submissions/S-595) | same PR as S-112 |

### Additional scope to be reviewed

These are additional changes that will be in scope.

| Fix | Mitigation of | Notes | 
| ----------- | ------------- | ----------- |
| [PR 143](https://github.com/0xIntuition/intuition-contracts-v2/pull/143) | [S-145: AtomWallet's validateUserOp reverts on invalid Signatures, violating ERC-4337 and Enabling Griefing Attacks](https://code4rena.com/audits/2026-03-intuition/submissions/S-145) | same PR as S-149  | 
| [PR 10](https://github.com/0xIntuition/intuition-contracts-v2-periphery/pull/10) | [S-324: https://code4rena.com/audits/2026-03-intuition/submissions/S-324](https://code4rena.com/audits/2026-03-intuition/submissions/S-324) |  | 

## Out of Scope
All other issues arising from the Intuition audit are out of scope.

## Redacted submissions

| Submission ID | Gist | Notes |
| ------------- | ----------- | ----------- |
| [S-112](https://code4rena.com/audits/2026-03-intuition/submissions/S-112) | [Epoch Boundary Inclusion Allows Reward Dilution (Inclusive Epoch-End Snapshot Allows Retroactive Eligibility + Emissions Dilution)](https://gist.github.com/itsmetechjay/4bbd403b6331bb615a99dacf5cd63a77) |
| [S-595](https://code4rena.com/audits/2026-03-intuition/submissions/S-595)	| [Boundary claim ordering allows rewards overclaim beyond epoch budget in `TrustBonding`](https://gist.github.com/itsmetechjay/379745460df4a6d7ee69de9deb5a85dd) |
| [S-239](https://code4rena.com/audits/2026-03-intuition/submissions/S-239) | [Epoch-boundary checkpoints retroactively qualify for the previous epoch's rewards](https://gist.github.com/itsmetechjay/684323739146783b0c59f972a682ecb8) | Primary submission of group including S-112 and S-595 |
