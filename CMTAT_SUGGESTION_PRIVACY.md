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
| Total supply | The issuer and every token holder, since a holder cannot determine the extent of a proportional right without it; publicly if the ledger is public |
| Decimals | Everyone, since it is display metadata and reveals nothing about holdings |
| Frozen status | The affected holder and the issuer (as already stated in functionality 14) |
| Whitelist membership | The affected holder and the issuer |

The same section SHOULD also list the third parties who may be entitled to a reading — the auditor of the issuer, the auditor of a token holder, a supervisory authority, a court or an officer appointed by it — since these are the readings an implementation has to provide for and cannot add after the fact. The draft below gives that list as a second table.

It SHOULD also state the consequences for the optional modules, since these are the operations that break first when balances are hidden: how the total supply is audited, how a snapshot and a distribution are computed on confidential balances, and how a validation rule screens participants without revealing the list. Finally it SHOULD require the disclosure path to be documented — which addresses or roles (auditor, regulator, court-appointed third party) can obtain a reading, and by what mechanism.

## Draft text for the framework

The text below is a draft that could be inserted in the framework as a new sub-section of Section 2, followed by an amendment to the wording of the functionalities that assume a public ledger. It is written in the framework's own register — lowercase "must", "may" and "should" — rather than in the RFC 2119 style used elsewhere in this repository, so that it can be pasted in without rewriting.

---

### § 2.8 Visibility and confidentiality of token data

The functionalities described in Section 3 are worded on the assumption that the ledger is public, and several of them state that "any person may know" a given item of data. Where the CMTAT is implemented on a ledger that provides confidentiality — such as the privacy-preserving implementation referred to in § 4.3 — that is not achievable, and it is not intended that such an implementation be treated as non-compliant on that ground.

For each item of data, the framework therefore distinguishes two things:

- the minimum set of persons who must be able to read it, which is a requirement of this framework; and
- publication to any person, which is the ordinary consequence of using a public ledger, and is not in itself a requirement.

| Data | Persons who must be able to read it |
|---|---|
| Number of tokens in circulation | the issuer and every token holder |
| Balance of an address | the holder of that address, and the issuer |
| Amount of a transfer | the parties to the transfer, and the issuer |
| Decimals | any person, since the value is display information and reveals nothing about holdings |
| Frozen status of an address | the holder of that address, and the issuer |
| Membership of a whitelist | the holder of that address, and the issuer |
| Snapshot total supply and snapshot balances | the same persons as for the corresponding current data |

The following persons may be entitled to read data that is not public. The implementation must provide for the readings that apply to it; a reading that the implementation cannot provide cannot be granted afterwards.

| Person | Data that person must be able to read |
|---|---|
| The holder of an address | the balance of that address, the transfers to and from it, its frozen status, its membership of a whitelist, and the number of tokens in circulation |
| The issuer, and the persons authorised by it | every item in the table above, for every address |
| The other party to a transfer | the amount of that transfer |
| A person auditing the issuer, or auditing the instrument | every item in the table above, for the periods covered by the audit |
| A person auditing a token holder | the balances and the transfers of the addresses of that holder |
| A supervisory or regulatory authority | the data required by the regulation applicable to the issuer or to the instrument |
| A court, or an officer appointed by it, such as an insolvency administrator or an executor | the balances and the transfers of the addresses concerned by the order or the appointment |
| A tax authority | the data required by the applicable tax legislation |
| An operator of a trading or settlement venue on which the instrument is admitted | the data required to settle the transactions it processes |
| The operator of the ledger, and the nodes that validate the transactions | whatever the ledger requires them to process, which is a consequence of the technology chosen and must be documented as such |

For each of these persons, the implementation must state whether the reading is available permanently or on request, and who grants it. A reading that depends on the co-operation of a person having an interest in withholding it does not satisfy this requirement.

An implementation must document, for each item of data: whether it is public, confidential, or partially confidential; the mechanism by which confidentiality is obtained; and how a person entitled to read the data obtains it, for example a viewing key, a decryption share, an observer node, or a disclosure made by the issuer.

The issuer must retain the ability to read whatever data is necessary to perform the functionalities that the token implements. In particular, the issuer must be able to determine the balances at a record time where the Snapshot module (§ 3.2.1) or the Distribution module (§ 3.2.4) is used, and the balance of an address that is subject to a freeze (functionality 12) or to an enforced transfer (functionality 37).

Where the applicable law, or a competent authority, requires a third party such as an auditor, a regulator or a court-appointed representative to obtain a reading of confidential data, the implementation must provide a means of disclosure to that third party, and the issuer must document it.

Confidentiality does not remove any mandatory functionality. Where an item of data is not readable by any person, the implementation must state how the operations that depend on it are performed nonetheless.

### Consequential amendment to the functionalities

The functionalities that presently read "any person may know" would be qualified by reference to the new sub-section. For example, functionality 1 would read:

> 1. **Know total supply**: for a particular CMTAT token, the persons designated in § 2.8 may know the total number of tokens in circulation at any point in time. On a public ledger, that information is available to any person.

The same qualification applies to functionality 2 (know balance), functionality 11 (know decimals), and functionalities 18 to 20 (snapshot time, snapshot total supply and snapshot balance). Functionality 14 (know frozen status) already contains a note to this effect, which would be replaced by the reference to § 2.8.
