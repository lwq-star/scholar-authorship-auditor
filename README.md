# Scholar Authorship Auditor for Codex

![version](https://img.shields.io/badge/version-v0.7.3-blue)
![status](https://img.shields.io/badge/status-active-brightgreen)
![platform](https://img.shields.io/badge/platform-Codex-111827)
![workflow](https://img.shields.io/badge/workflow-OpenAlex%20batch%20first-0f766e)
![outputs](https://img.shields.io/badge/outputs-Markdown%20%2B%20Word-7c3aed)

[English](README.md) | [简体中文](README_zh.md)

Reconstruct a scholar's education and career timeline, then identify papers where the scholar is verified as first author, co-first author, corresponding author, or co-corresponding author.

In this skill, "all papers", "all articles", and "all publications" mean all papers that satisfy that fixed authorship rule. Ordinary middle-author papers are not included by default.

## Contents

- [Highlights](#highlights)
- [Install In 30 Seconds](#install-in-30-seconds)
- [Verify Installation](#verify-installation)
- [Quick Start](#quick-start)
- [OpenAlex API Key](#openalex-api-key)
- [What It Produces](#what-it-produces)
- [Report Format](#report-format)
- [What Counts As A Paper](#what-counts-as-a-paper)
- [Folder Layout](#folder-layout)
- [Troubleshooting](#troubleshooting)
- [Maintenance](#maintenance)

## Highlights

| Feature | Default behavior |
|---|---|
| Authorship scope | First, co-first, corresponding, and co-corresponding papers only |
| Identity disambiguation | Timeline, institution history, identifiers, coauthors, topics, and article-level role evidence |
| Retrieval strategy | OpenAlex batch enumeration first; web/PDF checks only for ambiguous boundary cases |
| Language | Chat answers and reports follow the user's request language |
| Outputs | Markdown report and Word report only |
| Output location | Current working directory under `outputs/scholar-authorship-auditor/` |

## Install In 30 Seconds

Download or clone this folder, then copy the complete `scholar-authorship-auditor` directory into your Codex skills directory.

Windows PowerShell, run from the parent directory that contains `scholar-authorship-auditor/`:

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.codex\skills"
Copy-Item -Recurse -Force ".\scholar-authorship-auditor" "$env:USERPROFILE\.codex\skills\"
```

macOS/Linux, run from the parent directory that contains `scholar-authorship-auditor/`:

```bash
mkdir -p "$HOME/.codex/skills"
cp -R ./scholar-authorship-auditor "$HOME/.codex/skills/"
```

The installed skill should look like this:

```text
~/.codex/skills/scholar-authorship-auditor/SKILL.md
~/.codex/skills/scholar-authorship-auditor/references/
~/.codex/skills/scholar-authorship-auditor/scripts/
~/.codex/skills/scholar-authorship-auditor/assets/
```

Restart Codex or start a new chat after installation so the skill index can refresh.

## Verify Installation

Generic check:

```bash
python path/to/skill-creator/scripts/quick_validate.py ~/.codex/skills/scholar-authorship-auditor
```

On this Windows machine:

```powershell
$env:PYTHONUTF8 = '1'
D:\app\miniforge\envs\spyder_env\python.exe C:\Users\A1827\.codex\skills\.system\skill-creator\scripts\quick_validate.py C:\Users\A1827\.codex\skills\scholar-authorship-auditor
```

Expected result:

```text
Skill is valid!
```

## Quick Start

Ask Codex to use the skill and provide at least one identity anchor, such as institution, ORCID, official homepage, field, or representative paper.

```text
Use $scholar-authorship-auditor to find all papers by Zhenzhong Zeng at Southern University of Science and Technology.
```

```text
Use $scholar-authorship-auditor to audit all first-author and corresponding-author papers by Yu Feng at Eastern Institute of Technology, Ningbo.
```

For Chinese requests, the chat answer and generated reports are written in Chinese. For English requests, they are written in English. Paper titles, journal names, author names, DOI, PMID, arXiv IDs, and quoted source text stay in their original form unless translation is requested.

## OpenAlex API Key

The skill works best when OpenAlex requests can use an API key. Set it through an environment variable or another secret-safe channel. Do not hard-code API keys into `SKILL.md`, scripts, README files, reports, or logs.

Windows PowerShell:

```powershell
$env:OPENALEX_API_KEY = "your-api-key"
```

macOS/Linux:

```bash
export OPENALEX_API_KEY="your-api-key"
```

If OpenAlex quota appears exhausted, the skill should tell the user and recommend creating a free OpenAlex account for a free API key before falling back to slower alternate sources.

## What It Produces

Default full runs create two files:

```text
<scholar-name>-authorship-audit-report.md
<scholar-name>-authorship-audit-report.docx
```

Default output location:

```text
<current-working-directory>/outputs/scholar-authorship-auditor/<YYYYMMDD>-<scholar-name-slug>/
```

The skill installation directory under `.codex/skills` is not used for run outputs.

## Report Format

Default reports contain:

1. Executive summary.
2. Scholar identity and anchors.
3. Education and career timeline.
4. Qualifying first/co-first/corresponding/co-corresponding journal articles.

The default paper table is compact:

```text
Year | Title | Journal | Scholar role
```

Raw API fields, Evidence Passport files, rejection logs, CSV files, cache files, and extra companion files are not emitted by default.

## What Counts As A Paper

Included by default:

- first-author papers;
- co-first-author papers with explicit equal-contribution evidence;
- corresponding-author papers with database, article, PDF, or publisher evidence;
- co-corresponding-author papers with explicit evidence.

Excluded by default:

- middle-author-only papers;
- last-author papers without corresponding-author evidence;
- generic CRediT contribution statements without co-first or correspondence evidence;
- patents, datasets, theses, news, editorials, corrections, posters, and consortium-only records;
- records with unresolved same-name collisions.

## Folder Layout

```text
scholar-authorship-auditor/
  SKILL.md                         # Skill entrypoint and core workflow
  assets/
    authorship-audit-template.docx # Fixed Word report template
  references/                      # Detailed rules loaded only when needed
  scripts/
    render_report.py               # Deterministic Markdown and Word renderer
  README.md                        # English overview
  README_zh.md                     # Chinese overview
```

## Troubleshooting

| Symptom | What to check |
|---|---|
| Codex does not load the skill | Confirm the folder is under `~/.codex/skills/scholar-authorship-auditor/` and start a new chat |
| Validation fails | Check that `SKILL.md` exists and has valid YAML frontmatter with `name` and `description` |
| Chinese text looks garbled in PowerShell | Read files with `-Encoding UTF8` or set UTF-8 output variables |
| No report files appear | Check the current working directory and `outputs/scholar-authorship-auditor/` |
| OpenAlex is slow or quota-limited | Set an OpenAlex API key through an environment variable |

## Maintenance

Validate the skill after edits:

```powershell
$env:PYTHONUTF8 = '1'
D:\app\miniforge\envs\spyder_env\python.exe C:\Users\A1827\.codex\skills\.system\skill-creator\scripts\quick_validate.py C:\Users\A1827\.codex\skills\scholar-authorship-auditor
```

Validate the report renderer:

```powershell
D:\app\miniforge\envs\spyder_env\python.exe -B C:\Users\A1827\.codex\skills\scholar-authorship-auditor\scripts\render_report.py --help
```
