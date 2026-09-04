# CMTAT Framework — suggested improvements

## Purpose

This document lists potential improvements to the **CMTAT Framework functional specifications** (`doc/cmtat-framework-functional-specifications-june-2026.pdf`, first published January 2022, updated November 2024, September 2025 and June 2026).

It is written from the work on the [CMTAT Equivalency Assessment Criteria](README.md): every criterion in that document had to be mapped from the framework to a concrete implementation, and the points below are the places where that mapping was ambiguous, incomplete, or contradicted by the reference implementation. It is a suggestion list produced by this repository, not a CMTA publication.

Each suggestion gives the current wording (with its section and page in the PDF), the gap, and a proposed change.

Three subjects have their own companion documents: [CMTAT_SUGGESTION_CROSSCHAIN.md](CMTAT_SUGGESTION_CROSSCHAIN.md) for cross-chain transferability, [CMTAT_SUGGESTION_PRIVACY.md](CMTAT_SUGGESTION_PRIVACY.md) for privacy and confidentiality, and [CMTAT_SUGGESTION_EDITORIAL.md](CMTAT_SUGGESTION_EDITORIAL.md) for the typographical and consistency corrections.

### What this was checked against

| Source | Version |
|---|---|
| CMTAT Framework functional specifications | June 2026 |
| CMTAT Equivalency Assessment Criteria (this repository) | `v0.3.0` |
| CMTAT Solidity | `v3.3.0-rc3` |
| RuleEngine | `v3.0.0-rc6` |
| Rules | `v0.6.0` |
| SnapshotEngine | `v0.5.0` |

## Table of Contents

- [1. Versioning](#1-versioning)
- [2. Validation module and transfer restrictions](#2-validation-module-and-transfer-restrictions)
- [3. Enforcement, cancellation and frozen addresses](#3-enforcement-cancellation-and-frozen-addresses)
- [4. Pause and deactivation semantics](#4-pause-and-deactivation-semantics)
- [5. Authorization module](#5-authorization-module)
- [6. Attributes and documents](#6-attributes-and-documents)
- [7. Batch operations and atomicity](#7-batch-operations-and-atomicity)
- [8. Events and auditability](#8-events-and-auditability)
- [9. Reference implementations](#9-reference-implementations)
- [10. Divergences with this repository's criteria](#10-divergences-with-this-repositorys-criteria)

## 1. Versioning

### 1.1 Add a "Know version" functionality

The numbered list of functionalities has no version functionality, yet §4.1 (page 13) states that the Solidity implementation has a "BaseModule which contains the smart contract version", and that implementation exposes `version()` through `IERC3643Version`.

An implementer reading only the framework has no reason to expose a version, and an assessor has no criterion to check. This repository had to add it as optional criterion 6 without a framework functionality to map it to.

The framework SHOULD add an optional functionality, worded chain-agnostically:

> **Know version**: for a particular CMTAT token, any person may know the version of the token implementation. This is the version of the code, not the version of the tokenized instrument nor the version of this framework.

It SHOULD also state the acceptable forms, since they differ per ledger: a constant returned by a read-only entry point; the chain-native contract or package metadata, where the target chain already versions deployed code; or a state variable restricted to an administrator, in which case the implementation MUST prevent the value from being desynchronized from the deployed code. For an upgradeable implementation, the version SHOULD be updated by the upgrade itself.

### 1.2 Give the framework document itself a version number

The document is identified only by publication dates ("Updated November 2024, September 2025 and June 2026"). Anything derived from it — an implementation, an assessment, a compliance report — can therefore only cite a month.

The framework SHOULD carry a [semantic version](https://semver.org/) on the cover in addition to the date, so that a filled assessment can record which edition it was produced against.

### 1.3 Make the functionality numbers stable across editions

Functionalities are numbered 1–42 sequentially across all modules, so inserting one functionality renumbers every following one. Any document citing "functionality 21" therefore silently changes meaning between editions.

Two options, either of which removes the problem:

- Number per module (`BASE-1`, `ENF-3`, `VAL-2`), so an insertion only affects the module it lands in.
- Keep global numbers but make them append-only: a new functionality takes the next free number wherever it belongs logically, and a withdrawn functionality leaves its number retired rather than reused.

The framework SHOULD also record, in each edition, which numbers changed meaning since the previous one. This repository had to publish exactly such a warning when inserting one criterion shifted 49 following IDs, which invalidated every assessment already filled.

## 2. Validation module and transfer restrictions

The Validation module (§3.2.2, page 10) has three functionalities: conditional transfer request, conditional transfer approve, and assign to whitelist. The reference stack offers considerably more, and the framework's silence leaves each of the following undefined.

### 2.1 Add a pre-flight "may this transfer proceed" query

Nothing in the framework lets a holder, a wallet, or a trading venue ask whether a transfer would be accepted before submitting it. The reference implementation exposes exactly that through ERC-1404 (`detectTransferRestriction`, `canTransfer` and their `…From` variants), and it is what a venue needs in order to avoid submitting a transaction that will revert.

The framework SHOULD add an optional functionality:

> **Know transfer restriction**: for a proposed transfer, any person may know whether it would be rejected, and for which reason.

It SHOULD also require the read path to be **non-reverting** — it answers a question rather than performing an operation — and require a machine-readable reason, plus a human-readable message for it.

### 2.2 State whether restrictions apply to creation and cancellation

The module speaks only of transfers. In practice a restriction may or may not screen the minter and the burner, and the rules in the reference stack differ from one another on precisely this point: some exempt creation and cancellation entirely, some block a blacklisted or sanctioned minter, and some (supply caps, per-minter quotas) act on creation only.

The framework SHOULD require each restriction to state its behaviour on creation and cancellation explicitly, rather than leaving it implied by the word "transfer".

### 2.3 Add the restriction families that exist in practice

"Assign to whitelist" is one restriction among many. The framework SHOULD list the families an implementer may need, without mandating any:

| Family | What it restricts |
|---|---|
| Whitelist / allowlist | Only listed addresses may send and receive; variants screen the receiver only, or the delegate of a delegated transfer only |
| Blacklist | Listed addresses may not participate |
| Sanctions screening | Participants are checked against an external sanctions source |
| Identity verification | Participants must be verified in an identity registry |
| Maximum total supply | Creation is capped at an issued amount |
| Reserve-backed cap | Creation is capped at reserves published by an external source |
| Maximum balance per address | No address may hold more than a set amount |
| Per-issuer creation quota | Each authorized minter has its own allowance |
| Conditional transfer | Each transfer must be approved beforehand (functionalities 21–22) |

**Blacklist and freeze are not interchangeable, and the framework SHOULD say which to use.** Freezing an address is already mandatory in the Enforcement module (functionalities 12–14), and it is the mechanism an issuer SHOULD use to block the funds recorded on an address: it lives in the token, the issuer controls it directly, and the frozen status is readable per address. A blacklist rule that reproduces the same decision inside the Validation module creates a second record of it, and the two can disagree — an address frozen on the token but absent from the list, or the reverse.

A blacklist earns its place in one case: when the list is a **contract shared by several tokens**. One listing decision then applies to every token that consults the list, so an issuer with a range of instruments, or several issuers sharing a compliance provider, does not have to repeat the freeze on each token and keep the copies in step. The trade-off is that the record of a blocked address then lives outside the token, and whoever administers the shared list can block transfers on every token pointing at it.

### 2.4 Require an explicit fail-open or fail-closed policy

A restriction backed by an external source (a sanctions oracle, an identity registry, a reserve feed) has to behave somehow when that source is unset, unavailable or stale. Both answers are defensible — reject every operation, or allow every operation — and the reference rules genuinely differ: an unset sanctions oracle allows everything, an empty aggregated whitelist rejects everything.

The framework SHOULD require the policy to be stated per restriction, since the legal consequence of guessing wrong is asymmetric.

### 2.5 Define the composition of several restrictions

When several restrictions apply to the same transfer, the framework says nothing about evaluation order or about which reason is reported. The reference engine returns the first non-zero code, so the order of the rules determines what a rejected holder is told.

The framework SHOULD require an implementation to document the order and the reported reason.

### 2.6 Add a "know whitelist status" functionality

The framework has "know pause status" (8) and "know frozen status" (14), but no equivalent for the whitelist, even though the same operational need exists: a holder needs to know whether they are listed before attempting a transfer. The reference implementations all expose it.

## 3. Enforcement, cancellation and frozen addresses

### 3.1 Say whether a frozen address can be cancelled from

Functionality 12 (page 9) prevents any token from being transferred to or from a frozen address. Functionality 5, "cancel tokens", is not qualified. Whether the issuer can cancel tokens held on a frozen address is therefore undefined — and it is precisely the case that matters, since an address is frozen exactly when a court order or a suspicion is being acted upon.

In the Solidity implementation the standard cancellation path refuses a frozen address, and a dedicated enforcement function is required instead. The framework SHOULD state the intended semantics and add the corresponding optional functionality:

> **Enforce a cancellation**: cancel tokens recorded on an address without the consent of the holder, including where that address is frozen.

The framework currently has "enforce a transfer" (37) but no enforced cancellation, so the only documented way to cancel from a frozen address is to enforce a transfer to the issuer and cancel there — which is a workaround worth either endorsing explicitly or replacing.

### 3.2 Reconcile "user-approved cancel" with issuer-only cancellation

Functionality 41 (page 12) states that "this functionality also allows token holders to cancel their own tokens". That is a substantive legal position — under several jurisdictions a security can only be cancelled by its issuer, not by its holder — and it is the opposite of the position taken by the Solidity implementation, where self-cancellation is not permitted by default.

The framework SHOULD separate the two capabilities it currently merges: a cancellation that the holder **authorizes** but the issuer **performs**, and a cancellation that the holder performs alone. It SHOULD note that the second is available only where the applicable law permits it.

## 4. Pause and deactivation semantics

Functionality 6 (page 7) leaves the interaction between pause and issuance to the issuer: "It is up to the issuer to decide whether token creation and deletion operations are also affected by the pause."

That is a reasonable degree of freedom, but it makes the pause status uninterpretable to a third party: a holder seeing a paused token cannot tell whether supply can still change. The framework SHOULD require the choice to be **documented and readable**, and SHOULD state the two cases where the answer is not free:

- Cross-chain creation and cancellation MUST be blocked while paused (see [CMTAT_SUGGESTION_CROSSCHAIN.md](CMTAT_SUGGESTION_CROSSCHAIN.md)).
- A pause that does not block creation lets the issuer dilute holders while they cannot transfer, which SHOULD be called out as a consequence the issuer accepts.

Functionality 9, "deactivate contract", requires tokens to be destroyed before or during deactivation, and states that the issuer can no longer create or cancel tokens afterwards. On ledgers where an account or contract cannot be removed, and in upgradeable deployments, "permanently and irreversibly" needs qualification: the framework SHOULD state what MUST be true after deactivation (no transfer, no creation, no cancellation, and the state readable and irreversible) rather than how it is achieved.

## 5. Authorization module

The Authorization module (§3.2.3, page 10) has grant role, revoke role, and role attribution. Three additions would reflect what implementations actually need:

- **Know role admin**: which role or account may grant and revoke a given role. Without it, "grant role" does not say who may call it.
- **Renounce a role**: an account dropping its own privileges. This matters for a bridge or a service account whose key is being retired, and it is the only role operation an account can perform on itself.
- A caution on **implicit super-roles**: in the Solidity implementation the default administrator is treated as holding every role. Any such arrangement MUST be documented, because a reader checking "who may freeze an address" will otherwise get an incomplete answer from the role assignments alone.

The framework SHOULD also recommend a **two-step transfer of the administrator role** (the new holder accepts before the old one loses control), since a one-step transfer to a wrong address is unrecoverable and permanently disables every issuer functionality.

## 6. Attributes and documents

### 6.1 Add document functionalities to the numbered list

The attributes list (page 8) requires a "reference to any legally required documentation", and §4.1 mentions a Document module calling an ERC-1643 document engine, but no numbered functionality covers documents. Since the legal link between the token and the instrument runs through those documents, they deserve the same treatment as the other attributes:

> **Set document** / **know document**: associate a named document with the token, as a reference and a hash allowing a reader to verify that the document has not been altered.

### 6.2 State that decimals are fixed after issuance

The framework requires decimals to be zero unless the applicable law allows fractions, and functionality 11 explains their display role. It does not say whether the value may change after issuance.

It MUST not: changing decimals retroactively reinterprets every recorded balance. The framework SHOULD state that decimals are set at issuance and immutable thereafter, and that a change of denomination is a corporate action carried out through cancellation and re-issuance.

### 6.3 Reconsider the optionality of the ticker symbol

The attributes list marks the ticker symbol as optional. Every reference implementation exposes it, wallets and venues rely on it, and this repository's criteria treat it as mandatory (criterion 2). Either the framework SHOULD make it mandatory, or the criteria SHOULD be relaxed — the two documents currently disagree (see §10).

## 7. Batch operations and atomicity

§2.1 (page 3) discusses the issuer's need to burn and mint atomically, and mentions a multicall function or a dedicated `burnAndMint` function. Nothing in the numbered list reflects this, so a conformant implementation may offer no way to do it.

The framework SHOULD add optional functionalities for **batch creation, batch cancellation and batch transfer**, and for an **atomic cancel-and-create**. Both are already present in the Solidity implementation, and on ledgers that batch natively the requirement is satisfied by the ledger rather than by the token — which is worth stating, since it is the kind of difference an assessment has to record.

## 8. Events and auditability

The framework describes readable state ("know total supply", "know frozen status") but never requires the token to **record who did what**. An audit trail is a legal requirement in most of the contexts the framework addresses, and on some ledgers it is not obtainable after the fact if the implementation did not emit it.

The framework SHOULD require that every issuer functionality records an entry identifying the operation, the acting account, the affected address, and the amount where applicable — as ledger events, logs, or whatever mechanism the target ledger provides.

## 9. Reference implementations

§4 (pages 12–14) lists the Ethereum, Tezos, Aztec and Solana implementations. Three improvements:

- **Add the engines and their versions.** The Solidity implementation is not one repository but five that are versioned separately (CMTAT, RuleEngine, Rules, SnapshotEngine, and the document and debt engines). A reader cannot tell which combination was validated together. A compatibility table would fix this, and each implementation already publishes the information.
- **Point to the equivalency assessment criteria.** The framework says that "additional implementations are encouraged" but gives no procedure for showing that a new implementation matches the framework. The criteria in this repository are that procedure, and §4 is where an implementer would look for it.
- **State the review status per implementation.** §4.2 notes that CMTA reviewed the Tezos FA2 implementation for compliance with the standard only, and that CMTA was not involved in the Ligo implementation. The other entries say nothing, so the absence of a statement cannot be distinguished from an endorsement. A one-line status for each entry (reviewed, audited, contributed, third-party) would make the list readable.

## 10. Divergences with this repository's criteria

These are places where the framework and the [CMTAT Equivalency Assessment Criteria](README.md) `v0.3.0` currently disagree. Each needs resolving on one side or the other.

Three of them are gaps in the criteria rather than in the framework: functionalities 8, 10 and 14 — know pause status, know deactivate status, know frozen status — are **mandatory** in the framework and have no criterion at all. The criteria cover the operations that change those states but never require the states to be readable, so an implementation could pass all 17 mandatory criteria while offering no way to find out whether the token is paused, deactivated, or an address frozen. Adding them would take the mandatory count from 17 to 20 and renumber every following criterion, so it belongs in a release that carries the renumbering warning.

| Point | Framework (June 2026) | This repository's criteria |
|---|---|---|
| Ticker symbol | Optional attribute (page 8) | Mandatory, criterion 2 |
| Version | Not a functionality | Optional, criterion 6 |
| Self-cancellation | Permitted by functionality 41 (page 12) | Not permitted by default; the Self-Burn section states that only the issuer and authorized addresses may cancel |
| Enforced cancellation | No functionality; only "enforce a transfer" (37) | Documented in Forced Burn and Forced Transfer, and in the Implementation Details table |
| Know pause status | **Mandatory** functionality 8 | No criterion |
| Know deactivate status | **Mandatory** functionality 10 | No criterion |
| Know frozen status | **Mandatory** functionality 14 | No criterion |
| Know active balance / frozen balance | Optional functionalities 39–40 | No criteria; only the partial freeze setters, criterion 19 |
| Know decimals | Mandatory functionality 11 where decimals are permitted | Criterion 4, worded as "no fractions" |
| Cross-chain | Absent | Documented as a non-criterion reference section |
| Restrictions beyond whitelisting | Absent | Documented as a non-criterion reference catalogue |
| Privacy | One note under functionality 14 | Documented as a non-criterion reference section |
