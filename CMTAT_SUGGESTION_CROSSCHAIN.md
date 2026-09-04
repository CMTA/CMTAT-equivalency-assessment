# CMTAT Framework — suggested improvements: cross-chain transferability

## Purpose

This document suggests how the **CMTAT Framework functional specifications** (`doc/cmtat-framework-functional-specifications-june-2026.pdf`, June 2026 edition) could address cross-chain transferability.

It is a companion to [CMTAT_SUGGESTION.md](CMTAT_SUGGESTION.md), which carries the other suggestions, and it comes from the same source: the mapping work behind the [CMTAT Equivalency Assessment Criteria](README.md), whose own Cross-Chain Bridge Support section covers the implementation side of the same question. It is a suggestion list produced by this repository, not a CMTA publication.

## The gap

The framework does not mention cross-chain transfers at all, while the Solidity implementation ships an optional cross-chain module implementing [ERC-7802](https://eips.ethereum.org/EIPS/eip-7802) and the Chainlink CCIP administrative hooks. Multi-chain issuance is common enough that implementers are making these decisions today with no framework guidance.

The framework SHOULD add an optional **Cross-chain module**, stating at least:

- That cross-chain transferability is **not** a requirement of the framework, so that an implementation without it is not judged incomplete.
- The two acceptable models: **burn-and-mint** (tokens cancelled on the source chain, created on the destination chain) and **lock-and-mint** (tokens immobilized on the source chain, created on the destination chain). Neither needs to be mandated — what matters is that the same instrument is never represented twice across the two ledgers.
- For lock-and-mint, that the locked tokens MUST be treated as **not valid** for as long as they are locked: they MUST NOT be counted in the circulating supply, MUST NOT be transferable, and MUST NOT carry the rights of the instrument — no distribution, no entitlement from a snapshot, no vote. Without that, the locked tokens on the source chain and the newly created tokens on the destination chain both look valid, and the issued amount is represented twice.
- That the cross-chain entry points SHOULD be **dedicated functions restricted to the trusted bridge**, distinct from the issuer's mint and cancel functions, so that the bridge's authority can be granted and revoked without touching the issuance authority.
- That if an implementation instead reuses the standard mint and cancel functions for bridge operations, those functions MUST apply the same checks as a transfer — in particular the pause check. A bridge burn followed by a bridge mint is economically a transfer between two chains, so tokens would otherwise keep moving across chains while transfers are frozen on each of them.
- Which transfer checks apply on the bridge path (freeze, partial freeze, whitelist, validation rules), and that any skipped check MUST be documented.

## Qualify "know total supply" for multi-chain deployments

Functionality 1 (page 7) reads "any person may know the total number of tokens in circulation at any point in time". For a token deployed on several chains, a single contract only knows its **local** supply.

The framework SHOULD state that, where the same instrument is issued across several ledgers, the functionality is satisfied per ledger, and the issuer MUST be able to reconcile the aggregate — either off-chain or through a designated ledger of record.
