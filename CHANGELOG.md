# Changelog

All notable changes to this document will be recorded here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [0.3.0] - 2026-09-03

### Added

- Summary section (before the equivalency table) giving the aggregated compliance of the implementation being approved with CMTAT: scope of the count (17 mandatory / 38 optional criteria), allowed answer values (`y` / `partial` / `n`), compliance table to fill, and a note requiring every `partial` and every mandatory `n` to be explained
- Cross-Chain Bridge Support section (ERC-7802 and Chainlink CCIP), documented as an optional module outside the equivalency criteria
  - Guidance for implementations that reuse the standard `mint` and `burn` functions for bridge operations instead of dedicated cross-chain entry points: those functions SHOULD apply the pause check on the bridge path, so a cross-chain movement is subject to the same checks as a standard transfer, and the implementation SHOULD state which other transfer checks (freeze, partial freeze, allowlist, rule engine) the bridge path enforces
- `CMTAT_SUGGESTION.md`: suggested improvements to the CMTAT Framework functional specifications (June 2026 edition), written from the mapping work behind the criteria
  - Ten groups of suggestions, each giving the current wording with its section and page, the gap, and a proposed change: versioning, transfer restrictions, enforcement and cancellation, pause and deactivation, roles, attributes and documents, batch operations, auditability, reference implementations, and divergences with these criteria
  - Records that framework functionalities 8, 10 and 14 (know pause status, know deactivate status, know frozen status) are mandatory in the framework and have no criterion in this document, so an implementation could pass all 17 mandatory criteria while offering no way to read those states
- `CMTAT_SUGGESTION_EDITORIAL.md`: companion suggestion document listing the typographical defects found in the framework specifications, with the page, the current text and a suggested correction for each, plus two consistency points on module naming and on the role symbols
- `CMTAT_SUGGESTION_CROSSCHAIN.md`: companion suggestion document on cross-chain transferability, covering the two bridge models and the validity of locked tokens, dedicated bridge entry points against reusing the standard issuance functions, the checks the bridge path has to apply, and the reconciliation of a total supply spread over several ledgers
  - Includes draft text for a Cross-chain module, written in the framework's own register and numbered so that it can be appended to the existing list of functionalities
- `CMTAT_SUGGESTION_PRIVACY.md`: companion suggestion document on privacy and confidentiality, covering the framework functionalities worded as "any person may know", the minimum visibility suggested for each data item, and the disclosure path an implementation on a confidential ledger has to document
  - Includes draft text for a visibility and confidentiality sub-section, and the consequential amendment to the functionalities that assume a public ledger
- Restriction section in the Guideline for New Blockchain Implementations: a reference catalogue of the thirteen transfer restrictions shipped in the CMTA Rules repository, outside the equivalency count
  - Each entry states what the restriction checks on a transfer, on a mint and on a burn, since mint and burn travel the same path with one party missing and most rules leave them unchecked
  - Records the fail-open or fail-closed behaviour of the rules backed by an external oracle, registry or feed
  - Note asking the implementation to say which restrictions it applies, where the logic lives, in which order restrictions are evaluated, what a rejected operation returns, and how each restriction behaves when its data source is unavailable
- Privacy and Confidentiality section, for implementations targeting a blockchain or distributed ledger that offers some level of privacy, documented outside the equivalency criteria
  - Visibility values (`public` / `private` / `partial`) and a privacy table covering balances, transfer amounts, transfer participants, total supply, token decimals, frozen/blacklisted addresses and allowlisted/whitelisted addresses, with the CMTAT Solidity visibility pre-filled and columns for the visibility in the implementation being approved, whether the issuer can read the value, and which other readers can
  - List of typical readers to consider (everyone, the account holder, the transfer counterparty, the issuer or administrator role, an auditor or regulator, the ledger operator or validator nodes, nobody)
  - Note asking the implementation to describe how privacy works on the target chain and its consequences for the CMTAT features (supply audit, snapshot, dividend, freeze and allowlist enforcement, disclosure procedure)
- Document Version section: distinction between the **template version** (this document, as published by CMTA) and the **assessment version** (the filled document, set by its author)
  - Table with the two values (the template version, pre-filled and not to be modified, and the assessment version, set by the author) and the recommended one-line form `v0.2.0 (this assessment), filled from CMTA assessment template v0.2.0`
  - The template version an assessment was filled from MUST always be recorded, since criteria IDs MAY be renumbered from one template version to the next
- Note subsections for the Pause module, the Enforcement module and the Implementation Details table, so every fillable table now has a place to describe how the implementation being approved covers it (role model, execution flow, chain-level behaviour)
- Metadata promoted to a top-level section, placed next to Document Version at the top of the file instead of being the first subsection of the equivalency table, so that everything identifying an assessment sits in one block
  - The two existing fields (implementation language, implementation version) become a table and are joined by implementation name, target blockchain or distributed ledger, source repository and commit, assessment date, and assessed by
  - Sentence distinguishing the implementation version (criterion 6, the version of the token itself) from the assessment version and the template version
- Optional criterion 6: Version attribute, mapped to the CMTAT Solidity `VersionModule` / ERC-3643 `version()` function
- Version subsection in the Guideline for New Blockchain Implementations, describing how the implementation version MAY be exposed on other blockchains and the constraint on a mutable version value

### Changed

- Document version bumped to `0.3.0`; it stays a draft, since it is below `1.0`
- Submodules moved from the repository root into a `submodules/` directory (`submodules/CMTAT`, `submodules/SnapshotEngine`, `submodules/RuleEngine`, `submodules/Rules`); `git submodule update --init --recursive` is enough to pick up the new layout
- Reference table updated to the checked-out submodule versions: CMTAT `v3.2.0` to `v3.3.0-rc3`, SnapshotEngine `v0.3.0` to `v0.5.0`, RuleEngine `v3.0.0-rc2` to `v3.0.0-rc6`, Rules `v0.3.0` to `v0.6.0`
  - Every CMTAT Solidity function, interface and role named in the criteria was re-checked against these versions and still exists, so no pre-filled column changed
  - CMTAT is now pinned to a release candidate: `v3.3.0-rc3` is newer than the last stable release `v3.2.0`
- Bullet lists inside the note blockquotes are rendered one bullet per line again (Summary note, Version subsection, Cross-Chain Bridge Support note, Conclusion): they had been collapsed into a single paragraph where the items were separated by ` - `, which Markdown rendered as one run-on sentence. The guidance on reusing the standard `mint` and `burn` for bridge operations was likewise split into a lead-in sentence and three bullets
- Conclusion section: replaced the free-form guideline with a list of the technical points the conclusion MUST cover (token model, architecture, access control model, transfer control flow, issuance and cancellation, data and metadata storage, main differences with CMTAT Solidity, known limitations)
- How to Use This Document: added the filling order (equivalency table, then summary, then conclusion) and the `y` / `partial` / `n` answer values
- Table of Contents: added Summary and Conclusion entries
- All assessment table headers: the *Present in implementation being approved* column now accepts `y/partial/n` instead of `y/n`
- Criteria renumbered from 55 items: former IDs 6–54 shifted to 7–55 to insert the Version attribute as ID 6. Optional criteria count goes from 37 to 38 (mandatory stays at 17)
  - This breaks every assessment already filled against `0.2.0`: an answer given against an ID in the old numbering does not designate the same criterion in `0.3.0`, so such an assessment MUST be re-read against this version before being reused
- Credit Events table: stale reference to the pre-0.2.0 ID `1.29` replaced by ID 38

## [0.2.0] - 2026-05-22

Commit: `a0b5f516447aec9dea51b48c420817e0b821de5d`

### Added

- Table of Contents

- Access Control columns (`Access Control (CMTAT Solidity)` and `Access Control (implementation being approved)`) across all function tables
- Supplementary features section
- Guidance on optional ERC20 `approve` function, with recommendation to include it for secondary market support
- Recommendation to include `tokenId` in CMTAT reference implementations targeting non-Solidity blockchains
- Reference table listing submodule versions (CMTAT v3.2.0, SnapshotEngine v0.3.0, RuleEngine v3.0.0-rc2, Rules v0.3.0)
- Submodule configuration (`.gitmodules`) for CMTAT, SnapshotEngine, RuleEngine, and Rules

### Changed
- `tokenId` moved from mandatory to optional token attributes
- Normative language updated to RFC 2119/8174 keywords throughout (MUST, MUST NOT, SHOULD, MAY)
- Restructured function tables: split mandatory and optional functions into dedicated subsections (Token module, Pause, Enforcement, Transfer restriction, Access Control, Snapshot, Dividend, Credit Events, Debt)
- Row IDs renumbered to sequential integers (previously alphanumeric identifiers such as `1.a`, `1.1`)
- Implementation Details table extended with `Access Control (implementation being approved)` and `Present in implementation being approved` columns



## [0.1.0] - 2026-04-28

First release.

Commit: `2d37e7d1dd5019593828d5e263bc466db36f94af`

### Added
- CMTAT Function Equivalency Table with:
  - Mandatory Attributes: Name, Symbol, Token ID, Terms reference, Decimals (IDs `1.a`–`1.e`)
  - Mandatory Functions: `totalSupply`, `balanceOf`, `transfer`, `mint`/`batchMint`, `burn`/`batchBurn`/`burnFrom`, `pause`, `unpause`, `deactivateContract`, freeze, unfreeze (IDs `1.1`–`1.10`)
  - Optional Functions: Snapshot (`1.11`–`1.16`), Access Control / Transfer restriction (`1.17`–`1.22`), Dividend (`1.23`–`1.28`), Credit Events (`1.29`–`1.32`), Enforcement / forced transfer and partial freeze (`1.33`–`1.34`)
  - Optional Attributes: Debt fields — guarantor, debtholder, unique identifier/hash, issuance date, currency, par value, minimum denomination, maturity date, interest rate, coupon frequency, interest schedule format, interest payment date, day count convention, business day convention (IDs `1.f`–`1.s`)
- Guideline for New Blockchain Implementations section covering:
  - Freeze: ERC-3643 compatibility and split-function alternative
  - CMTAT Extended feature comparison table (Allowlist, Light, Debt, Standard variants)
  - Forced Burn and Forced Transfer guidance
  - Implementation Details table (mint/burn while paused, self-burn, frozen-address burn behaviour)
  - Self-Burn legal rationale
- Reference table listing submodule repositories and checked-out versions
