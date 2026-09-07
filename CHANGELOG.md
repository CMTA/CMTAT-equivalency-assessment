# Changelog

All notable changes to this document will be recorded here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [0.3.0] - 2026-09-03

### Added

- Two optional criteria for reading the amounts behind a partial freeze: **23 Know active balance** (`getActiveBalanceOf(address)`) and **24 Know frozen balance** (`getFrozenTokens(address)`, declared by the draft `IERC7943` interface), both public reads in CMTAT Solidity
  - The criteria previously covered the partial freeze setters alone, so an implementation could freeze part of a balance while leaving a holder no way to read how much of its own balance was still transferable
  - Criterion 24 records that on a confidential ledger the reading MAY be restricted in the same way as the frozen status
  - Optional count goes from 38 to 40, total from 58 to 60; the mandatory count is unchanged at 20
- Criterion 12, **User-approved cancellation** (`burnFrom(address account, uint256 value)`), optional: the token holder authorizes the cancellation with an `approve` and the issuer, or an address it has authorized such as a bridge, performs it
  - Covers CMTA framework functionality 41, which the criteria previously had no criterion for: they carried issuer-side cancellation (criterion 11) and the Self-Burn note, but nothing for a cancellation the holder authorizes and another party performs
  - CMTAT Solidity guards it with both an ERC-20 allowance from the holder and the `BURNER_FROM_ROLE`, so both conditions are recorded in the access-control column
  - Optional count goes from 40 to 41, total from 60 to 61; the mandatory count is unchanged at 20
- Three mandatory criteria for reading the states the existing criteria only let an issuer change: **15 Know pause status** (`paused()`), **17 Know deactivate status** (`deactivated()`, declared by the draft `IERC8343` interface) and **20 Know frozen status** (`isFrozen(address)`), all public reads in CMTAT Solidity
  - Closes a gap in which an implementation could pass every mandatory criterion while offering no way to find out whether the token was paused, whether it had been deactivated, or whether an address was frozen — the criteria covered the operations that change those states but never required the states to be readable
  - Criterion 20 records that on a ledger providing confidentiality the reading MAY be restricted to the issuer, the holder concerned and the third parties the issuer authorizes, cross-referencing the Privacy and Confidentiality section
  - Mandatory count goes from 17 to 20, total from 55 to 58; the optional count is unchanged at 38

- Summary section (before the equivalency table) giving the aggregated compliance of the implementation being approved with CMTAT: scope of the count (17 mandatory / 38 optional criteria), allowed answer values (`y` / `partial` / `n`), compliance table to fill, and a note requiring every `partial` and every mandatory `n` to be explained
- Cross-Chain Bridge Support section (ERC-7802 and Chainlink CCIP), documented as an optional module outside the equivalency criteria
  - Guidance for implementations that reuse the standard `mint` and `burn` functions for bridge operations instead of dedicated cross-chain entry points: those functions SHOULD apply the pause check on the bridge path, so a cross-chain movement is subject to the same checks as a standard transfer, and the implementation SHOULD state which other transfer checks (freeze, partial freeze, allowlist, rule engine) the bridge path enforces
- `CMTAT_SUGGESTION.md`: suggested improvements to the CMTAT Framework functional specifications (June 2026 edition), written from the mapping work behind the criteria
  - Ten groups of suggestions, each giving the current wording with its section and page, the gap, and a proposed change: versioning, transfer restrictions, enforcement and cancellation, pause and deactivation, roles, attributes and documents, batch operations, auditability, reference implementations, and divergences with these criteria
  - Every suggestion that proposes a change of wording carries a draft text block written in the framework's own register, ready to be pasted in: ten new functionalities numbered 43 to 52, a replacement for functionality 41, and additions to functionalities 6, 9 and 11, to the Validation module, to the attributes list and to the reference implementations section
  - Records that framework functionalities 8, 10 and 14 (know pause status, know deactivate status, know frozen status) are mandatory in the framework and have no criterion in this document, so an implementation could pass all 17 mandatory criteria while offering no way to read those states
- `CMTAT_SUGGESTION_EDITORIAL.md`: companion suggestion document on the editorial side of the framework specifications — giving the document its own version number, making the functionality numbers stable across editions and defining the normative keywords by reference to RFC 2119 and RFC 8174, then the typographical defects with the page, the current text and a suggested correction for each, plus two consistency points on module naming and on the role symbols
- `CMTAT_SUGGESTION_CROSSCHAIN.md`: companion suggestion document on cross-chain transferability, covering the two bridge models and the validity of locked tokens, dedicated bridge entry points against reusing the standard issuance functions, the checks the bridge path has to apply, and the reconciliation of a total supply spread over several ledgers
  - Includes draft text for a Cross-chain module, written in the framework's own register and numbered so that it can be appended to the existing list of functionalities
  - Requires the authorisation to be granted per bridge operator, so that one can be revoked without interrupting the others, and adds a functionality limiting the number of tokens each bridge may create or cancel over a period, which caps what a compromised bridge can do while the issuer revokes it
- `CMTAT_SUGGESTION_PRIVACY.md`: companion suggestion document on privacy and confidentiality, covering the framework functionalities worded as "any person may know", the minimum visibility suggested for each data item, and the disclosure path an implementation on a confidential ledger has to document
  - Includes draft text for a visibility and confidentiality sub-section, with a table of the data and the persons who must be able to read it, a second table of the third parties who may be entitled to a reading (the auditor of the issuer, the auditor of a token holder, a supervisory authority, a court or an officer appointed by it, a tax authority, a trading or settlement venue, and the operator of the ledger), and the consequential amendment to the functionalities that assume a public ledger
  - The number of tokens in circulation is readable by every token holder, not by the issuer alone, since a holder cannot determine the extent of a proportional right without it
  - Warning that a total supply which is public, or disclosed to holders, leaks information on a confidential ledger: two readings give the net amount created or cancelled in between, and a holder who knows the figure and its own balance knows the aggregate held by all the others. The reading by holders is therefore a default the issuer may restrict
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
- Criteria renumbered in this release, to insert the user-approved cancellation criterion, the three status-reading criteria and the two balance-reading criteria: relative to `0.2.0`, IDs 1–11 are unaffected, former IDs 12–14 become 13–15, 15 becomes 17, 16–17 become 19–20, 18–19 become 22–23, and 20–55 become 26–61
  - **This breaks every assessment already filled against `0.2.0` and against any earlier draft of `0.3.0`**: an answer given against an ID in the previous numbering does not designate the same criterion here, so such an assessment MUST be re-read against this version before being reused
  - The stale ID references were updated with it: the compliance-table example now cites criterion 16 for *Deactivate contract*, the Credit Events note cites ID 41, and the Restriction section cites criteria 23–25
- `CMTAT_SUGGESTION` § 9: a suggestion that the framework split its Base module, whose eleven mandatory functionalities cover four unrelated concerns, into a Base module (the attributes), a Token module (supply, balance, transfer, decimals), a Supply module (create and cancel) and a Pause module (pause, unpause, deactivate and the two status reads)
  - The grouping does not match § 4.1 of the framework itself, which already names `BaseModule`, `ERC20BaseModule` and `PauseModule` as separate contracts, nor the Solidity implementation, which splits them further
  - It also obscures where a role or a restriction attaches — the bridge authorisation, the pause check on the issuance path and the enforcement powers all attach to the supply-change functions alone — and makes "mandatory" all or nothing in an assessment
  - Sections 9 and 10 of that document became 10 and 11, and the references to § 10 were updated with them
- Corrections found by re-reading the two CMTA source documents in `doc/` against the criteria and the suggestion documents
  - `CMTAT_SUGGESTION_PRIVACY`: functionality 2 (know balance) was listed among those worded "any person may know"; it is not — it already names its readers as the holder, the issuer and any third party the issuer appoints, which is the model the document proposes generalising. Functionalities 8 and 10 were missing from that list, and 18–20 read "anyone may know" rather than "any person may know"
  - `CMTAT_SUGGESTION_EDITORIAL`: the example of a descriptive "may" cited § 2.7 as "the issuer may wish to delegate"; that section reads "Issuers can however choose to delegate", so the example was replaced by a verbatim one from § 2.1
  - `CMTAT_SUGGESTION_CROSSCHAIN`: the introduction said the draft numbers functionalities "53 to 55" when four are drafted, 53 to 56
- Criterion 4 retitled from *No fractions* to *Decimals (no fractions by default)*, and given a second requirement: the value MUST be readable
  - The criterion always mapped to the public `decimals` getter, but its title named only the constraint on the value, so an implementation could have answered `y` on the ground that it has no fractions while exposing no way to read the number of decimals
  - Closes the divergence with the framework, whose functionality 11 (know decimals) is a read requirement; no renumbering, since the criterion covers both halves rather than being split in two
- Token Attributes: a sentence recording why the ticker symbol (criterion 2) is mandatory here although the CMTA framework lists it as an optional attribute — a symbol SHOULD be set where the token is intended to be held in a wallet or admitted to trading, and every CMTA reference implementation carries one
- Self-Burn: removed the paragraph stating that a security can only be cancelled by its issuer and that an investor wishing to exit should transfer to the issuer first
  - The criteria are a mapping instrument and should not assert a legal position that holds in some jurisdictions and not others; the applicable law is for the issuer and its counsel to determine, and the section already leaves the choice open
- Self-Burn: the permission to offer self-burn is now grounded in the CMTA framework rather than presented as a local departure — functionality 41, *user-approved cancel*, states that it "also allows token holders to cancel their own tokens" — and the section distinguishes it from criterion 12, which is the holder-authorized cancellation the issuer performs
- Self-Burn: an implementation that offers self-burn SHOULD now state it, together with the legal basis on which it is offered, so that an assessment records which of the two arrangements was adopted instead of leaving it to be inferred from the absence of a remark
- Cross-Chain Bridge Support: `burnFrom` added to the reference table, and a note on when to prefer it to `crosschainBurn`
  - It is declared by the same `ERC20CrossChainModule` as the ERC-7802 entry points and is usable by a Chainlink CCIP token pool, so its absence from the table was a gap rather than a scoping choice
  - The two bound a bridge's authority differently: `crosschainBurn` requires no allowance, so a bridge holding `CROSS_CHAIN_ROLE` can cancel the tokens of any address, whereas `burnFrom` spends an allowance and is therefore capped per holder by what that holder approved
  - The cost of `burnFrom` is recorded with it: the holder MUST approve first, which adds a transaction to the bridge flow
- Cross-Chain Bridge Support: added the LayerZero arrangement alongside ERC-7802 and the Chainlink CCIP hooks
  - `CMTAT-LayerZero` sits outside the token rather than in it — an adapter on the LayerZero V2 OFT standard that holds the bridge authorization itself and calls the token to burn on the source chain and mint on the destination chain
  - Records its two forms: `LayerZeroAdapterERC7802` on the ERC-7802 entry points, and `LayerZeroAdapter` on the ERC-3643 `mint` and `burn`, which is the reuse case the section already warned about
  - Notes that each adapter carries its own pause, controlled by the adapter owner and independent of the token's, and that several bridges can each hold their own authorization
- Reference table: added the three submodules pinned since it was last written — `CMTAT-Confidential` `v1.0.0`, `CMTAT-LayerZero` (`v0.2.0` + 1 commit) and `private-CMTAT-aztec` (`0.1.1` + 25 commits) — with a sentence separating the four implementations the criteria are mapped against from the three cited only by the sections outside the count
- The four suggestion documents moved from the repository root into `doc/CMTAT_Suggestion/`, next to the CMTA specifications they comment on
  - `CMTAT_SUGGESTION.md`, `CMTAT_SUGGESTION_CROSSCHAIN.md`, `CMTAT_SUGGESTION_PRIVACY.md` and `CMTAT_SUGGESTION_EDITORIAL.md` keep their names, so any reference to one of them by file name still resolves; only their location changed
  - The root now holds the deliverable and its metadata alone (`README.md`, `CHANGELOG.md`, `LICENSE.md`), which keeps the suggestions to CMTA visibly separate from the criteria
  - Inside those documents, the framework specifications are now cited as `cmtat-framework-functional-specifications-june-2026.pdf` rather than `doc/cmtat-framework-functional-specifications-june-2026.pdf`, since the path was relative to the old location

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
