# CMTAT Framework — suggested improvements: cross-chain transferability

## Purpose

This document suggests how the **CMTAT Framework functional specifications** (`doc/cmtat-framework-functional-specifications-june-2026.pdf`, June 2026 edition) could address cross-chain transferability.

It is a companion to [CMTAT_SUGGESTION.md](CMTAT_SUGGESTION.md), which carries the other suggestions, and it comes from the same source: the mapping work behind the [CMTAT Equivalency Assessment Criteria](README.md), whose own Cross-Chain Bridge Support section covers the implementation side of the same question. It is a suggestion list produced by this repository, not a CMTA publication.

## The gap

The framework does not mention cross-chain transfers at all, while the Solidity implementation ships an optional cross-chain module implementing [ERC-7802](https://eips.ethereum.org/EIPS/eip-7802) and the Chainlink CCIP administrative hooks. Multi-chain issuance is common enough that implementers are making these decisions today with no framework guidance.

The framework SHOULD add an optional **Cross-chain module**, stating at least:

- That cross-chain transferability is **not** a requirement of the framework, so that an implementation without it is not judged incomplete.
- The two acceptable models: **burn-and-mint** (tokens cancelled on the source chain, created on the destination chain) and **lock-and-mint** (tokens immobilized on the source chain, created on the destination chain). Neither needs to be mandated — what matters is that the same instrument is never represented twice across the two ledgers.
- For lock-and-mint, that the locked tokens MUST be treated as **not valid** for as long as they are locked: they MUST NOT be transferable, and MUST NOT carry the rights of the instrument — no distribution, no entitlement from a snapshot, no vote. Without that, the locked tokens on the source chain and the newly created tokens on the destination chain both look valid, and the issued amount is represented twice.
- That the exclusion of the locked tokens applies to the **accounting of the instrument**, not necessarily to the total supply reported by the source ledger. Removing locked tokens from the on-chain total supply is not always practical — on some ledgers the lock is a transfer to a custody address, and the supply figure cannot distinguish it — so an implementation MAY keep counting them on-chain, provided the locked amount is readable and the issuer reconciles the valid amount across ledgers from it.
- That the cross-chain entry points SHOULD be **dedicated functions restricted to the trusted bridge**, distinct from the issuer's mint and cancel functions, so that the bridge's authority can be granted and revoked without touching the issuance authority.
- That if an implementation instead reuses the standard mint and cancel functions for bridge operations, those functions MUST apply the same checks as a transfer — in particular the pause check. A bridge burn followed by a bridge mint is economically a transfer between two chains, so tokens would otherwise keep moving across chains while transfers are frozen on each of them.
- Which transfer checks apply on the bridge path (freeze, partial freeze, whitelist, validation rules), and that any skipped check MUST be documented.

## Qualify "know total supply" for multi-chain deployments

Functionality 1 (page 7) reads "any person may know the total number of tokens in circulation at any point in time". For a token deployed on several chains, a single contract only knows its **local** supply.

The framework SHOULD state that, where the same instrument is issued across several ledgers, the functionality is satisfied per ledger, and the issuer MUST be able to reconcile the aggregate — either off-chain or through a designated ledger of record.

## Draft text for the framework

The text below is a draft that could be inserted in the framework as a new module. It is written in the framework's own register — lowercase "must", "may" and "should", numbered functionalities, a rationale followed by a list — rather than in the RFC 2119 style used elsewhere in this repository, so that it can be pasted in without rewriting. The numbers 53 to 55 continue the numbering used in [CMTAT_SUGGESTION.md](CMTAT_SUGGESTION.md), which proposes functionalities 43 to 52; if this module is adopted on its own, they become 43 to 45.

---

### § 3.2.7 Cross-chain module

Rationale: an issuer may wish to make the same instrument available on more than one distributed ledger, so that holders can hold and transfer their tokens on the ledger of their choice. Cross-chain transferability is not a requirement of this framework: a token issued on a single ledger satisfies it in full. Where an issuer chooses to support it, the arrangements below apply, so that the same instrument is never represented twice.

Two models are acceptable:

- **Cancel-and-create** (also called burn-and-mint): the tokens are cancelled on the source ledger, and the same number of tokens is created on the destination ledger.
- **Lock-and-create** (also called lock-and-mint): the tokens are immobilised on the source ledger, and the same number of tokens is created on the destination ledger. Tokens locked for this purpose must be treated as not valid for as long as they are locked: they must not be transferable, and they must not carry any right attached to the instrument, in particular no distribution, no entitlement determined by a snapshot, and no voting right.

Tokens that are locked are excluded from the number of tokens representing the instrument. That exclusion concerns the accounting of the instrument, which the issuer performs across all the ledgers concerned; it does not require the source ledger to exclude them from the total supply it reports, since the technical means of doing so may not exist. Where the locked tokens remain counted in the total supply reported by the source ledger, the amount locked must be readable, so that the number of valid tokens can be derived from it.

Under either model, the total number of valid tokens across all the ledgers on which the instrument is issued must at all times equal the number of tokens issued.

Functionalities:

53. **Cross-chain create**: create, on the destination ledger, the number of tokens that has been cancelled or locked on the source ledger.

54. **Cross-chain cancel**: cancel or lock, on the source ledger, the number of tokens that is to be created on the destination ledger.

55. **Know cross-chain authorisation**: for a particular CMTAT token, any person may know which addresses are authorised to call functionalities 53 and 54.

The following applies to the two functionalities above:

- They should be implemented as functions distinct from "create tokens" (functionality 4) and "cancel tokens" (functionality 5), reserved to the addresses that the issuer has designated as bridges. The authorisation of a bridge can then be granted and revoked without affecting the issuer's own authority to issue and cancel tokens.
- Where an implementation instead uses functionalities 4 and 5 themselves for cross-chain operations, those functions must apply the same restrictions as a transfer, and in particular the pause (functionality 6). A cancellation on one ledger followed by a creation on another is economically a transfer between two ledgers, and must not remain possible while transfers are paused.
- The issuer must document which transfer restrictions apply on the cross-chain path — freeze (functionality 12), partial freeze (functionality 38), whitelisting (functionality 23) and any other validation rule — and which do not.
- Functionality 1 (know total supply) is satisfied per ledger. Where the same instrument is issued across several ledgers, the issuer must be able to reconcile the aggregate number of tokens in circulation, either off chain or on a ledger designated as the ledger of record.

Roles: functionalities 53 and 54 are issuer functionalities, exercised by the bridge designated by the issuer. Functionality 55 may be called by any person.
