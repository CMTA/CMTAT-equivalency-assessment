# CMTAT Equivalency Assessment — agent guide

> **Note — keep in sync:** `AGENTS.md` and `CLAUDE.md` must always be **identical**. Any edit to one must be applied verbatim to the other.

> **Note — commit messages:** After each group of modifications or each feature added, always provide a **one-line GitHub commit message** (Conventional-Commits style — here mostly `docs: ...`, sometimes `fix: ...` or `chore: ...`).
>
> **Never put `!` in a commit message** — not as the breaking-change marker (`docs!: ...`), not anywhere else. In an interactive bash, `!` inside double quotes triggers history expansion, so `git commit -m "docs!: ..."` aborts with `bash: !: unrecognized history modifier`. Signal a breaking change with an uppercase `BREAKING CHANGE:` line in the commit body instead, and keep the subject line free of `!`.

> **Note — no tool names in the changelog:** never name an assistant tool, skill or slash command in `CHANGELOG.md`. The changelog records what changed in *this project*, for readers who have no idea what tooling produced it. A line ending "the `<some-skill>` skill gained the corresponding check" documents the author's toolbox rather than the release, and it rots independently of the repository — the tool can be renamed or deleted, leaving a dangling reference to something the reader could never have seen. Describe the change and its effect; if the tooling matters, record it in the audit or analysis report instead.
>
> This is about **tool identities, not the word "Claude"**: files committed to the repository — `CLAUDE.md`, `AGENTS.md`, `CLAUDE_AUDIT.md`, `CLAUDE_ANALYSIS*.md` — are cited freely, because a reader can open them.

> **Note — do not hard-wrap prose in `CHANGELOG.md`:** one line per bullet or paragraph, and let the editor soft-wrap. Markdown collapses a single newline into a space, so a hard-wrapped bullet renders identically — the cost is invisible in the published changelog and paid entirely in the repository. Changing one word reflows every following line, so a one-word correction arrives as a multi-line diff in which a reviewer cannot see what actually changed; and because the wrap column depends on whoever wrote the entry, the file drifts into a mix of styles that reads as damage. Keep the line structure only where it is semantic: fenced code blocks, tables and blockquotes.

> **Note — long changelog entries get sub-bullets:** past roughly three sentences, a bullet stops being scannable — the defect, its blast radius, the fix, the precedent and the caveat all run together, so a reader looking for any one of them has to parse all five. Lead with one sentence naming *what changed*, then one sub-bullet per distinct claim: impact, fix, behaviour-change warning, cost, migration note. A useful trigger is length — compare against the file's own median bullet and split anything several times longer, since that length almost always means several claims in one paragraph. Sub-bullets follow the same no-hard-wrap rule: one line each.

## What this project is

This repository is **documentation only**. It defines the *CMTAT Equivalency Assessment Criteria*: the fillable checklist used to assess whether a token implementation — typically on a non-EVM blockchain — is equivalent to the [CMTAT](https://github.com/CMTA/CMTAT) (CMTA Token) standard. The deliverable is `README.md`; the four git submodules are the CMTA Solidity reference implementations the criteria are mapped against. There is no source code, no build system and no test suite.

## Key concepts

- `README.md` **is** the document. Changing the criteria means editing `README.md` and recording it in `CHANGELOG.md` — nothing else is generated from them automatically.
- Two audiences share the file: columns 1–5 of every criteria table are pre-filled reference data about CMTAT Solidity, columns 6–8 are left blank for the implementation being approved to fill.
- Every criteria row has the same 8 columns: `ID | Requirement | CMTAT Solidity corresponding feature | Access Control (CMTAT Solidity) | Notes | Present in implementation being approved (y/partial/n) | Access Control (implementation being approved) | Implementation details`. New rows MUST keep that column count and order.
- The document currently holds **55 numbered criteria**: **17 mandatory** (IDs 1–4, 7–11, 13–17, 23–25) and **38 optional** (IDs 5–6, 12, 18–22, 26–55).
- Those figures appear in four places that must always agree: the `Scope of the count` table, the `Compliance table` column headers `Mandatory (17)` / `Optional (38)`, the sum rule underneath it, and the example filled table. Update all four together.
- IDs are sequential over the whole document, so inserting or deleting a criterion renumbers every following row. It also invalidates the ID ranges in `Summary` and any ID quoted inside a `Notes` cell — grep for stale references after renumbering.
- Answers are `y` (present), `partial`, or `n` (absent). Every `partial` and every mandatory `n` MUST be explained in the note under the compliance table.
- The tables with no ID column — `CMTAT Extended`, `Implementation Details`, `Restriction`, `Cross-Chain Bridge Support`, `Privacy and Confidentiality` — are deliberately outside the count and are commented in the `Conclusion` instead.
- Documented fill order for users: equivalency table first, then `Summary` (the counts), then `Conclusion` (how the implementation works technically).
- Normative language follows [RFC 2119](https://www.rfc-editor.org/info/rfc2119) / [RFC 8174](https://www.rfc-editor.org/info/rfc8174): use MUST, MUST NOT, SHOULD, MAY rather than "has to" or "needs to".
- The Table of Contents at the top of `README.md` is maintained by hand. Add an entry whenever you add a heading, and match GitHub's anchor slug.
- Cross-chain bridging is explicitly **not** an equivalency requirement; it is documented only as an optional CMTAT Solidity module so that other chains can map it if they want it.

## File tree

```
.
├── README.md         # THE document: criteria tables, guidelines, summary, conclusion
├── README.pdf        # manual export of README.md — stale until re-exported
├── CHANGELOG.md      # Keep a Changelog format; every README change is recorded here
├── CMTAT_SUGGESTION.md   # suggested improvements to the CMTA framework specifications in doc/ — not part of the criteria
├── CMTAT_SUGGESTION_CROSSCHAIN.md # its cross-chain transferability companion
├── CMTAT_SUGGESTION_PRIVACY.md   # its privacy and confidentiality companion
├── LICENSE.md        # Mozilla Public License 2.0
├── .gitmodules       # the four CMTA reference repositories below
├── specification/
│   ├── CMTAT_Equivalency_Assessment_Specificationv0.2.0.pdf   # released PDF (cover page + document)
│   ├── coverpage_CMTA.odg                                     # LibreOffice cover page source
│   └── coverpage_CMTA.pdf                                     # its PDF export
├── doc/              # CMTA source documents the criteria are derived from (framework functional specifications, debt-instrument standard)
└── submodules/       # the four CMTA reference repositories, all pinned to a tag
    ├── CMTAT/            # submodule — CMTAT Solidity reference implementation
    ├── SnapshotEngine/   # submodule — snapshot engine behind the Snapshot criteria (26–31)
    ├── RuleEngine/       # submodule — transfer-hook engine behind the Transfer restriction criteria (20–22)
    └── Rules/            # submodule — individual rules (whitelist, conditional transfer)
```

Most useful paths when checking or writing the CMTAT Solidity columns:

- `submodules/CMTAT/contracts/modules/wrapper/{core,controllers,extensions,options,security}/` — the module implementations the criteria map to.
- `submodules/CMTAT/contracts/interfaces/` — the interfaces quoted in the criteria (`IERC3643Partial.sol`, `ICMTATDebt`, `ICMTATCreditEvents`, …).
- `submodules/CMTAT/doc/modules/` — per-module documentation, the quickest source for a feature's description and its access control.

## Dependencies (pinned submodule versions)

| Submodule | Version | Commit |
|---|---|---|
| CMTAT | `v3.3.0-rc3` | `658672f190d56d3f61663a7d6d51962b8980df70` |
| SnapshotEngine | `v0.5.0` | `aa089353605cd1b0e555d22b62aa4fbeaae7df25` |
| RuleEngine | `v3.0.0-rc6` | `ca75429c581a2eb9043e4719561e941d0b2e1206` |
| Rules | `v0.6.0` | `283efe723225c89729fd618852a9c2705a47180b` |

This table is duplicated in the `Reference` section at the end of `README.md`. Whenever a submodule is moved, update both from `git submodule status`.

## Common commands

```bash
git submodule update --init --recursive   # fetch the four reference repositories
git submodule status                      # versions and commits for the Reference table
git -C submodules/<name> tag --sort=-v:refname | head   # newest tag available upstream, to check the pin
```

After editing any criteria table, check that the IDs are still a gap-free sequence and that the totals still hold:

```bash
python3 -c "
import re
ids=[int(m.group(1)) for m in map(lambda l: re.match(r'^\| *(\d+) *\|', l), open('README.md')) if m]
print('count:', len(ids), 'sequential:', ids == list(range(1, len(ids)+1)))"
```

## Conventions

- The document version lives in the `Document Version` section of `README.md`. Anything below `1.0`, or suffixed `rc`, is a draft. That section holds two distinct versions: the **template** version (this repository, currently `v0.2.0`) and the **assessment** version filled in by whoever uses the template, together with the template version it was filled from. Only the template version is maintained here.
- Record every change in `CHANGELOG.md` under `## [Unreleased]`, then rename that heading to the version and date at release time.
- Renumbering criteria breaks every assessment already filled against the previous version: say so explicitly in the changelog and bump the version.
- `README.pdf` and `specification/*.pdf` are exported by hand and are not regenerated by any script — flag them as stale whenever `README.md` changes materially.
- Keep the pre-filled columns factual: `CMTAT Solidity corresponding feature` and `Access Control (CMTAT Solidity)` must match the code in the pinned submodules, not a general recollection of CMTAT.
- Prose in `README.md` follows the same one-line-per-block rule as the changelog, except inside tables, fenced blocks and blockquotes.
