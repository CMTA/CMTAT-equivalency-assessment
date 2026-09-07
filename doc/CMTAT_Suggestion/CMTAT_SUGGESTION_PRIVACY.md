# CMTAT Framework — suggested improvements: privacy and confidentiality

## Purpose

This document suggests how the **CMTAT Framework functional specifications** (`cmtat-framework-functional-specifications-june-2026.pdf`, June 2026 edition) could address privacy and confidentiality.

It is a companion to `CMTAT_SUGGESTION.md`, which carries the other suggestions, and comes from the same mapping work behind the CMTAT Equivalency Assessment Criteria (`README.md`), whose own Privacy and Confidentiality section covers the implementation side of the same question. It is a suggestion list produced by this repository, not a CMTA publication.

## The gap

Functionalities 1, 2, 11 and 18–20 are worded as "any person may know" — total supply, decimals, snapshot time, snapshot total supply, snapshot balance. On a confidential ledger, none of these is necessarily public. The framework already acknowledges this once, for the frozen status only, in functionality 14 (page 9): "On private blockchains, such as Aztec, the issuer may want to restrict access to the issuer, the relevant token holder, and possibly any third parties explicitly authorized by the issuer." §4.3 (page 14) then lists a privacy-preserving Aztec implementation as a reference implementation.

The wording and the reference implementations are therefore inconsistent: an implementation on a confidential ledger cannot satisfy "any person may know" for balances, and it is not clear whether it thereby fails the mandatory Base module.

The framework SHOULD generalize the note under functionality 14 into its own section, stating for each data item whether public readability is a **requirement** or merely the **default on a public ledger**:

| Data | Suggested minimum visibility |
|---|---|
| Balance of an address | The holder of that address, and the issuer |
| Transfer amount | The parties to the transfer, and the issuer |
| Total supply | The issuer, and every token holder unless the issuer restricts it for the reason set out below, since a holder cannot otherwise determine the extent of a proportional right; publicly if the ledger is public |
| Decimals | Every token holder and the issuer; the framework SHOULD recommend, rather than require, a reading by any person with access to the ledger, since the value is display information and reveals nothing about holdings |
| Frozen status | The affected holder and the issuer (as already stated in functionality 14) |
| Whitelist membership | The affected holder and the issuer |

**A readable total supply leaks balance and transaction information, and the framework SHOULD warn about it.** On a confidential ledger the total supply is the one aggregate that is hard to hide, and both making it public and disclosing it to holders open inference channels:

- **Delta inference across two readings.** An observer who reads the value before and after a period of activity recovers the net amount created or cancelled in between, and where exactly one such operation occurred, that operation's amount is revealed in full. This is documented in [CMTAT-Confidential](https://github.com/CMTA/CMTAT-Confidential) — FAQ "Is the total supply public or private information?" and the Total Supply Visibility section, reported as audit finding `OZ-L-01` — which recommends aggregating several supply-changing operations before publishing rather than publishing after each one.
- **Complement inference within a small holder set.** A holder who knows the total supply and their own balance also knows the aggregate held by everyone else. Where there are two holders, that is the other holder's exact balance, and the fewer the holders, the narrower the range for each of them.

The framework SHOULD therefore present readability by holders as the default rather than an absolute requirement, and allow the issuer to restrict it where the confidentiality of individual operations outweighs the holder's interest in knowing the denominator — stating which of the two it has chosen, and how a holder determines a proportional entitlement if the figure is withheld.

**A restricted frozen status is still observable by anyone who attempts a transfer, and the framework SHOULD say so.** A freeze takes effect by causing the transfer to be rejected, so a holder who sends tokens to a frozen address, or a frozen holder who sends tokens, has the operation rejected by the ledger; the rejection is itself the answer. Where the sender knows that the other conditions are met — the token is not paused, both addresses are on the whitelist, the balance is sufficient — the freeze is the only remaining explanation, and the probe costs no more than one rejected transaction. Restricting the reading of the frozen status therefore prevents it from being enumerated over addresses at large, but it does not conceal it from a counterparty who tries; and where the ledger publishes the fact that a transaction was rejected even without publishing its contents, that counterparty is not the only person who learns it. The same probe identifies an address missing from a whitelist.

The same section SHOULD also list the third parties who may be entitled to a reading — the auditor of the issuer, the auditor of a token holder, a supervisory authority, a court or an officer appointed by it — since these are the readings an implementation has to provide for and cannot add after the fact. The draft below gives that list as a second table.

It SHOULD also state the consequences for the optional modules, since these are the operations that break first when balances are hidden: how the total supply is audited, how a snapshot and a distribution are computed on confidential balances, and how a validation rule screens participants without revealing the list. Finally it SHOULD require the disclosure path to be documented — which addresses or roles (auditor, regulator, court-appointed third party) can obtain a reading, and by what mechanism.

## Draft text for the framework

The text below is a draft that could be inserted in the framework as a new sub-section of Section 2, followed by an amendment to the wording of the functionalities that assume a public ledger. It is written in the framework's own register — numbered functionalities, a rationale followed by a list — so that it can be pasted in without rewriting, and its normative keywords use the RFC 2119 and RFC 8174 uppercase forms, as explained in `CMTAT_SUGGESTION.md`.

---

### § 2.8 Visibility and confidentiality of token data

The functionalities described in Section 3 are worded on the assumption that the ledger is public, and several of them state that "any person may know" a given item of data. Where the CMTAT is implemented on a ledger that provides confidentiality — such as the privacy-preserving implementation referred to in § 4.3 — that is not achievable, and it is not intended that such an implementation be treated as non-compliant on that ground.

For each item of data, the framework therefore distinguishes two things:

- the minimum set of persons who MUST be able to read it, which is a requirement of this framework; and
- publication to any person, which is the ordinary consequence of using a public ledger, and is not in itself a requirement.

| Data | Persons who MUST be able to read it | Persons who may also be entitled to read it |
|---|---|---|
| Number of tokens in circulation | the issuer; and every token holder, unless the issuer has restricted that reading under the paragraph on inference below | a person auditing the issuer or the instrument; a supervisory authority; a trading or settlement venue on which the instrument is admitted |
| Balance of an address | the holder of that address, and the issuer | a person auditing the issuer; a person auditing that holder; a supervisory authority; a court or an officer appointed by it; a tax authority |
| Amount of a transfer | the parties to the transfer, and the issuer | the persons listed for a balance; the venue that settles the transaction |
| Decimals | the issuer, and every token holder | any person having access to the ledger, as recommended below |
| Frozen status of an address | the holder of that address, and the issuer; the status is in any event disclosed to a counterparty whose transfer is rejected, as set out below | a person auditing the issuer; a supervisory authority; a court or an officer appointed by it |
| Membership of a whitelist | the holder of that address, and the issuer | a person auditing the issuer; a supervisory authority; the operator of a venue verifying the eligibility of a counterparty |
| Snapshot total supply and snapshot balances | the same persons as for the corresponding current data | the same persons as for the corresponding current data |

The second column is a requirement: an implementation that does not provide those readings does not satisfy this framework. The third column is not. It lists the readings that the applicable law, the terms of the instrument or a contract may call for, and which the implementation has to be capable of providing when they are called for.

The following table states the same allocation by person rather than by item of data. The implementation MUST provide for the readings that apply to it; a reading that the implementation cannot provide cannot be granted afterwards.

| Person | Data that person MUST be able to read |
|---|---|
| The holder of an address | the balance of that address, the transfers to and from it, its frozen status, its membership of a whitelist, the number of decimals, and the number of tokens in circulation |
| The issuer, and the persons authorised by it | every item in the table above, for every address |
| The other party to a transfer | the amount of that transfer |
| A person auditing the issuer, or auditing the instrument | every item in the table above, for the periods covered by the audit |
| A person auditing a token holder | the balances and the transfers of the addresses of that holder |
| A supervisory or regulatory authority | the data required by the regulation applicable to the issuer or to the instrument |
| A court, or an officer appointed by it, such as an insolvency administrator or an executor | the balances and the transfers of the addresses concerned by the order or the appointment |
| A tax authority | the data required by the applicable tax legislation |
| An operator of a trading or settlement venue on which the instrument is admitted | the data required to settle the transactions it processes |
| The operator of the ledger, and the nodes that validate the transactions | whatever the ledger requires them to process, which is a consequence of the technology chosen and MUST be documented as such |

The number of tokens in circulation requires particular care, since it is an aggregate of every balance. Where the ledger is confidential, an observer who obtains that figure at two points in time can determine the net number of tokens created or cancelled between them, and where a single such operation occurred in that interval, its amount is revealed in full. A holder who knows the figure and its own balance also knows the aggregate held by all the other holders, which, where the holders are few, narrows their individual balances. An implementation MAY therefore restrict the reading of that figure, including as against holders, where the confidentiality of individual operations is to be preserved; it MUST then state how a holder determines the extent of a right expressed as a proportion of the tokens in issue. Where the figure is disclosed periodically, the disclosures SHOULD cover several operations rather than each one.

The frozen status of an address is subject to a comparable limit. A freeze takes effect by causing the transfer to be rejected, so any person who attempts a transfer to or from a frozen address learns that the transfer is not permitted, and where that person knows that the other conditions of the transfer are satisfied, the freeze is disclosed to them by the rejection alone. Restricting the reading of that status accordingly prevents it from being enumerated for addresses at large, and prevents a person who attempts no transfer from obtaining it, but it does not conceal it from a counterparty who attempts one; and where the ledger publishes the fact that a transaction was rejected, even without its contents, that counterparty is not the only person who learns it. The same applies to the membership of a whitelist. An implementation MUST state whether a rejection distinguishes its reason, since a rejection that names the freeze discloses more than one that reports only that the transfer is not permitted.

The number of decimals is treated differently from the other items, since it is display information: it states how a balance is to be presented, and reveals nothing about any holding, any transfer or the number of tokens in issue. Its reading SHOULD therefore be open to any person having access to the ledger, and an implementation on a confidential ledger has no reason to protect it. That reading is a recommendation and not a requirement, since a ledger may restrict access at its own level, and an implementation is not to be treated as non-compliant because the ledger it uses is permissioned. What is required is that the issuer and every token holder be able to read the value, without which a holder cannot interpret its own balance.

For each of these persons, the implementation MUST state whether the reading is available permanently or on request, and who grants it. A reading that depends on the co-operation of a person having an interest in withholding it does not satisfy this requirement.

An implementation MUST document, for each item of data: whether it is public, confidential, or partially confidential; the mechanism by which confidentiality is obtained; and how a person entitled to read the data obtains it, for example a viewing key, a decryption share, an observer node, or a disclosure made by the issuer.

The issuer MUST retain the ability to read whatever data is necessary to perform the functionalities that the token implements. In particular, the issuer MUST be able to determine the balances at a record time where the Snapshot module (§ 3.2.1) or the Distribution module (§ 3.2.4) is used, and the balance of an address that is subject to a freeze (functionality 12) or to an enforced transfer (functionality 37).

Where the applicable law, or a competent authority, requires a third party such as an auditor, a regulator or a court-appointed representative to obtain a reading of confidential data, the implementation MUST provide a means of disclosure to that third party, and the issuer MUST document it.

Confidentiality does not remove any mandatory functionality. Where an item of data is not readable by any person, the implementation MUST state how the operations that depend on it are performed nonetheless.

### Consequential amendment to the functionalities

The functionalities that presently read "any person may know" would be qualified by reference to the new sub-section. For example, functionality 1 would read:

> 1. **Know total supply**: for a particular CMTAT token, the persons designated in § 2.8 may know the total number of tokens in circulation at any point in time. On a public ledger, that information is available to any person.

The same qualification applies to functionality 2 (know balance), functionality 11 (know decimals), and functionalities 18 to 20 (snapshot time, snapshot total supply and snapshot balance). Functionality 14 (know frozen status) already contains a note to this effect, which would be replaced by the reference to § 2.8.
