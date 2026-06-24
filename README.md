# Scholar Authorship Auditor for Codex

![version](https://img.shields.io/badge/version-v0.7.4-blue)
![status](https://img.shields.io/badge/status-active-brightgreen)
![platform](https://img.shields.io/badge/platform-Codex-111827)
![outputs](https://img.shields.io/badge/outputs-Markdown%20%2B%20Word-7c3aed)

[English](README.md) | [简体中文](README_zh.md)

`scholar-authorship-auditor` is a Codex skill for auditing a scholar's qualifying publications. It reconstructs the scholar's education and career timeline, resolves identity ambiguity, and finds papers where the scholar is first author, co-first author, corresponding author, or co-corresponding author.

In this skill, requests such as "all papers", "all articles", or "all publications" mean all papers that match those authorship rules. Ordinary middle-author papers are not included by default.

## Install

Ask Codex to install the skill for you. Send Codex this message:

```text
Please install the [lwq-star/scholar-authorship-auditor](https://github.com/lwq-star/scholar-authorship-auditor) skill for me.
```

After installation, start a new Codex chat so the skill index can refresh.

## Quick Start

```text
请使用这个 [$scholar-authorship-auditor](SKILL.md) skill，查找北京大学朴世龙发表的所有文章。
```

```text
Please use this [$scholar-authorship-auditor](SKILL.md) skill to find all articles published by Giovanni Forzieri of the European Commission, Joint Research Centre.
```

The chat answer and generated reports follow the user's request language. Paper titles, journal names, author names, DOI, PMID, arXiv IDs, and quoted source text stay in their original form unless translation is requested.

### Example result previews

These previews are generated from existing Markdown audit outputs. Full run outputs remain outside this public repository.

![Piao Shilong audit result preview](docs/images/piao-shilong-result-preview.png)

![Giovanni Forzieri audit result preview](docs/images/giovanni-forzieri-result-preview.png)

## What It Does

- Reconstructs the target scholar's education and career timeline.
- Resolves same-name ambiguity with institutions, coauthors, topics, identifiers, and official profiles.
- Uses OpenAlex batch enumeration first, then checks web pages or PDFs only for ambiguous boundary cases.
- Produces a compact publication list with `Year | Title | Journal | Scholar role`.
- Generates Markdown and Word reports.

## What Counts

Included by default:

- first-author papers;
- co-first-author papers with explicit equal-contribution evidence;
- corresponding-author papers with database, article, PDF, or publisher evidence;
- co-corresponding-author papers with explicit evidence.

Excluded by default:

- middle-author-only papers;
- last-author papers without corresponding-author evidence;
- patents, datasets, theses, news, editorials, corrections, posters, and consortium-only records;
- records with unresolved same-name collisions.

## Outputs

Default full runs create:

```text
<scholar-name>-authorship-audit-report.md
<scholar-name>-authorship-audit-report.docx
```

Default output location:

```text
<current-working-directory>/outputs/scholar-authorship-auditor/<YYYYMMDD>-<scholar-name-slug>/
```

## Repository Contents

```text
scholar-authorship-auditor/
  SKILL.md
  references/
  scripts/render_report.py
  assets/authorship-audit-template.docx
```

## Optional OpenAlex API Key

The skill works best when OpenAlex requests can use an API key. You can spend about 30 seconds creating a free OpenAlex account and getting a free API key from the OpenAlex platform. When you use this skill in Codex, give that API key to Codex at runtime or through a secret-safe channel.

This skill will not publish your API key online. Do not hard-code API keys into `SKILL.md`, scripts, README files, reports, logs, or public GitHub commits.
