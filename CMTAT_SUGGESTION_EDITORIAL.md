# CMTAT Framework — suggested improvements: editorial corrections

## Purpose

This document covers the editorial side of the **CMTAT Framework functional specifications** (`doc/cmtat-framework-functional-specifications-june-2026.pdf`, June 2026 edition): how the document identifies itself and numbers its functionalities, and the typographical defects found in this edition. Page and section references are those of that PDF.

It is a companion to `CMTAT_SUGGESTION.md`, which carries the substantive suggestions, alongside `CMTAT_SUGGESTION_CROSSCHAIN.md` and `CMTAT_SUGGESTION_PRIVACY.md`. It is a suggestion list produced by this repository, not a CMTA publication.

## Give the document itself a version number

The document is identified only by publication dates ("Updated November 2024, September 2025 and June 2026"). Anything derived from it — an implementation, an assessment, a compliance report — can therefore only cite a month.

The framework SHOULD carry a [semantic version](https://semver.org/) on the cover in addition to the date, so that a filled assessment can record which edition it was produced against.

## Make the functionality numbers stable across editions

Functionalities are numbered 1–42 sequentially across all modules, so inserting one functionality renumbers every following one. Any document citing "functionality 21" therefore silently changes meaning between editions.

Two options, either of which removes the problem:

- Number per module (`BASE-1`, `ENF-3`, `VAL-2`), so an insertion only affects the module it lands in.
- Keep global numbers but make them append-only: a new functionality takes the next free number wherever it belongs logically, and a withdrawn functionality leaves its number retired rather than reused.

The framework SHOULD also record, in each edition, which numbers changed meaning since the previous one. This repository had to publish exactly such a warning when inserting one criterion shifted 49 following IDs, which invalidated every assessment already filled.

## Define the normative keywords

The document nowhere defines the words that carry its normative force. It uses "must" 19 times, "should" 8 times and "may" 29 times — the whole distinction between what an implementation has to do and what it may choose to do rests on them — and it cites neither [RFC 2119](https://www.rfc-editor.org/info/rfc2119) nor [RFC 8174](https://www.rfc-editor.org/info/rfc8174). None of the keywords is capitalised.

The framework SHOULD adopt the standard formulation, as this repository's criteria do, placing it in the introduction:

> The key words "MUST", "MUST NOT", "REQUIRED", "SHOULD", and "MAY" in this document are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/info/rfc2119) and [RFC 8174](https://www.rfc-editor.org/info/rfc8174).

RFC 8174 restricts that meaning to the **uppercase** forms, so adopting the paragraph means a pass over the document to capitalise each keyword that is normative and leave in lowercase those that are descriptive — "the issuer may wish to delegate", in § 2.7, describes a practice rather than granting a permission. That pass is worth making on its own account: it forces, sentence by sentence, the question a reader of a standard has to answer, which is whether a given statement is a requirement or an explanation.

The draft wording proposed in `CMTAT_SUGGESTION.md` and in its companions is written in lowercase, to match the document as it stands. If the framework adopts the formulation above, those drafts would be capitalised in the same pass.

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
