# CMTAT Equivalency Assessment Criteria

## Table of Contents

- [Document Version](#document-version)
- [How to Use This Document](#how-to-use-this-document)
- [General Note](#general-note)
- [Warning](#warning)
- [Summary](#summary)
  - [Scope of the count](#scope-of-the-count)
  - [Answer values](#answer-values)
  - [Compliance table](#compliance-table)
- [CMTAT Function Equivalency Table](#cmtat-function-equivalency-table)
  - [Metadata](#metadata)
  - [Token Attributes](#token-attributes)
    - [Token module](#token-module)
  - [Pause module (mandatory)](#pause-module-mandatory)
    - [Enforcement](#enforcement)
    - [Transfer restriction (optional)](#transfer-restriction-optional)
    - [Access Control](#access-control)
    - [Snapshot (optional)](#snapshot-optional)
    - [Dividend (optional)](#dividend-optional)
    - [Credit Events (optional)](#credit-events-optional)
  - [Debt (optional)](#debt-optional)
- [Guideline for New Blockchain Implementations](#guideline-for-new-blockchain-implementations)
  - [Freeze](#freeze)
  - [Version](#version)
  - [CMTAT Extended](#cmtat-extended)
  - [Forced Burn and Forced Transfer](#forced-burn-and-forced-transfer)
  - [Implementation Details](#implementation-details)
  - [Self-Burn](#self-burn)
  - [Cross-Chain Bridge Support](#cross-chain-bridge-support)
  - [Privacy and Confidentiality](#privacy-and-confidentiality)
- [Supplementary features](#supplementary-features)
- [Conclusion](#conclusion)
- [Reference](#reference)

## Document Version

Two distinct versions MUST be distinguished: the version of **this template**, as published by CMTA, and the version of the **filled assessment** produced for the implementation being approved.

| Version | Value |
|---|---|
| Template version — this document, as published by CMTA | `v0.2.0` |
| Assessment version — the filled document, set by its author |  |
| Template version this assessment was filled from |  |

A filled assessment SHOULD state both on a single line, for example:

> `v0.2.0` (this assessment), filled from CMTA assessment template `v0.2.0`

The two numbers are independent: a filled assessment MAY be revised — for example after a new release of the implementation being approved — without any change to the template, and a new template version MAY be published without the existing assessments being refilled.

Note:

- versions with the `rc` suffix are draft versions.
- version before `1.0` are also draft versions
- both notes above apply to the template version and to the assessment version.
- the template version an assessment was filled from MUST always be recorded: criteria IDs are sequential over the whole document and MAY be renumbered from one template version to the next, so an answer given against an earlier template cannot be read against a later one without checking the [changelog](CHANGELOG.md).

## How to Use This Document

- Fill the document in this order: **CMTAT Function Equivalency Table** first, then **Summary**, then **Conclusion**.
- Record both versions in **[Document Version](#document-version)** before starting: the version of the assessment being written, and the template version it is filled from.
- Use the **CMTAT Function Equivalency Table** as the fillable assessment checklist. Each criterion MUST be answered with `y`, `partial`, or `n` in the *Present in implementation being approved* column (see [Answer values](#answer-values)).
- Use the **Summary** to give the aggregated compliance result of the implementation being approved with the CMTAT standard.
- Use the **Conclusion** to describe, in broad terms, how the implementation being approved works and where it differs from CMTAT.
- Use **Guideline for New Blockchain Implementations** as reference guidance when designing or mapping non-Solidity implementations.

## General Note

- The listed functionalities are the **minimal set** required for each module.
- The key words "MUST", "MUST NOT", "REQUIRED", "SHOULD", and "MAY" in this document are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/info/rfc2119) and [RFC 8174](https://www.rfc-editor.org/info/rfc8174).

## Warning

An implementation MAY satisfy the CMTAT standard while still failing to meet the criteria required for tokenized shares under Swiss law at the underlying-ledger level. In particular, compliance with CMTAT does not, by itself, demonstrate that decentralization-related legal criteria are satisfied.

## Summary

> This section MUST be completed **after** the [CMTAT Function Equivalency Table](#cmtat-function-equivalency-table). It summarizes the compliance of the implementation being approved with the CMTAT standard.

### Scope of the count

The equivalency table contains **55 numbered criteria**:

| Category | Count | IDs |
|---|---:|---|
| Mandatory | 17 | 1–4, 7–11, 13–17, 23–25 |
| Optional | 38 | 5–6, 12, 18–22, 26–55 |

Each criterion MUST be counted exactly once. The non-numbered tables ([CMTAT Extended](#cmtat-extended), [Implementation Details](#implementation-details), [Cross-Chain Bridge Support](#cross-chain-bridge-support), [Privacy and Confidentiality](#privacy-and-confidentiality)) are **not** part of this count; they SHOULD be commented in the [Conclusion](#conclusion) instead.

### Answer values

Each criterion MUST be answered with exactly one of the following values in the *Present in implementation being approved* column:

| Value | Meaning |
|---|---|
| `y` | **Present** — an equivalent feature exists and covers the requirement, even if the name, the signature, or the chain-level mechanism differs from CMTAT Solidity. |
| `partial` | **Partial** — an equivalent feature exists but covers only a part of the requirement, or covers it with a restriction, a different access control model, or different semantics. |
| `n` | **Absent** — no equivalent feature is available in the implementation being approved. |

### Compliance table

| Answer         | Mandatory (17) | Optional (38) |
| -------------- | -------------: | ------------: |
| Present (`y`)  |                |               |
| Partial        |                |               |
| Absent (`n`)   |                |               |

Each column MUST sum to its total: 17 for mandatory criteria, 38 for optional criteria.

An implementation SHOULD be considered equivalent to CMTAT only if **no mandatory criterion is answered `n`**. Any mandatory criterion answered `partial` MUST be justified in the note below.

#### Note

> This subsection MUST explain the figures given in the compliance table, and in particular:
>
> - **every `partial` answer**: which part of the requirement is covered, which part is not, and why (chain-level limitation, legal or business choice, different access control model, feature planned for a later version); - **every mandatory `n` answer**: why the requirement cannot be met, and what compensating measure (if any) exists; - **optional modules left out by design**: when a whole optional module (for example Dividend, Debt, or Snapshot) is answered `n`, it SHOULD be stated once here rather than criterion by criterion.
>
> Example of an entry:
>
> > *Criterion 15 (Deactivate contract) — Partial: the implementation permanently blocks all transfers and mints, but the account itself cannot be deleted from the ledger, since the chain runtime does not allow it. The deactivated state is irreversible and publicly readable.*

<details><summary>Example of a filled compliance table</summary>

| Answer         | Mandatory (17) | Optional (38) |
| -------------- | -------------: | ------------: |
| Present (`y`)  |             14 |             3 |
| Partial        |              3 |             4 |
| Absent (`n`)   |              0 |            31 |

</details>

## CMTAT Function Equivalency Table

### Metadata

- Implementation language: _(to be filled)_
- Implementation version: _(to be filled)_

### Token Attributes

#### Mandatory
| ID | Requirement | CMTAT Solidity corresponding feature | Access Control (CMTAT Solidity) | Notes | Present in implementation being approved (`y/partial/n`) | Access Control (implementation being approved) | Implementation details |
|---|---|---|---|---|---|---|---|
| 1 | Name attribute | ERC20 `name` | Public (`view`) |  |  |  |  |
| 2 | Ticker symbol attribute | ERC20 `symbol` | Public (`view`) |  |  |  |  |
| 3 | Reference to legally required documentation | `terms` | Public (`view`) |  |  |  |  |
| 4 | No fractions | ERC20 `decimals` | Public (`view`) | - Decimals must be set to zero unless governing law permits fractions.<br />- CMTAT Solidity allows configurable decimals at deployment |  |  |  |

For CMTAT reference implementations, decimals SHOULD be configurable rather than defaulting to zero, to support use cases beyond tokenized shares in Switzerland.

##### Note

> This subsection can be used to detail how mandatory token attributes are implemented and to document specific legal, business, or chain-specific cases.

#### Optional
| ID | Requirement | CMTAT Solidity corresponding feature | Access Control (CMTAT Solidity) | Notes | Present in implementation being approved (`y/partial/n`) | Access Control (implementation being approved) | Implementation details |
|---|---|---|---|---|---|---|---|
| 5 | Token ID attribute | `tokenId` | Public (`view`) | Optional parameter. |  |  |  |
| 6 | Version attribute | `version()` (`IERC3643Version`, implemented by `VersionModule`) | Public (`view`) | Returns the version of the token implementation, for example `"3.2.0"`. In CMTAT Solidity the value is a constant of the contract code: it changes only through a new deployment or an upgrade, and it is not settable at runtime. |  |  |  |

For CMTAT reference implementations, `tokenId` and `version` SHOULD both be included.

##### Note

> This subsection can be used to detail optional token attributes implemented by the target system and to explain specific cases where an optional field is omitted or represented differently.



#### Token module

##### Mandatory

| ID | Requirement | CMTAT Solidity corresponding feature | Access Control (CMTAT Solidity) | Notes | Present in implementation being approved (`y/partial/n`) | Access Control (implementation being approved) | Implementation details |
|---|---|---|---|---|---|---|---|
| 7 | Know total supply | ERC20 `totalSupply` | Public (`view`) |  |  |  |  |
| 8 | Know balance | ERC20 `balanceOf` | Public (`view`) |  |  |  |  |
| 9 | Transfer tokens | ERC20 `transfer` | Token holder (`msg.sender`) |  |  |  |  |
| 10 | Create tokens | `mint` / `batchMint` | Role-restricted (issuer/minter authorized) |  |  |  |  |
| 11 | Cancel tokens | `burn` / `batchBurn` / `burnFrom` | Role-restricted (issuer/burner authorized) | Implementations SHOULD use a dedicated issuer/authorized burn path for forced cancellation scenarios. |  |  |  |
#### Optional

| ID   | Requirement | CMTAT Solidity corresponding feature            | Access Control (CMTAT Solidity) | Notes                                                        | Present in implementation being approved (`y/partial/n`) | Access Control (implementation being approved) | Implementation details |
|---|---|---|---|---|---|---|---|
| 12   | Approve     | ERC20 `approve(address spender, uint256 value)` | Token holder                    | Grants a delegate permission to transfer a specific amount of tokens from the token account. This is optional, but implementations SHOULD include it since secondary market capability may depend on delegated approval to automate trading and settlement for regulated entities. Issuers SHOULD consult relevant trading and settlement venues if listing is contemplated. |                                                  |                                                |                        |

##### Note

> This subsection can be used to detail how each mandatory function is implemented, including role model, execution flow, and specific chain-level behavior.

### Pause module (mandatory)

| ID   | Requirement         | CMTAT Solidity corresponding feature | Access Control (CMTAT Solidity)           | Notes                                                        | Present in implementation being approved (`y/partial/n`) | Access Control (implementation being approved) | Implementation details |
|---|---|---|---|---|---|---|---|
| 13   | Pause tokens        | `pause`                              | Role-restricted (pauser/admin authorized) | Pause must prevent all transfers until `unpause` is called.  |                                                  |                                                |                        |
| 14   | Unpause tokens      | `unpause`                            | Role-restricted (pauser/admin authorized) |                                                              |                                                  |                                                |                        |
| 15   | Deactivate contract | `deactivateContract`                 | Role-restricted (admin authorized)        | Must permanently disable the token (except in upgradeability patterns where deactivation behavior is explicitly defined). |                                                  |                                                |                        |

#### Enforcement

#### Mandatory

| ID   | Requirement | CMTAT Solidity corresponding feature                         | Access Control (CMTAT Solidity)               | Notes                                                        | Present in implementation being approved (`y/partial/n`) | Access Control (implementation being approved) | Implementation details |
|---|---|---|---|---|---|---|---|
| 16   | Freeze      | `freeze` or `setAddressFrozen(true)` *(inferred from extracted PDF text)* | Role-restricted (compliance/admin authorized) | Must block transfers to and from a given address. Single-function implementations are acceptable if they set a frozen status. |                                                  |                                                |                        |
| 17   | Unfreeze    | `unfreeze` or `setAddressFrozen(false)` *(inferred from extracted PDF text)* | Role-restricted (compliance/admin authorized) | Single-function implementations are acceptable if they clear a frozen status. |                                                  |                                                |                        |



#### Optional

| ID   | Requirement        | CMTAT Solidity corresponding feature                         | Access Control (CMTAT Solidity)                  | Notes                                                        | Present in implementation being approved (`y/partial/n`) | Access Control (implementation being approved) | Implementation details |
|---|---|---|---|---|---|---|---|
| 18   | Enforce a transfer | `forcedTransfer(address from, address to, uint256 value)`    | Role-restricted (operator/compliance authorized) | Enforcement transfer is performed via `forcedTransfer`.      |                                                  |                                                |                        |
| 19   | Partial freeze     | `freezePartialTokens(address account, uint256 value)` / `unfreezePartialTokens(address account, uint256 value)` | Role-restricted (operator/compliance authorized) | Intended only to block a sold amount to avoid double-spend during settlement. |                                                  |                                                |                        |



#### Transfer restriction (optional)

| ID   | Requirement                   | CMTAT Solidity corresponding feature                         | Access Control (CMTAT Solidity)                         | Notes                                                        | Present in implementation being approved (`y/partial/n`) | Access Control (implementation being approved) | Implementation details |
|---|---|---|---|---|---|---|---|
| 20   | Conditional transfer request  | `RuleConditionalTransferLight.detectTransferRestriction(from, to, value)` / `detectTransferRestrictionFrom(spender, from, to, value)` and `approvedCount(from, to, value)` | Public (`view`)                                         | Request is represented by a transfer restricted until approval count is non-zero. |                                                  |                                                |                        |
| 21   | Conditional transfer approval | `RuleConditionalTransferLight.approveTransfer(from, to, value)` (or `approveAndTransferIfAllowed`) | Role-restricted (compliance/approver authorized)        | Approval is consumed on transfer via `transferred(...)`; cancellation via `cancelTransferApproval(...)`. |                                                  |                                                |                        |
| 22   | Assign to whitelist           | CMTAT Allowlist: `setAddressAllowlist(account, status)`, `batchSetAddressAllowlist(accounts, status)`, `isAllowlisted(account)`; Rules whitelist: `addAddress`, `removeAddress`, `addAddresses`, `removeAddresses`, `isAddressListed` | Role-restricted for setters; public (`view`) for checks | CMTAT Allowlist and Rules whitelist are alternative whitelist implementations. |                                                  |                                                |                        |

##### Note

> This subsection can be used to detail the different transfer restrictions available.



#### Access Control

| ID   | Requirement      | CMTAT Solidity corresponding feature                         | Access Control (CMTAT Solidity)                 | Notes                                                        | Present in implementation being approved (`y/partial/n`) | Access Control (implementation being approved) | Implementation details |
|---|---|---|---|---|---|---|---|
| 23   | Grant role       | `grantRole(bytes32 role, address account)` (OpenZeppelin AccessControl via CMTAT/Rules modules) | Role admin (`DEFAULT_ADMIN_ROLE` or role admin) | Used for roles such as `ALLOWLIST_ROLE`, `DEBT_ROLE`, `OPERATOR_ROLE`, `COMPLIANCE_MANAGER_ROLE`. |                                                  |                                                |                        |
| 24   | Revoke role      | `revokeRole(bytes32 role, address account)`                  | Role admin (`DEFAULT_ADMIN_ROLE` or role admin) | AccessControl role removal.                                  |                                                  |                                                |                        |
| 25   | Role attribution | `hasRole(bytes32 role, address account)` / `getRoleAdmin(bytes32 role)` | Public (`view`)                                 | In CMTAT `AccessControlModule`, `DEFAULT_ADMIN_ROLE` is treated as having all roles in `hasRole`. |                                                  |                                                |                        |

##### Note

> This subsection can be used to detail the concrete authorization model (roles, admins, delegates, approvers) and implementation-specific exceptions. It MAY also be relevant to explain how access control works in the implementation being approved.

#### Snapshot (optional)
| ID | Requirement | CMTAT Solidity corresponding feature | Access Control (CMTAT Solidity) | Notes | Present in implementation being approved (`y/partial/n`) | Access Control (implementation being approved) | Implementation details |
|---|---|---|---|---|---|---|---|
| 26 | Schedule a snapshot | `scheduleSnapshot(uint256 time)` | Role-restricted (snapshot scheduler/admin authorized) | SnapshotEngine `ISnapshotScheduler`. |  |  |  |
| 27 | Reschedule a snapshot | `rescheduleSnapshot(uint256 oldTime, uint256 newTime)` | Role-restricted (snapshot scheduler/admin authorized) | `newTime` must stay between adjacent scheduled snapshots (not before previous / not after next). |  |  |  |
| 28 | Unschedule a snapshot | `unscheduleLastSnapshot(uint256 time)` / `unscheduleSnapshotNotOptimized(uint256 time)` | Role-restricted (snapshot scheduler/admin authorized) | `unscheduleLastSnapshot` is restricted to the latest scheduled snapshot; `unscheduleSnapshotNotOptimized` supports generic unscheduling. |  |  |  |
| 29 | Snapshot time | `getAllSnapshots()` / `getNextSnapshots()` | Public (`view`) | Returns created snapshot times and pending scheduled times. |  |  |  |
| 30 | Snapshot total supply | `snapshotTotalSupply(uint256 time)` | Public (`view`) | `ISnapshotState`. |  |  |  |
| 31 | Snapshot balance | `snapshotBalanceOf(uint256 time, address tokenHolder)` | Public (`view`) | `ISnapshotState` (see also `snapshotInfo`). |  |  |  |
##### Note

> This subsection can be used to detail snapshot scheduling and query behavior, including timing constraints and permission specifics.



#### Dividend (optional)

| ID | Requirement | CMTAT Solidity corresponding feature | Access Control (CMTAT Solidity) | Notes | Present in implementation being approved (`y/partial/n`) | Access Control (implementation being approved) | Implementation details |
|---|---|---|---|---|---|---|---|
| 32 | Distribution create parameters |  |  |  |  |  |  |
| 33 | Distribution set eligibility |  |  |  |  |  |  |
| 34 | Distribution set deposit |  |  |  |  |  |  |
| 35 | Distribution claim deposit |  |  |  |  |  |  |
| 36 | Distribution schedule |  |  |  |  |  |  |
| 37 | Distribution unschedule |  |  |  |  |  |  |
##### Note

> This subsection can be used to detail dividend/distribution workflow specifics and jurisdiction- or product-specific handling rules. No direct CMTAT Solidity equivalent is currently defined for these items; they are implementation-specific. However, a prototype is available on the CMTA GitHub organization: https://github.com/CMTA/IncomeVault

#### Credit Events (optional)
| ID | Requirement | CMTAT Solidity corresponding feature | Access Control (CMTAT Solidity) | Notes | Present in implementation being approved (`y/partial/n`) | Access Control (implementation being approved) | Implementation details |
|---|---|---|---|---|---|---|---|
| 38 | Flag as default | `setCreditEvents(CreditEvents)` -> `creditEvents().flagDefault` | Role-restricted (issuer/compliance/admin authorized) | Managed in `ICMTATCreditEvents.CreditEvents`. |  |  |  |
| 39 | Remove default flag | `setCreditEvents(CreditEvents)` with `flagDefault = false` | Role-restricted (issuer/compliance/admin authorized) | Same function as ID 38 with a different value. |  |  |  |
| 40 | Flag as redeemed | `setCreditEvents(CreditEvents)` -> `creditEvents().flagRedeemed` | Role-restricted (issuer/compliance/admin authorized) | Managed in `ICMTATCreditEvents.CreditEvents`. |  |  |  |
| 41 | Set rating | `setCreditEvents(CreditEvents)` -> `creditEvents().rating` | Role-restricted (issuer/compliance/admin authorized) | Managed in `ICMTATCreditEvents.CreditEvents`. |  |  |  |
##### Note

> This subsection can be used to detail how credit event states are updated, governed, and audited in the implementation being approved.



### Debt (optional)
| ID | Attribute | CMTAT Solidity corresponding feature | Access Control (CMTAT Solidity) | Notes | Present in implementation being approved (`y/partial/n`) | Access Control (implementation being approved) | Implementation details |
|---|---|---|---|---|---|---|---|
| 42 | Guarantor identifier | `debt().debtIdentifier.guarantor` (set via `setDebt`) | Read: public (`view`); write: role-restricted (`setDebt`) | Debt module (`ICMTATDebt.DebtIdentifier`). |  |  |  |
| 43 | Debtholder representative identifier | `debt().debtIdentifier.debtHolder` (set via `setDebt`) | Read: public (`view`); write: role-restricted (`setDebt`) | Debt module (`ICMTATDebt.DebtIdentifier`). |  |  |  |
| 44 | Unique identifier / hash | `tokenId()` and `terms().doc.documentHash` | Public (`view`) | `tokenId` is optional (implementations MAY omit it); document hash is in `terms` metadata. |  |  |  |
| 45 | Issuance date | `debt().debtInstrument.issuanceDate` (set via `setDebt` / `setDebtInstrument`) | Read: public (`view`); write: role-restricted (`setDebt*`) | Debt module (`ICMTATDebt.DebtInstrument`). |  |  |  |
| 46 | Currency of payments | `debt().debtInstrument.currency` / `debt().debtInstrument.currencyContract` | Read: public (`view`); write: role-restricted (`setDebt*`) | Supports symbol-like string and token/asset contract address. |  |  |  |
| 47 | Par value | `debt().debtInstrument.parValue` | Read: public (`view`); write: role-restricted (`setDebt*`) | Debt module (`uint256`). |  |  |  |
| 48 | Minimum denomination | `debt().debtInstrument.minimumDenomination` | Read: public (`view`); write: role-restricted (`setDebt*`) | Debt module (`uint256`). |  |  |  |
| 49 | Maturity date | `debt().debtInstrument.maturityDate` | Read: public (`view`); write: role-restricted (`setDebt*`) | Debt module (`string`). |  |  |  |
| 50 | Interest rate | `debt().debtInstrument.interestRate` | Read: public (`view`); write: role-restricted (`setDebt*`) | Debt module (`uint256`). |  |  |  |
| 51 | Coupon payment frequency | `debt().debtInstrument.couponPaymentFrequency` | Read: public (`view`); write: role-restricted (`setDebt*`) | Debt module (`string`). |  |  |  |
| 52 | Interest schedule format: A) start date/end date/period; B) start date/end date/day of period; C) date 1/date 2/date 3 | `debt().debtInstrument.interestScheduleFormat` | Read: public (`view`); write: role-restricted (`setDebt*`) | Debt module (`string`). |  |  |  |
| 53 | Interest payment date: A) period; B) specific date | `debt().debtInstrument.interestPaymentDate` | Read: public (`view`); write: role-restricted (`setDebt*`) | Debt module (`string`). |  |  |  |
| 54 | Day count convention | `debt().debtInstrument.dayCountConvention` | Read: public (`view`); write: role-restricted (`setDebt*`) | Debt module (`string`). |  |  |  |
| 55 | Business day convention | `debt().debtInstrument.businessDayConvention` | Read: public (`view`); write: role-restricted (`setDebt*`) | Debt module (`string`). |  |  |  |
##### Note

> This subsection can be used to detail supplementary attributes and to explain specific representation or governance choices made by the implementation being approved.



## Guideline for New Blockchain Implementations

If you create a version for another blockchain, use this section to build a correspondence table between the CMTAT framework, the CMTAT Solidity version, and your implementation.

### Freeze

> To be compatible with [ERC-3643](https://eips.ethereum.org/EIPS/eip-3643), freeze in CMTAT Solidity is implemented with a single function: `setAddressFrozen(targetAddress, frozenStatus)`. For non-EVM blockchains, implementations MAY separate this into two distinct functions:

```solidity
freeze(address targetAddress)
unfreeze(address targetAddress)
```

##### Note

> This subsection can be used to detail the choice made by the implementation being approved.



### Version

> CMTAT Solidity exposes the version of the implementation through the ERC-3643 function `version()`, provided by the `VersionModule`. The returned value is a compile-time constant (for example `"3.2.0"`) following [semantic versioning](https://semver.org/). It identifies the version of the token implementation; it is neither the version of the issued security nor the version of this assessment document.
>
> Versioning is an optional feature (criterion 6). Implementations on other blockchains MAY expose it differently:
>
> - as a constant returned by a read-only entry point, as in CMTAT Solidity; - through the chain-native contract or package metadata, when the target chain already versions the deployed code; - as a state variable restricted to an administrator role. In that case, the implementation MUST ensure that the value cannot be desynchronized from the deployed code, typically by writing it only in the initialization or upgrade path.
>
> For an upgradeable implementation, the version SHOULD be updated by the upgrade itself, so that an off-chain observer can always determine which code is live.

##### Note

> This subsection can be used to detail the choice made by the implementation being approved.



### CMTAT Extended

In the table below, the CMTAT framework extended features are mapped to Solidity features.

| CMTAT Functionalities | CMTAT Solidity corresponding features | CMTAT Allowlist | CMTAT Light | CMTAT Debt | CMTAT Standard | Present in implementation being approved (`y/partial/n`) | Implementation details |
|---|---|---|---|---|---|---|---|
| On-chain snapshot | `snapshotModule` and `snapshotEngine` | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> |  |  |
| Forced transfer | `forcedTransfer` | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> |  |  |
| Forced burn | `forcedBurn` | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> |  |  |
| Freeze partial token | `freezePartialTokens` / `unfreezePartialTokens` | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> |  |  |
| Integrated whitelisting/allowlisting | CMTAT Allowlist | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> |  |  |
| External whitelisting/allowlisting | CMTAT with rule whitelist | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> |  |  |
| RuleEngine / transfer hook | CMTAT with RuleEngine | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> |  |  |
| Upgradeability | CMTAT Upgradeable version | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> |  |  |
| Fee payer / gasless | CMTAT with ERC-2771 module | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> |  |  |

##### Note

> This section can be used to detail supplementary features implemented beyond the mandatory baseline and specific cases in the target chain. For non-EVM blockchains, it MAY be relevant to explain how gasless/gas sponsorship and upgradeability work in the particular blockchain targeted.

### Forced Burn and Forced Transfer

> In the standard burn function, tokens from a frozen wallet MUST NOT be burnable. CMTAT offers `forcedTransfer` to force a transfer or a burn.
>
> If `forcedTransfer` is not available, implementations MAY implement only `forcedBurn` (as in CMTAT Light). Implementations MAY also implement both. In that case, only `forcedBurn` SHOULD burn tokens, and `forcedTransfer` SHOULD NOT burn tokens.
>
> With the CMTAT Solidity version, when `forcedTransfer` is available, `forcedBurn` is not implemented to reduce contract code size. This limitation MAY not apply to other blockchains.

##### Note

> This subsection can be used to detail the choice made by the implementation being approved.

### Implementation Details

| Functionalities | CMTAT Solidity | Access Control (CMTAT Solidity) | Note | Present in implementation being approved (`y/partial/n`) | Access Control (implementation being approved) | Implementation details |
|---|---|---|---|---|---|---|
| Mint while pause | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | Role-restricted (minter/issuer authorized) | Dedicated cross-chain mint (for example `crosschainMint`) cannot be performed while paused. |  |  |  |
| Burn while pause | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | Role-restricted (burner/issuer authorized) | Dedicated cross-chain burn (for example `crosschainBurn`) cannot be performed while paused. |  |  |  |
| Self-Burn for everyone | <strong><span style="color: #b00020;">&#x2718;</span></strong> | Not permitted | Token holders cannot burn their own tokens; only authorized addresses can burn. |  |  |  |
| Self-Burn for authorized addresses | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | Role-restricted (authorized burner) |  |  |  |  |
| Standard burn on a frozen address | <strong><span style="color: #b00020;">&#x2718;</span></strong> | Not permitted in standard burn path | Requires `forcedTransfer` or `forcedBurn`. |  |  |  |
| Burn tokens with `forcedTransfer` | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | Role-restricted (operator/compliance authorized) | See notes above. |  |  |  |

### Self-Burn

Only the issuer and authorized addresses (not the token holder) can burn a token in CMTAT Solidity, which reflects legal requirements in several jurisdictions.

Once issued, a security can only be cancelled by its issuer, not its holder. Since the token represents the security, the same rule applies. An investor who wants to exit should transfer to the issuer, who can then cancel when legally permitted.

You MAY still add self-burn in your version if it fits your legal or business context.



### Cross-Chain Bridge Support

> **This feature is NOT part of the CMTAT specification directly.** Cross-chain transferability is not a requirement of the CMTAT standard and is not part of the equivalency criteria above. It is an **optional module offered by the CMTAT Solidity implementation**, documented here only as a reference so that new implementations MAY map equivalent functionality if they choose to support bridging. An implementation MAY be fully CMTAT-equivalent without providing any cross-chain capability.

CMTAT Solidity supports cross-chain bridging through a **burn-and-mint** model rather than lock-and-mint: tokens are burned on the source chain by an authorized bridge and minted on the destination chain by an authorized bridge. Two complementary standards are implemented in the optional cross-chain module:

- **[ERC-7802](https://eips.ethereum.org/EIPS/eip-7802)** — a minimal, bridge-agnostic interface for cross-chain mint and burn. This is the primitive that any compliant token bridge can call.
- **[Chainlink CCIP](https://docs.chain.link/ccip) (Cross-Chain Token / CCT standard)** — administrative hooks (`CCIPModule`) that let the token register with the CCIP token admin registry.

The cross-chain mint/burn entry points are **not** the standard `mint` / `burn` functions: they are dedicated functions restricted to the trusted bridge via a specific role, and they are blocked while the contract is paused (consistent with the *Mint while pause* / *Burn while pause* rows in [Implementation Details](#implementation-details)).

This distinction matters because, in CMTAT Solidity, the standard `mint` and `burn` remain available while the contract is paused: the pause check is carried by the authorization hook of each entry point (`_checkTokenBridge` is `whenNotPaused`, `_authorizeMint` is not), not by the shared mint/burn path. An implementation that does **not** provide dedicated cross-chain entry points, and instead reuses the standard `mint` and `burn` functions for bridge operations, SHOULD therefore apply the pause check to those functions when they are called on the bridge path, so that a cross-chain movement is subject to the same checks as a standard transfer. A bridge burn on the source chain followed by a bridge mint on the destination chain is economically a transfer between two chains: if the reused functions stay callable while the token is paused, tokens keep moving across chains while transfers are frozen on each of them. The same reasoning applies to the other transfer checks (freeze, partial freeze, allowlist, rule engine or transfer hook): the implementation SHOULD state which of them the bridge path enforces, and why any of them is skipped.

| Requirement | CMTAT Solidity corresponding feature | Access Control (CMTAT Solidity) | Notes | Present in implementation being approved (`y/partial/n`) | Access Control (implementation being approved) | Implementation details |
|---|---|---|---|---|---|---|
| Cross-chain mint (ERC-7802) | `crosschainMint(address to, uint256 value)` | Role-restricted (`CROSS_CHAIN_ROLE`, trusted token bridge); blocked while paused | Authenticates the bridge with `msg.sender` (not `_msgSender()`) so a relayer/forwarder cannot impersonate the bridge. Emits `CrosschainMint`. |  |  |  |
| Cross-chain burn (ERC-7802) | `crosschainBurn(address from, uint256 value)` | Role-restricted (`CROSS_CHAIN_ROLE`, trusted token bridge); blocked while paused | Does **not** require an ERC-20 allowance from `from`, following the Optimism Superchain ERC-20 and OpenZeppelin `ERC20Bridgeable` design. Emits `CrosschainBurn`. |  |  |  |
| Advertise ERC-7802 support | `supportsInterface(type(IERC7802).interfaceId)` | Public (`view`) | ERC-165 discovery so bridges can detect ERC-7802 compatibility. |  |  |  |
| Set CCIP admin | `setCCIPAdmin(address newAdmin)` | Role-restricted (`DEFAULT_ADMIN_ROLE`) | Chainlink CCIP (CCT) integration. The CCIP admin only registers the token with the CCIP token admin registry and has no other powers; 1-step transfer, `address(0)` revokes. |  |  |  |
| Get CCIP admin | `getCCIPAdmin()` | Public (`view`) | Returns the current CCIP admin. |  |  |  |

##### Note

> - The trusted bridge holds `CROSS_CHAIN_ROLE`. A bridge MAY `renounceRole` to drop its privileges; this only deprives it of cross-chain mint/burn and has no other effect, but such a bridge should then be considered compromised and not reused. - CMTAT Solidity also exposes related dedicated burn paths used alongside bridging — `burnFrom` (guarded by `BURNER_FROM_ROLE`) and self-`burn` (guarded by `BURNER_SELF_ROLE`) — which are likewise role-restricted and blocked while paused. - This subsection can be used to detail whether and how the implementation being approved supports bridging, which standard(s) or bridge(s) it targets, and the trust/role model applied to the bridge on the target chain. For non-EVM blockchains, ERC-7802 and CCIP may not be directly applicable; an equivalent burn-and-mint bridge model MAY be documented instead.



### Privacy and Confidentiality

> **This section is NOT part of the CMTAT specification and is NOT an equivalency criterion.** CMTAT Solidity targets public EVM chains, where all token state is readable by anyone. This section exists so that an implementation targeting a blockchain or a distributed ledger offering some level of privacy or confidentiality can document what stays public, what is hidden, and who is still able to read it.

On a public EVM chain, every element of the table below is public: contract storage is readable by any node even when a variable is declared `private` in Solidity, and every transfer, mint, burn, freeze, and allowlist update is visible in the transaction data and in the emitted events. An implementation on a privacy-enabled ledger MAY hide part of this state. It MUST then document how the hidden state is protected, and who can still read it — in particular whether the issuer retains the visibility required by the CMTAT features it claims (snapshot, dividend, forced transfer, freeze).

#### Visibility values

| Value | Meaning |
|---|---|
| `public` | Readable by anyone with access to the ledger, as in CMTAT Solidity. |
| `private` | Not readable from the ledger; only the holder of a specific right (private key, view key, role, node, disclosure credential) can read it. |
| `partial` | Partly hidden — for example the amount is encrypted but the participants are public, the value is visible only to the parties of the transaction, or it is disclosed only in aggregated or delayed form. |

#### Privacy table

| Data | Visibility in CMTAT Solidity | Visibility in the implementation being approved (`public/private/partial`) | Available to the issuer (`y/n`) | Other readers (role or address) | Implementation details |
|---|---|---|---|---|---|
| Balance of an address | `public` — `balanceOf` is a public view function and the balance mapping is readable from storage |  |  |  |  |
| Transfer amount | `public` — carried in the transaction calldata and in the `Transfer` event |  |  |  |  |
| Transfer participants (sender and recipient) | `public` — indexed `from` and `to` in the `Transfer` event |  |  |  |  |
| Total supply | `public` — `totalSupply` is a public view function; mint and burn are publicly traceable |  |  |  |  |
| Token decimals | `public` — `decimals` is a public view function |  |  |  |  |
| Frozen / blacklisted addresses | `public` — `isFrozen` / `getFrozenTokens` are public view functions and freeze operations emit events |  |  |  |  |
| Allowlisted / whitelisted addresses (if applicable) | `public` — `isAllowlisted` (CMTAT Allowlist) and `isAddressListed` (Rules whitelist) are public view functions |  |  |  |  |

#### Readers

> Typical readers to consider when filling the *Other readers* column. An implementation SHOULD list every case that applies, not only the most restrictive one:
>
> - **everyone** — the value is public on the ledger; - **the account holder** — an address can read its own balance and its own transfers, but not those of other holders; - **the counterparty of a transfer** — sender and recipient both see the amount, third parties do not; - **the issuer or an administrator role** — able to read all balances, typically because the corporate actions covered by this document (snapshot, dividend, forced transfer, freeze) require it; - **an auditor, a regulator, or a court-appointed third party** — granted a read credential (view key, decryption share, observer node) permanently or on request; - **the operator of the ledger or the validator nodes** — on a permissioned ledger the nodes hosting the data may see it even when the public cannot; - **nobody** — the value is recoverable only by the key holder, and is lost with the key.

##### Note

> This subsection is here to provide additional notes regarding privacy and confidentiality. It SHOULD describe **how privacy and confidentiality work on the particular blockchain targeted**, for example: encrypted balances under homomorphic encryption, a shielded pool with zero-knowledge proofs and view keys, confidential amounts with range proofs, per-participant data segregation on a permissioned ledger, or off-chain balances anchored on-chain by a commitment.
>
> It SHOULD also state the consequences for the CMTAT features: how the total supply can be audited when individual balances are hidden, how a snapshot or a dividend is computed on hidden balances, how the freeze and allowlist checks are enforced without revealing the lists, and what an issuer, an auditor, or a regulator has to do to obtain a disclosure.
>
> Privacy MUST NOT remove a mandatory capability: if the issuer cannot read a balance, the implementation MUST still explain how it performs the operations the mandatory criteria require on that balance.


## Supplementary features

> This section MAY be used to document supplementary features beyond the CMTAT standard that are present in the implementation being approved.

## Conclusion

> This section MUST describe, in broad terms, **how the implementation being approved works technically**, so that a reader who has not gone through the tables can understand the design and its main differences with the CMTAT specification. It SHOULD cover at least the following points:
>
> - **Token model**: the underlying token primitive of the target blockchain (for example ERC-20, SPL token, Soroban token interface, UTXO-based asset), and how balances, total supply, and decimals are represented. - **Architecture**: single contract or several modules/programs, how they are linked (inheritance, composition, external calls, on-chain registry), and the upgradeability strategy (proxy, native chain upgrade, immutable with redeployment). - **Access control model**: which roles exist, who holds the administrator role, how roles are granted and revoked, and how these roles map to the CMTAT roles. - **Transfer control flow**: which checks are applied on a transfer (pause, freeze, partial freeze, allowlist, rule engine or transfer hook), in which order, and where this logic lives (inside the token, in an external module, or in the chain runtime). - **Issuance and cancellation**: the mint and burn paths, forced transfer and forced burn, and the behaviour on a frozen address or while the contract is paused. - **Data and metadata storage**: how terms, `tokenId`, debt attributes, and credit events are stored (on-chain state, hash with off-chain document, or chain-native metadata). - **Main differences with the CMTAT Solidity implementation**, and the reason for each one (chain constraints, legal context, performance, code size). - **Known limitations** and features that are planned but not yet implemented.
>
> The [Summary](#summary) gives the counts; this section gives the technical explanation behind them and MUST stay consistent with it.

## Reference

Submodules used in this project and current checked-out versions:

| Submodule | Repository | Version | Commit |
|---|---|---|---|
| CMTAT | https://github.com/CMTA/CMTAT | `v3.2.0` | `49544f4de1993008acfc9e848d0bf03bd31d8579` |
| SnapshotEngine | https://github.com/CMTA/SnapshotEngine | `v0.3.0-1-g19e0b56` | `19e0b569bf5823aa8cec5760f080a932a9ac940e` |
| RuleEngine | https://github.com/CMTA/RuleEngine | `v3.0.0-rc2-2-g9c0aa70` | `9c0aa70aae08047e4062beab0f89f92bd60252c0` |
| Rules | https://github.com/CMTA/Rules | `v0.3.0` | `91c21c1191e84ff938892267ec443b0d1bb9efb0` |
