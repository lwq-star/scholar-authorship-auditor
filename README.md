**English** | [简体中文](README_zh.md)

# Scholar Authorship Auditor Skill

> Find the papers that actually credit a scholar as a first or corresponding author, without mixing in namesakes.

[GitHub repository](https://github.com/lwq-star/scholar-authorship-auditor)

`scholar-authorship-auditor` is a Codex skill for building a verified publication list for a scholar. Give it the scholar's name and at least one identity clue, such as an institution, research field, ORCID, homepage, or representative paper. Codex reconstructs the scholar's education and career timeline, separates namesakes, checks authorship roles, and returns readable Markdown and Word reports.

The default result includes papers where the target scholar is the **first author, co-first author, corresponding author, or co-corresponding author**. Ordinary middle-author papers are not included unless you explicitly ask for a broader coauthorship list.

## Where to use it

Use this skill in a Codex Desktop or Codex CLI conversation that can load local skills. It is not a standalone website, literature database, or Python package.

It is useful when you want Codex to:

- find a scholar's first-author and corresponding-author papers;
- check whether a CV, ORCID, Google Scholar profile, or publication list contains missing or misattributed records;
- separate scholars who share the same or a similar name;
- reconstruct an education and employment timeline before attributing papers;
- turn the verified results into a readable Markdown and Word report.

## Why this project

A name is not a reliable author identifier. Scholars may publish under different spellings, move between institutions, share a name with other researchers, or have profiles that merge records from several people. Author order alone also does not prove who is a corresponding or co-first author.

General literature search is useful for finding candidates, but a long result list is not the same as a trustworthy publication record. This skill adds an evidence-based workflow that:

- establishes who the scholar is before searching for papers;
- checks affiliations, dates, identifiers, coauthors, and research topics against the scholar's timeline;
- verifies first, co-first, corresponding, and co-corresponding roles from article-level evidence;
- merges preprints and published versions instead of counting the same work twice;
- excludes unresolved records rather than silently presenting them as confirmed.

## Installation

### Install from GitHub with Codex

Send Codex this request:

```text
Please use $skill-installer to install scholar-authorship-auditor from https://github.com/lwq-star/scholar-authorship-auditor. The skill is at the repository root (path `.`); install it with the name `scholar-authorship-auditor`.
```

Call the skill on the next turn. If Codex has not discovered it yet, start a new task and call it by name.

### Install manually

1. Download or clone the [GitHub repository](https://github.com/lwq-star/scholar-authorship-auditor).
2. Copy the repository folder to `$CODEX_HOME/skills/scholar-authorship-auditor`. If `CODEX_HOME` is not set, the usual locations are:
   - Windows: `%USERPROFILE%\.codex\skills\scholar-authorship-auditor`
   - macOS/Linux: `~/.codex/skills/scholar-authorship-auditor`
3. Confirm that `SKILL.md`, `references/`, `scripts/`, and `assets/` are directly inside that folder.
4. Call `$scholar-authorship-auditor` on the next turn. If it is not discovered, start a new Codex task and try again.

## Before you start

Provide:

- the scholar's full name;
- at least one identity clue: current or past institution, research field, ORCID, official homepage or CV, representative paper, native-script name, advisor, lab, or recurring collaborator;
- the language you want for the report, if it differs from your request language.

For a common name, an institution, ORCID, homepage, or representative paper greatly reduces false matches. You do not need to choose a date range or authorship rule: the default scope is fixed to first, co-first, corresponding, and co-corresponding papers.

## Quick start

```text
Please use $scholar-authorship-auditor.
Scholar: Giovanni Forzieri
Identity clue: European Commission, Joint Research Centre
Task: Find all papers that match the skill's default authorship scope.
Output: English Markdown and Word reports.
```

```text
请使用 $scholar-authorship-auditor。
学者：朴世龙
身份线索：北京大学，生态学
任务：核验其所有符合默认署名范围的论文。
输出：中文 Markdown 和 Word 报告。
```

Paper titles, journal names, author names, identifiers, and quoted source text remain in their original language unless you ask for translation.

## Contents

- [Where to use it](#where-to-use-it)
- [Why this project](#why-this-project)
- [Installation](#installation)
- [Before you start](#before-you-start)
- [Quick start](#quick-start)
- [Example results](#example-results)
- [How it works](#how-it-works)
- [What you receive](#what-you-receive)
- [Which papers are included](#which-papers-are-included)
- [Optional OpenAlex API key](#optional-openalex-api-key)
- [Limits](#limits)
- [Feedback and contact](#feedback-and-contact)
- [Acknowledgements](#acknowledgements)

## Example results

These previews come from completed scholar audits. Full run outputs remain outside this public repository.

![Piao Shilong audit result preview](docs/images/piao-shilong-result-preview.png)

![Giovanni Forzieri audit result preview](docs/images/giovanni-forzieri-result-preview.png)

## How it works

1. Codex confirms the scholar's identity from the name and identity clue you provide.
2. It reconstructs the scholar's education and employment timeline, including name variants and institution changes.
3. It gathers candidate papers from bibliographic databases and official sources, using OpenAlex batch records first when available.
4. It compares each candidate with the timeline, affiliations, identifiers, coauthors, and research topics to remove namesakes.
5. It checks author order, equal-contribution notes, correspondence information, and publisher or database evidence.
6. It merges duplicate versions, excludes unresolved records, and writes the final reports.

Publisher pages and PDFs are reserved for records that cannot be settled from reliable structured metadata. This keeps the audit focused while still checking ambiguous or high-stakes cases closely.

## What you receive

A normal full run creates:

- an executive summary with the number of verified papers;
- a scholar identity summary and education/career timeline;
- a compact table with `Year | Title | Journal | Scholar role`;
- a Markdown report for easy review and reuse;
- a Word report for sharing or further editing.

Default filenames:

```text
<scholar-name>-authorship-audit-report.md
<scholar-name>-authorship-audit-report.docx
```

Default output location:

```text
<current-working-directory>/outputs/scholar-authorship-auditor/<YYYYMMDD>-<scholar-name-slug>/
```

## Which papers are included

Included by default:

- first-author papers;
- co-first-author papers with explicit equal-contribution evidence;
- corresponding-author papers supported by reliable database or article evidence;
- co-corresponding-author papers with explicit evidence.

Excluded by default:

- ordinary middle-author papers;
- last-author papers without corresponding-author evidence;
- patents, datasets, theses, news items, editorials, corrections, posters, and consortium-only records;
- standalone preprints, discussion papers, and posted manuscripts unless you explicitly request provisional records;
- records whose identity or authorship role cannot be resolved.

In this project, requests such as "all papers", "all articles", or "all publications" mean all verified papers within this default authorship scope.

## Optional OpenAlex API key

The skill works best when OpenAlex requests can use a free API key. An API key improves access to full author-work enumeration, especially for scholars with long publication histories. Provide it only at runtime through an environment variable or another secret-safe channel.

Never place an API key in `SKILL.md`, scripts, README files, reports, logs, or public commits. If OpenAlex is unavailable or its quota is exhausted, the skill can continue with other sources but will report the resulting coverage limitation.

## Limits

- The audit depends on public and accessible evidence; paywalls, missing metadata, or unavailable contribution notes can leave some roles unresolved.
- A high-coverage result is not a claim that every database in the world has been searched.
- The default report is a verified leadership/correspondence paper list, not a complete list of every paper on which the scholar appears.
- The report supports human review; it does not replace an official CV, publisher record, institutional verification, or the scholar's own confirmation.

## Feedback and contact

Bug reports, unclear instructions, and improvement suggestions are welcome. Please open a [GitHub issue](https://github.com/lwq-star/scholar-authorship-auditor/issues) and, when possible, include the scholar identity clues you used, the unexpected result, and the relevant source link. Do not post API keys, passwords, confidential CVs, or other sensitive information.

For feedback you prefer not to post publicly, contact [laiwenqinstar@gmail.com](mailto:laiwenqinstar@gmail.com).

## Acknowledgements

This project benefits from the open scholarly metadata ecosystem. We thank OpenAlex, Crossref, ORCID, Semantic Scholar, PubMed / Europe PMC, dblp, arXiv, publisher article pages, institutional profiles, and official CVs for making scholar identity, publication records, and publication versions discoverable and verifiable.

We also thank the [LINUX DO](https://linux.do/) community and platform for its technical exchange, feedback, and support.

This project is independent and is not endorsed by the data sources or platforms named above.
