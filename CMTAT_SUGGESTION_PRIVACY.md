# CMTAT Framework — suggested improvements: privacy and confidentiality

## Purpose

This document suggests how the **CMTAT Framework functional specifications** (`doc/cmtat-framework-functional-specifications-june-2026.pdf`, June 2026 edition) could address privacy and confidentiality.

It is a companion to [CMTAT_SUGGESTION.md](CMTAT_SUGGESTION.md), which carries the other suggestions, and it comes from the same source: the mapping work behind the [CMTAT Equivalency Assessment Criteria](README.md), whose own Privacy and Confidentiality section covers the implementation side of the same question. It is a suggestion list produced by this repository, not a CMTA publication.

## The gap

Functionalities 1, 2, 11 and 18–20 are worded as "any person may know" — total supply, decimals, snapshot time, snapshot total supply, snapshot balance. On a confidential ledger, none of these is necessarily public. The framework already acknowledges this once, for the frozen status only, in functionality 14 (page 9): "On private blockchains, such as Aztec, the issuer may want to restrict access to the issuer, the relevant token holder, and possibly any third parties explicitly authorized by the issuer." §4.3 (page 14) then lists a privacy-preserving Aztec implementation as a reference implementation.

The wording and the reference implementations are therefore inconsistent: an implementation on a confidential ledger cannot satisfy "any person may know" for balances, and it is not clear whether it thereby fails the mandatory Base module.

The framework SHOULD generalize the note under functionality 14 into its own section, stating for each data item whether public readability is a **requirement** or merely the **default on a public ledger**:

| Data | Suggested minimum visibility |
|---|---|
| Balance of an address | The holder of that address, and the issuer |
| Transfer amount | The parties to the transfer, and the issuer |
| Total supply | The issuer; publicly if the ledger is public |
| Decimals | Everyone, since it is display metadata and reveals nothing about holdings |
| Frozen status | The affected holder and the issuer (as already stated in functionality 14) |
| Whitelist membership | The affected holder and the issuer |

It SHOULD also state the consequences for the optional modules, since these are the operations that break first when balances are hidden: how the total supply is audited, how a snapshot and a distribution are computed on confidential balances, and how a validation rule screens participants without revealing the list. Finally it SHOULD require the disclosure path to be documented — which addresses or roles (auditor, regulator, court-appointed third party) can obtain a reading, and by what mechanism.
