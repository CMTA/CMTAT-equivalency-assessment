# CMTAT Framework — suggested improvements: editorial corrections

## Purpose

This document lists editorial defects found in the **CMTAT Framework functional specifications** (`doc/cmtat-framework-functional-specifications-june-2026.pdf`, June 2026 edition), with a suggested correction for each. Page and section references are those of that PDF.

It is a companion to [CMTAT_SUGGESTION.md](CMTAT_SUGGESTION.md), which carries the substantive suggestions, alongside [CMTAT_SUGGESTION_CROSSCHAIN.md](CMTAT_SUGGESTION_CROSSCHAIN.md) and [CMTAT_SUGGESTION_PRIVACY.md](CMTAT_SUGGESTION_PRIVACY.md). It is a suggestion list produced by this repository, not a CMTA publication.

## Corrections

| Page | Section | Current text | Suggested |
|---|---|---|---|
| 6 | §3, last line | "projects on Ethereum **ad** EVM blockchains" | "and EVM blockchains" |
| 7 | Functionality 1 | "at any point **it** time" | "at any point in time" |
| 7 | Functionality 2 | "Know balance Each token holder may know" | Add the missing colon after "Know balance", as in every other entry |
| 7 | Functionality 4 | "capital increase **of** re-opening of a particular debt issuance" | "capital increase or re-opening" |
| 12 | Functionality 38 | "A possible use case of this **opional** function" | "optional" |
| 12 | Functionality 42, note | "consult with the **relavent** trading and settlement venues" | "relevant" |
| 13 | §4.1 | The snapshot engine appears twice in the same list — "the SnapshotEngine implementing the 'Snapshot' function" and "the **SnapShot**EngineModule to call a SnapShotEngine to perform on-chain snapshots" | Keep one entry, and use one spelling of "Snapshot" throughout (the repository name is `SnapshotEngine`) |
| 14 | §4.3 | "the Ethereum layer-2 protocol Aztec (https://aztec.network)**.** and enables confidential transactions" | Remove the stray full stop before "and" |

## Consistency points

These two are not typographical errors, but they make the document harder to read or to quote:

- §3 (page 6) refers to the modules as "Base module", while §4.1 (page 13) refers to "BaseModule" and "ERC20BaseModule", which are Solidity contract names. The chain-agnostic sections SHOULD use the functional names only, and leave implementation names to §4.
- The role table (page 6) defines the symbols used to identify the issuer and user roles, but the numbered functionalities that follow carry no such symbol in the extracted text. If the symbols are present in the layout, they SHOULD also be stated in words per functionality, so that the allocation survives copying, translation and text extraction.
