# CMTAT Framework — suggested improvements

## Purpose

This document lists potential improvements to the **CMTAT Framework functional specifications** (`doc/cmtat-framework-functional-specifications-june-2026.pdf`, first published January 2022, updated November 2024, September 2025 and June 2026).

It is written from the work on the CMTAT Equivalency Assessment Criteria (`README.md`): every criterion in that document had to be mapped from the framework to a concrete implementation, and the points below are the places where that mapping was ambiguous, incomplete, or contradicted by the reference implementation. It is a suggestion list produced by this repository, not a CMTA publication.

Each suggestion gives the current wording (with its section and page in the PDF), the gap, and a proposed change. Where the change is a change of wording, a **Draft text** block gives it in the framework's own register — lowercase "must", "may" and "should", numbered functionalities — so that it can be pasted in without rewriting.

New functionalities are numbered from 43 onward, continuing the current list of 42, in the order in which they appear in this document; the cross-chain companion continues the same numbering at 53.

Three subjects have their own companion documents: `CMTAT_SUGGESTION_CROSSCHAIN.md` for cross-chain transferability, `CMTAT_SUGGESTION_PRIVACY.md` for privacy and confidentiality, and `CMTAT_SUGGESTION_EDITORIAL.md` for the typographical and consistency corrections.

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

The numbered list of functionalities has no version functionality, yet §4.1 (page 13) states that the Solidity implementation has a "BaseModule which contains the smart contract version", and that implementation exposes `version()` through `IERC3643Version`.

An implementer reading only the framework has no reason to expose a version, and an assessor has no criterion to check. This repository had to add it as optional criterion 6 without a framework functionality to map it to.

The framework SHOULD add an optional functionality, worded chain-agnostically, and SHOULD state the acceptable forms, since they differ per ledger.

**Draft text** — a new functionality in § 3.2, optional functionalities:

> 43. **Know version**: for a particular CMTAT token, any person may know the version of the implementation of the token. This is the version of the code that operates the token; it is neither the version of the tokenised instrument nor the version of this framework.
>
> The version may be exposed as a constant returned by a read-only function, as part of the metadata that the ledger keeps for the deployed code, or as a value recorded in the state of the token and modifiable by the issuer. Where the value is modifiable, the implementation must ensure that it cannot become inconsistent with the code actually in force, for example by writing it only when the token is created or upgraded. Where the implementation is upgradeable, the version should be updated by the upgrade itself, so that any person can determine which code is in force.

## 2. Validation module and transfer restrictions

The Validation module (§3.2.2, page 10) has three functionalities: conditional transfer request, conditional transfer approve, and assign to whitelist. The reference stack offers considerably more, and the framework's silence leaves each of the following undefined.

### 2.1 Add a pre-flight "may this transfer proceed" query

Nothing in the framework lets a holder, a wallet, or a trading venue ask whether a transfer would be accepted before submitting it. The reference implementation exposes exactly that through ERC-1404 (`detectTransferRestriction`, `canTransfer` and their `…From` variants), and it is what a venue needs in order to avoid submitting a transaction that will revert.

The framework SHOULD add an optional functionality, and SHOULD require it to answer rather than fail, since a query that reverts cannot be used to avoid a failing transaction.

**Draft text** — a new functionality in the Validation module, § 3.2.2:

> 44. **Know transfer restriction**: for a proposed transfer, any person may know whether that transfer would be rejected, and for which reason.
>
> This function must not prevent or alter any transfer, and must return an answer rather than fail, so that it can be called before a transfer is submitted. The reason should be returned in a form that a machine can interpret, together with a description that can be displayed to a person.

### 2.2 State whether restrictions apply to creation and cancellation

The module speaks only of transfers. In practice a restriction may or may not screen the minter and the burner, and the rules in the reference stack differ from one another on precisely this point: some exempt creation and cancellation entirely, some block a blacklisted or sanctioned minter, and some (supply caps, per-minter quotas) act on creation only.

The framework SHOULD require each restriction to state its behaviour on creation and cancellation explicitly, rather than leaving it implied by the word "transfer".

**Draft text** — an addition to the Validation module, § 3.2.2:

> Each restriction must state whether it applies to the creation of tokens (functionality 4) and to their cancellation (functionality 5), in addition to transfers. A restriction that screens the parties to a transfer does not necessarily screen the account creating tokens or the address whose tokens are cancelled, and the arrangement adopted must be documented for each restriction used.

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

A blacklist earns its place in two cases. The first is when the list is a **contract shared by several tokens**: one listing decision then applies to every token that consults the list, so an issuer with a range of instruments, or several issuers sharing a compliance provider, does not have to repeat the freeze on each token and keep the copies in step. The trade-off is that the record of a blocked address then lives outside the token, and whoever administers the shared list can block transfers on every token pointing at it.

The second is **preventive**: a blacklist records the addresses that must never come to hold the tokens, and is maintained in advance of any relationship with them, while a freeze is an enforcement measure taken against an address that already holds tokens, on a suspicion or an order concerning that holder. A freeze does block incoming transfers as well, so the two overlap in effect; they differ in what they are for, and therefore in how many addresses each is expected to carry and in how the decision to list is taken.

**Draft text** — an addition to the Validation module, § 3.2.2:

> The rules referred to in functionality 23 may take various forms, of which the following are the most common. This list is informative: an issuer may use one of them, several of them, or others.
>
> - a whitelist, under which only the listed addresses may send and receive tokens; variants screen the recipient only, or the account authorised to transfer the tokens of another;
> - a blacklist, under which the listed addresses may not participate in a transfer;
> - the screening of the parties against a sanctions list maintained outside the token;
> - the verification of the parties in a register of identities;
> - a maximum number of tokens in circulation, which limits the creation of tokens;
> - a maximum number of tokens in circulation determined by reserves published outside the token;
> - a maximum number of tokens that a single address may hold;
> - an allowance granted to each account authorised to create tokens;
> - the approval of each transfer, as provided by functionalities 21 and 22.
>
> Freezing, under functionalities 12 and 13, is the means by which the issuer blocks the tokens recorded on an address of a given token, and should be used for that purpose: it is an enforcement measure directed at an address that already holds tokens.
>
> A blacklist is appropriate in two cases. The first is where the list is held outside the token and shared by several tokens, so that a single decision applies to all of them; the record of a blocked address then lies outside the token, and the person who administers the list can block transfers on every token that consults it. The second is where the issuer wishes to prevent addresses from acquiring the tokens before they hold any: such a list is maintained in advance and may concern addresses that never interact with the token.

### 2.4 Require an explicit fail-open or fail-closed policy

A restriction backed by an external source (a sanctions oracle, an identity registry, a reserve feed) has to behave somehow when that source is unset, unavailable or stale. Both answers are defensible — reject every operation, or allow every operation — and the reference rules genuinely differ: an unset sanctions oracle allows everything, an empty aggregated whitelist rejects everything.

The framework SHOULD require the policy to be stated per restriction, since the legal consequence of guessing wrong is asymmetric.

**Draft text** — an addition to the Validation module, § 3.2.2:

> Where a restriction relies on information held outside the token, such as a sanctions list, a register of identities or a figure for reserves, the implementation must state how the restriction behaves where that source is not set, is unavailable, or returns information that is out of date. Rejecting every operation and allowing every operation are both acceptable, provided that the arrangement adopted is documented.

### 2.5 Define the composition of several restrictions

When several restrictions apply to the same transfer, the framework says nothing about evaluation order or about which reason is reported. The reference engine returns the first non-zero code, so the order of the rules determines what a rejected holder is told.

The framework SHOULD require an implementation to document the order and the reported reason.

**Draft text** — an addition to the Validation module, § 3.2.2:

> Where several restrictions apply to the same transfer, the implementation must document the order in which they are evaluated, and which reason is reported where more than one restriction would reject the transfer.

### 2.6 Add a "know whitelist status" functionality

The framework has "know pause status" (8) and "know frozen status" (14), but no equivalent for the whitelist, even though the same operational need exists: a holder needs to know whether they are listed before attempting a transfer. The reference implementations all expose it.

**Draft text** — a new functionality in the Validation module, § 3.2.2:

> 45. **Know whitelist status**: for a particular address, the issuer and the holder of that address may know whether the address is included in a whitelist. On a public ledger, that information is available to any person.

## 3. Enforcement, cancellation and frozen addresses

### 3.1 Say whether a frozen address can be cancelled from

Functionality 12 (page 9) prevents any token from being transferred to or from a frozen address. Functionality 5, "cancel tokens", is not qualified. Whether the issuer can cancel tokens held on a frozen address is therefore undefined — and it is precisely the case that matters, since an address is frozen exactly when a court order or a suspicion is being acted upon.

In the Solidity implementation the standard cancellation path refuses a frozen address, and a dedicated enforcement function is required instead. The framework currently has "enforce a transfer" (37) but no enforced cancellation, so the only documented way to cancel from a frozen address is to enforce a transfer to the issuer and cancel there — a workaround worth either endorsing explicitly or replacing.

**Draft text** — a new functionality in the optional functionalities of the Enforcement module, § 3.2.6:

> 46. **Enforce a cancellation**: cancel a given number of tokens recorded on a given ledger address without the consent of the holder, including where that address is frozen.
>
> This function may be used to comply with an order of a judicial authority, and where the tokens to be cancelled are recorded on an address frozen under functionality 12. Unless the implementation states otherwise, the cancellation under functionality 5 does not apply to a frozen address.

### 3.2 Reconcile "user-approved cancel" with issuer-only cancellation

Functionality 41 (page 12) states that "this functionality also allows token holders to cancel their own tokens". That is a substantive legal position — under several jurisdictions a security can only be cancelled by its issuer, not by its holder — and it is the opposite of the position taken by the Solidity implementation, where self-cancellation is not permitted by default.

The framework SHOULD separate the two capabilities it currently merges: a cancellation that the holder **authorizes** but the issuer **performs**, and a cancellation that the holder performs alone. It SHOULD note that the second is available only where the applicable law permits it.

**Draft text** — a replacement for functionality 41:

> 41. **User-approved cancellation**: cancel tokens recorded on the address of a holder who has authorised that cancellation. The cancellation is performed by the issuer.
>
> Where the law governing the tokenised instrument permits it, an implementation may also allow a holder to cancel its own tokens without the involvement of the issuer. Where it does not, only the issuer and the persons authorised by it may cancel tokens, since a security may be cancelled only by its issuer. The arrangement adopted must be documented.

## 4. Pause and deactivation semantics

Functionality 6 (page 7) leaves the interaction between pause and issuance to the issuer: "It is up to the issuer to decide whether token creation and deletion operations are also affected by the pause."

That is a reasonable degree of freedom, but it makes the pause status uninterpretable to a third party: a holder seeing a paused token cannot tell whether supply can still change. The framework SHOULD require the choice to be **documented and readable**, and SHOULD state the two cases where the answer is not free:

- Cross-chain creation and cancellation MUST be blocked while paused (see `CMTAT_SUGGESTION_CROSSCHAIN.md`).
- A pause that does not block creation lets the issuer dilute holders while they cannot transfer, which SHOULD be called out as a consequence the issuer accepts.

Functionality 9, "deactivate contract", requires tokens to be destroyed before or during deactivation, and states that the issuer can no longer create or cancel tokens afterwards. On ledgers where an account or contract cannot be removed, and in upgradeable deployments, "permanently and irreversibly" needs qualification: the framework SHOULD state what MUST be true after deactivation rather than how it is achieved. In an upgradeable deployment it MUST also say that deactivating the token is not sufficient on its own, since an upgrade can restore the functions it disabled — the ability to upgrade has to go with it.

**Draft text** — an addition to functionality 6:

> The issuer must document whether the creation and the cancellation of tokens remain possible while transfers are paused, and any person must be able to determine which of the two arrangements applies. Where creation remains possible, the issuer accepts that the number of tokens in circulation may increase while holders are unable to transfer them. Where the token can be transferred to another ledger, the operations described in the Cross-chain module must be blocked while transfers are paused.

**Draft text** — an addition to functionality 9:

> After deactivation, no token may be transferred, created or cancelled, and any person may know that the token has been deactivated. Deactivation cannot be reversed. Where the ledger does not permit the account or the code to be removed, a state satisfying these conditions is sufficient.
>
> Where the code that operates the token can be upgraded, deactivation is not irreversible by itself, since an upgrade can restore the functions that deactivation disabled. The issuer must therefore also remove the ability to upgrade the code, at the latest when the token is deactivated. Depending on the ledger, this may be done on the ledger, by transferring the right to upgrade to an address from which it cannot be exercised — the address zero on Ethereum and other EVM ledgers — or outside the ledger, by destroying the key that controls that right. The measure taken must be documented, and where it is taken outside the ledger the issuer must be able to evidence it, since a person examining the ledger cannot verify it.

## 5. Authorization module

The Authorization module (§3.2.3, page 10) has grant role, revoke role, and role attribution. Three additions would reflect what implementations actually need:

- **Know role admin**: which role or account may grant and revoke a given role. Without it, "grant role" does not say who may call it.
- **Renounce a role**: an account dropping its own privileges. This matters for a bridge or a service account whose key is being retired, and it is the only role operation an account can perform on itself.
- A caution on **implicit super-roles**: in the Solidity implementation the default administrator is treated as holding every role. Any such arrangement MUST be documented, because a reader checking "who may freeze an address" will otherwise get an incomplete answer from the role assignments alone.

The framework SHOULD also recommend a **two-step transfer of the administrator role** (the new holder accepts before the old one loses control), since a one-step transfer to a wrong address is unrecoverable and permanently disables every issuer functionality.

**Draft text** — two new functionalities in the Authorization module, § 3.2.3:

> 47. **Know role administration**: for a given role, the issuer, the persons it has authorised and the account holding that role may know which role or which account may grant and revoke it. On a public ledger, that information is available to any person.
>
> 48. **Renounce a role**: an account may renounce a role that it holds.
>
> Where an account holds a role by virtue of holding another one — for instance where an administrator is treated as holding every role — the implementation must document it, since the roles granted would otherwise give an incomplete picture of who may call a given function. The transfer of the administrator role should require the new holder to accept it before the previous holder loses it, as an administrator role transferred to an address that cannot use it cannot be recovered, and every issuer functionality would then be permanently unavailable.

## 6. Attributes and documents

### 6.1 Add document functionalities to the numbered list

The attributes list (page 8) requires a "reference to any legally required documentation", and §4.1 mentions a Document module calling an ERC-1643 document engine, but no numbered functionality covers documents. Since the legal link between the token and the instrument runs through those documents, they deserve the same treatment as the other attributes:

**Draft text** — two new functionalities in § 3.2, optional functionalities:

> 49. **Set document**: associate a document with the token, by a name, a reference allowing the document to be obtained, and a hash of its content.
>
> 50. **Know document**: for a particular CMTAT token, any person may know the documents associated with the token, their reference and their hash.
>
> The hash allows a reader to verify that the document obtained is the document that the issuer associated with the token. These functionalities may be used for the documentation referred to in the attributes applicable to all CMTAT tokens.
>
> A separate hash is not required where the document is held in a store in which the reference is itself derived from the content, such as a content-addressed system of the IPFS kind or a decentralised storage network of the Walrus kind. In that case the reference identifies one content and no other, and a document that has been altered is no longer obtainable under that reference. The issuer must still ensure that the document remains obtainable for as long as the instrument exists, since a reference of that kind establishes what the document is, not that a copy of it is still available.

### 6.2 State whether decimals may change after issuance

The framework requires decimals to be zero unless the applicable law allows fractions, and functionality 11 explains their display role. It does not say whether the value may change after issuance.

It SHOULD not, since changing decimals retroactively reinterprets every balance already recorded. The framework SHOULD state that decimals are set at issuance and SHOULD NOT change afterwards, that where a change is nevertheless required the holders MUST be informed before it takes effect, and that a change of denomination MAY instead be carried out through cancellation and re-issuance.

**Draft text** — an addition to functionality 11:

> The number of decimals is set when the token is created and should not change thereafter, as a change alters the meaning of every balance already recorded without any token being transferred, created or cancelled.
>
> Where a change is nevertheless required, it must be made with the knowledge of the token holders: the issuer must inform them before the change takes effect, so that no holder acts on a figure whose meaning has changed, and must record the change as it records a corporate action. A change of denomination may also be carried out by cancelling the tokens and creating new ones, which leaves the meaning of the existing balances untouched.

### 6.3 Reconsider the optionality of the ticker symbol

The attributes list marks the ticker symbol as optional. Every reference implementation exposes it, wallets and venues rely on it, and this repository's criteria treat it as mandatory (criterion 2). Either the framework SHOULD make it mandatory, or the criteria SHOULD be relaxed — the two documents currently disagree (see §10).

**Draft text** — in the attributes applicable to all CMTAT tokens, page 8, replace "Ticker symbol (optional)" with:

> • Ticker symbol

If the attribute is to remain optional, the following sentence would at least record the practice:

> Where the token is intended to be held in a wallet or admitted to trading, a ticker symbol should be set.

## 7. Batch operations and atomicity

§2.1 (page 3) discusses the issuer's need to burn and mint atomically, and mentions a multicall function or a dedicated `burnAndMint` function. Nothing in the numbered list reflects this, so a conformant implementation may offer no way to do it.

The framework SHOULD add optional functionalities for **batch creation, batch cancellation and batch transfer**, and for an **atomic cancel-and-create**. Both are already present in the Solidity implementation, and on ledgers that batch natively the requirement is satisfied by the ledger rather than by the token — which is worth stating, since it is the kind of difference an assessment has to record.

**Draft text** — two new functionalities in § 3.2, optional functionalities:

> 51. **Batch operations**: create, cancel or transfer tokens for several ledger addresses in a single operation.
>
> 52. **Atomic cancellation and creation**: cancel tokens and create tokens in a single operation, so that neither takes effect without the other.
>
> Where the ledger allows several operations to be grouped in a single transaction, as described in § 2.1, these functionalities may be satisfied by that mechanism rather than by functions of the token.

## 8. Events and auditability

The framework describes readable state ("know total supply", "know frozen status") but never requires the token to **record who did what**. An audit trail is a legal requirement in most of the contexts the framework addresses, and on some ledgers it is not obtainable after the fact if the implementation did not emit it.

The framework SHOULD require that every issuer functionality records an entry identifying the operation, the acting account, the affected address, and the amount where applicable — as ledger events, logs, or whatever mechanism the target ledger provides.

**Draft text** — an addition to § 3, before the list of mandatory functionalities:

> Every functionality exercised by the issuer must leave a record on the ledger identifying the operation performed, the account that called it, the address affected and, where applicable, the number of tokens concerned. Where the ledger provides a mechanism for events or logs, that mechanism should be used. Where the data recorded is confidential, the record must remain available to the persons who are entitled to read that data.

## 9. Reference implementations

§4 (pages 12–14) lists the Ethereum, Tezos, Aztec and Solana implementations. Three improvements:

- **Add the engines and their versions.** The Solidity implementation is not one repository but five that are versioned separately (CMTAT, RuleEngine, Rules, SnapshotEngine, and the document and debt engines). A reader cannot tell which combination was validated together. A compatibility table would fix this, and each implementation already publishes the information.
- **Point to the equivalency assessment criteria.** The framework says that "additional implementations are encouraged" but gives no procedure for showing that a new implementation matches the framework. The criteria in this repository are that procedure, and §4 is where an implementer would look for it.
- **State the review status per implementation.** §4.2 notes that CMTA reviewed the Tezos FA2 implementation for compliance with the standard only, and that CMTA was not involved in the Ligo implementation. The other entries say nothing, so the absence of a statement cannot be distinguished from an endorsement. A one-line status for each entry (reviewed, audited, contributed, third-party) would make the list readable.

**Draft text** — an addition to § 4, before § 4.1:

> Each implementation listed below is identified by the version of its components, since an implementation may consist of several elements published separately, and by its status: whether it was developed by CMTA, reviewed by CMTA for compliance with this framework, or referred to without CMTA having been involved in its development.
>
> An implementation of this framework on another ledger may be assessed against the CMTAT Equivalency Assessment Criteria, published by CMTA alongside this framework, which set out the functionalities to be mapped and the form in which the result is recorded.

## 10. Divergences with this repository's criteria

These are places where the framework and the CMTAT Equivalency Assessment Criteria (`README.md`) `v0.3.0` currently disagree. Each needs resolving on one side or the other.

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
