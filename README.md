# CMTAT Equivalency Assessment Criteria

## How to Use This Document
- Use the **CMTAT Function Equivalency Table** as the fillable assessment checklist.
- Use **Guideline for New Blockchain Implementations** as reference guidance when designing or mapping non-Solidity implementations.

## General Note
- The listed functionalities are the **minimal set** required for each module.

## CMTAT Function Equivalency Table (Extracted from source PDF)

### Metadata
- Implementation language: _(to be filled)_
- Version: _(to be filled)_

### Mandatory Attributes
| ID | Requirement | CMTAT Solidity corresponding feature | Notes | Present in implementation being approved (`y/n`) | Implementation details |
|---|---|---|---|---|---|
| 1.a | Name attribute | ERC20 `name` |  |  |  |
| 1.b | Ticker symbol attribute | ERC20 `symbol` |  |  |  |
| 1.c | Token ID attribute | `tokenId` |  |  |  |
| 1.d | Reference to legally required documentation | `terms` |  |  |  |
| 1.e | No fractions | ERC20 `decimals` | Decimals must be set to zero unless governing law permits fractions. |  |  |

### Mandatory Functions
| ID | Requirement | CMTAT Solidity corresponding feature | Notes | Present in implementation being approved (`y/n`) | Implementation details |
|---|---|---|---|---|---|
| 1.1 | Know total supply | ERC20 `totalSupply` |  |  |  |
| 1.2 | Know balance | ERC20 `balanceOf` |  |  |  |
| 1.3 | Transfer tokens | ERC20 `transfer` |  |  |  |
| 1.4 | Create tokens | `mint` / `batchMint` |  |  |  |
| 1.5 | Cancel tokens | `burn` / `batchBurn` / `burnFrom` | Use a dedicated issuer/authorized burn path for forced cancellation scenarios. |  |  |
| 1.6 | Pause tokens | `pause` | Pause must prevent all transfers until `unpause` is called. |  |  |
| 1.7 | Unpause tokens | `unpause` |  |  |  |
| 1.8 | Deactivate contract | `deactivateContract` | Must permanently disable the token (except in upgradeability patterns where deactivation behavior is explicitly defined). |  |  |
| 1.9 | Freeze | `freeze` or `setAddressFrozen(true)` *(inferred from extracted PDF text)* | Must block transfers to and from a given address. Single-function implementations are acceptable if they set a frozen status. |  |  |
| 1.10 | Unfreeze | `unfreeze` or `setAddressFrozen(false)` *(inferred from extracted PDF text)* | Single-function implementations are acceptable if they clear a frozen status. |  |  |

### Optional Functions

#### Snapshot
| ID | Requirement | CMTAT Solidity corresponding feature | Notes | Present in implementation being approved (`y/n`) | Implementation details |
|---|---|---|---|---|---|
| 1.11 | Schedule a snapshot | `scheduleSnapshot(uint256 time)` | SnapshotEngine `ISnapshotScheduler`. |  |  |
| 1.12 | Reschedule a snapshot | `rescheduleSnapshot(uint256 oldTime, uint256 newTime)` | `newTime` must stay between adjacent scheduled snapshots (not before previous / not after next). |  |  |
| 1.13 | Unschedule a snapshot | `unscheduleLastSnapshot(uint256 time)` / `unscheduleSnapshotNotOptimized(uint256 time)` | `unscheduleLastSnapshot` is restricted to the latest scheduled snapshot; `unscheduleSnapshotNotOptimized` supports generic unscheduling. |  |  |
| 1.14 | Snapshot time | `getAllSnapshots()` / `getNextSnapshots()` | Returns created snapshot times and pending scheduled times. |  |  |
| 1.15 | Snapshot total supply | `snapshotTotalSupply(uint256 time)` | `ISnapshotState`. |  |  |
| 1.16 | Snapshot balance | `snapshotBalanceOf(uint256 time, address tokenHolder)` | `ISnapshotState` (see also `snapshotInfo`). |  |  |

#### Access Control
| ID | Requirement | CMTAT Solidity corresponding feature | Notes | Present in implementation being approved (`y/n`) | Implementation details |
|---|---|---|---|---|---|
| 1.17 *(Mandatory for debt)* | Conditional transfer request | `RuleConditionalTransferLight.detectTransferRestriction(from, to, value)` / `detectTransferRestrictionFrom(spender, from, to, value)` and `approvedCount(from, to, value)` | Request is represented by a transfer restricted until approval count is non-zero. |  |  |
| 1.18 *(Mandatory for debt)* | Conditional transfer approval | `RuleConditionalTransferLight.approveTransfer(from, to, value)` (or `approveAndTransferIfAllowed`) | Approval is consumed on transfer via `transferred(...)`; cancellation via `cancelTransferApproval(...)`. |  |  |
| 1.19 | Assign to whitelist | CMTAT Allowlist: `setAddressAllowlist(account, status)`, `batchSetAddressAllowlist(accounts, status)`, `isAllowlisted(account)`; Rules whitelist: `addAddress`, `removeAddress`, `addAddresses`, `removeAddresses`, `isAddressListed` | CMTAT Allowlist and Rules whitelist are alternative whitelist implementations. |  |  |
| 1.20 | Grant role | `grantRole(bytes32 role, address account)` (OpenZeppelin AccessControl via CMTAT/Rules modules) | Used for roles such as `ALLOWLIST_ROLE`, `DEBT_ROLE`, `OPERATOR_ROLE`, `COMPLIANCE_MANAGER_ROLE`. |  |  |
| 1.21 | Revoke role | `revokeRole(bytes32 role, address account)` | AccessControl role removal. |  |  |
| 1.22 | Role attribution | `hasRole(bytes32 role, address account)` / `getRoleAdmin(bytes32 role)` | In CMTAT `AccessControlModule`, `DEFAULT_ADMIN_ROLE` is treated as having all roles in `hasRole`. |  |  |

#### Dividend

| ID | Requirement | CMTAT Solidity corresponding feature | Notes | Present in implementation being approved (`y/n`) | Implementation details |
|---|---|---|---|---|---|
| 1.23 | Distribution create parameters |  |  |  |  |
| 1.24 | Distribution set eligibility |  |  |  |  |
| 1.25 | Distribution set deposit |  |  |  |  |
| 1.26 | Distribution claim deposit |  |  |  |  |
| 1.27 | Distribution schedule |  |  |  |  |
| 1.28 | Distribution unschedule |  |  |  |  |

#### Credit Events
| ID | Requirement | CMTAT Solidity corresponding feature | Notes | Present in implementation being approved (`y/n`) | Implementation details |
|---|---|---|---|---|---|
| 1.29 | Flag as default | `setCreditEvents(CreditEvents)` -> `creditEvents().flagDefault` | Managed in `ICMTATCreditEvents.CreditEvents`. |  |  |
| 1.30 | Remove default flag | `setCreditEvents(CreditEvents)` with `flagDefault = false` | Same function as 1.29 with different value. |  |  |
| 1.31 | Flag as redeemed | `setCreditEvents(CreditEvents)` -> `creditEvents().flagRedeemed` | Managed in `ICMTATCreditEvents.CreditEvents`. |  |  |
| 1.32 | Set rating | `setCreditEvents(CreditEvents)` -> `creditEvents().rating` | Managed in `ICMTATCreditEvents.CreditEvents`. |  |  |

#### Enforcement
| ID | Requirement | CMTAT Solidity corresponding feature | Notes | Present in implementation being approved (`y/n`) | Implementation details |
|---|---|---|---|---|---|
| 1.33 | Enforce a transfer | `forcedTransfer(address from, address to, uint256 value)` | Enforcement transfer is performed via `forcedTransfer`. |  |  |
| 1.34 | Partial freeze | `freezePartialTokens(address account, uint256 value)` / `unfreezePartialTokens(address account, uint256 value)` | Intended only to block a sold amount to avoid double-spend during settlement. |  |  |

### Optional Attributes
| ID | Attribute | CMTAT Solidity corresponding feature | Notes | Present in implementation being approved (`y/n`) | Implementation details |
|---|---|---|---|---|---|
| 1.f | Guarantor identifier | `debt().debtIdentifier.guarantor` (set via `setDebt`) | Debt module (`ICMTATDebt.DebtIdentifier`). |  |  |
| 1.g | Debtholder representative identifier | `debt().debtIdentifier.debtHolder` (set via `setDebt`) | Debt module (`ICMTATDebt.DebtIdentifier`). |  |  |
| 1.h | Unique identifier / hash | `tokenId()` and `terms().doc.documentHash` | `tokenId` is base identifier; hash is in terms document metadata. |  |  |
| 1.i | Issuance date | `debt().debtInstrument.issuanceDate` (set via `setDebt` / `setDebtInstrument`) | Debt module (`ICMTATDebt.DebtInstrument`). |  |  |
| 1.j | Currency of payments | `debt().debtInstrument.currency` / `debt().debtInstrument.currencyContract` | Supports symbol-like string and token/asset contract address. |  |  |
| 1.k | Par value | `debt().debtInstrument.parValue` | Debt module (`uint256`). |  |  |
| 1.l | Minimum denomination | `debt().debtInstrument.minimumDenomination` | Debt module (`uint256`). |  |  |
| 1.m | Maturity date | `debt().debtInstrument.maturityDate` | Debt module (`string`). |  |  |
| 1.n | Interest rate | `debt().debtInstrument.interestRate` | Debt module (`uint256`). |  |  |
| 1.o | Coupon payment frequency | `debt().debtInstrument.couponPaymentFrequency` | Debt module (`string`). |  |  |
| 1.p | Interest schedule format: A) start date/end date/period; B) start date/end date/day of period; C) date 1/date 2/date 3 | `debt().debtInstrument.interestScheduleFormat` | Debt module (`string`). |  |  |
| 1.q | Interest payment date: A) period; B) specific date | `debt().debtInstrument.interestPaymentDate` | Debt module (`string`). |  |  |
| 1.r | Day count convention | `debt().debtInstrument.dayCountConvention` | Debt module (`string`). |  |  |
| 1.s | Business day convention | `debt().debtInstrument.businessDayConvention` | Debt module (`string`). |  |  |

## Guideline for New Blockchain Implementations

If you create a version for another blockchain, use this section to build a correspondence table between the CMTAT framework, the CMTAT Solidity version, and your implementation.

### Freeze

To be compatible with [ERC-3643](https://eips.ethereum.org/EIPS/eip-3643), freeze is implemented with a single function: `setAddressFrozen(targetAddress, frozenStatus)`.

For non-EVM blockchains, it can be clearer to separate this into two distinct functions:

```solidity
freeze(address targetAddress)
unfreeze(address targetAddress)
```

### CMTAT Extended

In the table below, the CMTAT framework extended features are mapped to Solidity features.

| CMTAT Functionalities | CMTAT Solidity corresponding features | CMTAT Allowlist | CMTAT Light | CMTAT Debt | CMTAT Standard |
|---|---|---|---|---|---|
| On-chain snapshot | `snapshotModule` and `snapshotEngine` | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> |
| Forced transfer | `forcedTransfer` | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> |
| Forced burn | `forcedBurn` | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> |
| Freeze partial token | `freezePartialTokens` / `unfreezePartialTokens` | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> |
| Integrated whitelisting/allowlisting | CMTAT Allowlist | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> |
| External whitelisting/allowlisting | CMTAT with rule whitelist | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> |
| RuleEngine / transfer hook | CMTAT with RuleEngine | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> |
| Upgradeability | CMTAT Upgradeable version | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> |
| Fee payer / gasless | CMTAT with ERC-2771 module | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #b00020;">&#x2718;</span></strong> | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> |

### Forced Burn and Forced Transfer

In the standard burn function, it is not possible to burn tokens from a frozen wallet. CMTAT offers `forcedTransfer` to force a transfer or a burn.

If `forcedTransfer` is not available, an alternative is to implement only `forcedBurn` (as in CMTAT Light). You can also implement both. In that case, it is suggested that only `forcedBurn` burns tokens, and `forcedTransfer` does not.

With the CMTAT Solidity version, when `forcedTransfer` is available, `forcedBurn` is not implemented to reduce contract code size. This limitation may not apply to other blockchains.

### Implementation Details

| Functionalities | CMTAT Solidity | Note |
|---|---|---|
| Mint while pause | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | Dedicated cross-chain mint (for example `crosschainMint`) cannot be performed while paused. |
| Burn while pause | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | Dedicated cross-chain burn (for example `crosschainBurn`) cannot be performed while paused. |
| Self-Burn for everyone | <strong><span style="color: #b00020;">&#x2718;</span></strong> | Token holders cannot burn their own tokens; only authorized addresses can burn. |
| Self-Burn for authorized addresses | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> |  |
| Standard burn on a frozen address | <strong><span style="color: #b00020;">&#x2718;</span></strong> | Requires `forcedTransfer` or `forcedBurn`. |
| Burn tokens with `forcedTransfer` | <strong><span style="color: #1e7e34;">&#x2714;</span></strong> | See notes above. |

### Self Burn

Only the issuer and authorized addresses (not the token holder) can burn a token in CMTAT Solidity, which reflects legal requirements in several jurisdictions.

Once issued, a security can only be cancelled by its issuer, not its holder. Since the token represents the security, the same rule applies. An investor who wants to exit should transfer to the issuer, who can then cancel when legally permitted.

You can still add self-burn in your version if it fits your legal or business context.
