# Worked examples

These examples illustrate how to apply the skill rules. They are artificial examples, not factual claims about real scholars.

## Example 1 — Clean timeline match

Target timeline:

| Period | Institution | Role |
|---|---|---|
| 2012-2017 | Stanford University | PhD |
| 2017-2019 | MIT | Postdoc |
| 2019-present | Zhejiang University | Professor |

Candidate:

```text
X. Chen et al. 2018, Journal of Catalysis
Target affiliation: MIT
Author position: first
```

Decision:

```text
Identity: strong
Reason: MIT matches active postdoc period.
Role: first
Final: include
```

## Example 2 — Post-move publication lag

Target left MIT in 2019 and started Zhejiang University in 2019.

Candidate:

```text
X. Chen et al. 2020, ACS Catalysis
Target affiliation: MIT
Coauthors: postdoc supervisor and MIT lab members
Role: first author
```

Decision:

```text
Identity: strong or moderate
Reason: 1 year after departure; coauthor and topic continuity support old-affiliation lag.
Role: first
Final: include, note lag.
```

## Example 3 — Two-year old-affiliation lag

Candidate:

```text
X. Chen et al. 2021
Target affiliation: MIT
Target left MIT in 2019
Coauthors: MIT supervisor present
Topic: same as PhD/postdoc work
Role: corresponding author verified on publisher page
```

Decision:

```text
Identity: moderate
Reason: 2 years after departure; old-affiliation lag plausible but requires corroboration.
Role: corresponding
Final: include only if the corroboration is sufficient for the current fixed-authorship audit; otherwise keep it internal as ambiguous.
```

## Example 4 — Affiliation mismatch

Candidate:

```text
X. Chen et al. 2018
Target affiliation: Fudan University
Target timeline: Stanford, MIT, Zhejiang University only
Coauthors: no overlap
Topic: unrelated
```

Decision:

```text
Identity: excluded
Reason: institution never appears in timeline; no secondary fingerprints.
Final: exclude as probable same-name author.
```

## Example 5 — Missing affiliation but strong coauthor network

Candidate:

```text
X. Chen et al. 2016
Affiliation unavailable
Coauthors: PhD advisor and two recurring lab members
Topic: same method and system as target dissertation
Role: co-first footnote visible
```

Decision:

```text
Identity: moderate or strong depending on source reliability
Reason: affiliation missing but multiple secondary fingerprints match.
Role: co_first
Final: include with confidence note.
```

## Example 6 — Equal contribution not co-first

Candidate:

```text
Author contribution statement: All authors contributed equally to manuscript preparation.
Target author position: middle
No co-first footnote in byline
No correspondence marker
```

Decision:

```text
Role: contribution_statement_only
Final: exclude by default
Reason: equal contribution does not identify target as co-first or corresponding.
```

## Example 7 — Co-first footnote

Candidate byline:

```text
A†, B†, X. Chen†, D*, E
† These authors contributed equally.
* Correspondence to D.
```

If X. Chen is the target:

```text
Role: co_first
Final: include
```

If D is the target:

```text
Role: corresponding
Final: include
```

## Example 8 — Co-corresponding author

Candidate byline:

```text
A, B, C, X. Chen*, Y. Liu*
* Correspondence to X. Chen and Y. Liu.
```

If X. Chen is the target:

```text
Role: co_corresponding
Final: include
```

## Example 9 — Last author is not enough

Candidate:

```text
Target is last author.
No correspondence marker.
Publisher page lists another author as corresponding.
```

Decision:

```text
Role: middle_or_last_author_only
Final: exclude by default
Reason: last-author position alone does not prove corresponding status.
```

## Example 10 — Preprint and journal duplicate

Raw candidates:

```text
2021 arXiv preprint: same title, same authors
2022 journal article: DOI available, same work
```

Decision:

```text
Keep journal article as primary.
Record arXiv version as alternate version.
Do not count twice.
```

If author order differs between versions, flag role discrepancy and verify which version the user wants counted.

## Example 11 — Same institution, same name, same era

Target:

```text
Wei Li, materials science, Tsinghua University, 2015-2020
```

Collision:

```text
Another Wei Li, same university, adjacent department, overlapping years.
```

Decision:

```text
Affiliation alone is insufficient.
Use ORCID, email, advisor, lab, coauthors, topic, grant, and official CV.
If unresolved, report two clusters and mark ambiguous items.
```
