# Scholar Authorship Auditor for Codex

![version](https://img.shields.io/badge/version-v0.7.3-blue)
![status](https://img.shields.io/badge/status-active-brightgreen)
![platform](https://img.shields.io/badge/platform-Codex-111827)
![workflow](https://img.shields.io/badge/workflow-OpenAlex%20batch%20first-0f766e)
![outputs](https://img.shields.io/badge/outputs-Markdown%20%2B%20Word-7c3aed)

[English](README.md) | [简体中文](README_zh.md)

这个 Codex skill 用于重建目标学者的教育和任职履历，并识别该学者满足以下署名角色的论文：第一作者、共同第一作者、通讯作者、共同通讯作者。

在这个 skill 中，“所有文章”“所有论文”“全部发表文章”默认指所有满足上述固定署名规则的论文。普通中间作者论文默认不纳入最终结果。

## 目录

- [功能亮点](#功能亮点)
- [30 秒安装](#30-秒安装)
- [校验安装](#校验安装)
- [快速开始](#快速开始)
- [OpenAlex API Key](#openalex-api-key)
- [默认输出](#默认输出)
- [报告结构](#报告结构)
- [什么算作该学者的论文](#什么算作该学者的论文)
- [目录结构](#目录结构)
- [故障排查](#故障排查)
- [维护校验](#维护校验)

## 功能亮点

| 功能 | 默认行为 |
|---|---|
| 署名范围 | 只纳入第一作者、共同第一作者、通讯作者、共同通讯作者论文 |
| 身份消歧 | 结合履历时间线、机构历史、标识符、合作者、主题和文章级署名证据 |
| 检索策略 | OpenAlex 批量枚举优先；网页/PDF 只核验模糊边界案例 |
| 语言 | 聊天回答和报告跟随用户提问语言 |
| 输出 | 只输出 Markdown 报告和 Word 报告 |
| 输出位置 | 当前工作目录下的 `outputs/scholar-authorship-auditor/` |

## 30 秒安装

下载或克隆这个文件夹后，把完整的 `scholar-authorship-auditor` 目录复制到 Codex 的 skills 目录。

Windows PowerShell，在包含 `scholar-authorship-auditor/` 的上一级目录运行：

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.codex\skills"
Copy-Item -Recurse -Force ".\scholar-authorship-auditor" "$env:USERPROFILE\.codex\skills\"
```

macOS/Linux，在包含 `scholar-authorship-auditor/` 的上一级目录运行：

```bash
mkdir -p "$HOME/.codex/skills"
cp -R ./scholar-authorship-auditor "$HOME/.codex/skills/"
```

安装后目录应类似：

```text
~/.codex/skills/scholar-authorship-auditor/SKILL.md
~/.codex/skills/scholar-authorship-auditor/references/
~/.codex/skills/scholar-authorship-auditor/scripts/
~/.codex/skills/scholar-authorship-auditor/assets/
```

安装后重启 Codex，或开启一个新聊天，让 skill 索引刷新。

## 校验安装

通用校验：

```bash
python path/to/skill-creator/scripts/quick_validate.py ~/.codex/skills/scholar-authorship-auditor
```

在这台 Windows 机器上可以运行：

```powershell
$env:PYTHONUTF8 = '1'
D:\app\miniforge\envs\spyder_env\python.exe C:\Users\A1827\.codex\skills\.system\skill-creator\scripts\quick_validate.py C:\Users\A1827\.codex\skills\scholar-authorship-auditor
```

预期结果：

```text
Skill is valid!
```

## 快速开始

让 Codex 使用这个 skill，并至少提供一个身份锚点，例如单位、ORCID、官方主页、研究领域或代表论文。

```text
请使用 $scholar-authorship-auditor 查找南方科技大学曾振中发表的所有文章。
```

```text
请使用 $scholar-authorship-auditor 审计宁波东方理工大学冯禹的第一作者和通讯作者论文。
```

用户用中文提问时，聊天回答和生成报告使用中文；用户用英文提问时，聊天回答和生成报告使用英文。论文题名、期刊名、作者名、DOI、PMID、arXiv ID 和原文引用默认保留原文，除非用户明确要求翻译。

## OpenAlex API Key

这个 skill 使用 OpenAlex API key 时效果最好。请通过环境变量或其他安全的 secret 渠道提供，不要把 API key 写进 `SKILL.md`、脚本、README、报告或日志。

Windows PowerShell：

```powershell
$env:OPENALEX_API_KEY = "your-api-key"
```

macOS/Linux：

```bash
export OPENALEX_API_KEY="your-api-key"
```

如果 OpenAlex 额度疑似耗尽，skill 应提醒用户，并建议用户花约 30 秒创建免费的 OpenAlex 账户以获取免费 API key，然后再决定是否切换到较慢的替代来源。

## 默认输出

完整运行默认生成两个文件：

```text
<scholar-name>-authorship-audit-report.md
<scholar-name>-authorship-audit-report.docx
```

默认输出位置：

```text
<当前工作目录>/outputs/scholar-authorship-auditor/<YYYYMMDD>-<scholar-name-slug>/
```

skill 安装目录 `.codex/skills` 不用于存放运行输出。

## 报告结构

默认报告包含：

1. 结果摘要。
2. 学者身份锚点。
3. 教育和任职时间线。
4. 符合条件的第一作者/共同第一作者/通讯作者/共同通讯作者论文。

默认论文表格保持简洁：

```text
年份 | 题名 | 期刊 | 学者角色
```

默认不输出原始 API 字段、Evidence Passport、排除日志、CSV 文件、缓存文件或其他配套文件。

## 什么算作该学者的论文

默认纳入：

- 第一作者论文；
- 有明确共同贡献证据的共同第一作者论文；
- 有数据库、文章页面、PDF 或出版商证据的通讯作者论文；
- 有明确证据的共同通讯作者论文。

默认排除：

- 仅为普通中间作者的论文；
- 没有通讯作者证据的最后作者论文；
- 只有泛泛 CRediT 贡献声明、但没有共同第一作者或通讯证据的论文；
- 专利、数据集、学位论文、新闻、社论、更正、海报和联盟作者记录；
- 存在无法解决的同名作者冲突的记录。

## 目录结构

```text
scholar-authorship-auditor/
  SKILL.md                         # skill 入口和核心流程
  assets/
    authorship-audit-template.docx # 固定 Word 报告模板
  references/                      # 按需加载的详细规则
  scripts/
    render_report.py               # Markdown 和 Word 报告渲染脚本
  README.md                        # 英文说明
  README_zh.md                     # 中文说明
```

## 故障排查

| 现象 | 检查事项 |
|---|---|
| Codex 没有加载 skill | 确认目录位于 `~/.codex/skills/scholar-authorship-auditor/`，然后开启新聊天 |
| 校验失败 | 检查 `SKILL.md` 是否存在，且 YAML frontmatter 是否包含 `name` 和 `description` |
| PowerShell 中文显示乱码 | 使用 `-Encoding UTF8` 读取文件，或设置 UTF-8 输出变量 |
| 没有生成报告文件 | 检查当前工作目录和 `outputs/scholar-authorship-auditor/` |
| OpenAlex 较慢或额度受限 | 通过环境变量设置 OpenAlex API key |

## 维护校验

修改 skill 后校验：

```powershell
$env:PYTHONUTF8 = '1'
D:\app\miniforge\envs\spyder_env\python.exe C:\Users\A1827\.codex\skills\.system\skill-creator\scripts\quick_validate.py C:\Users\A1827\.codex\skills\scholar-authorship-auditor
```

校验报告渲染脚本：

```powershell
D:\app\miniforge\envs\spyder_env\python.exe -B C:\Users\A1827\.codex\skills\scholar-authorship-auditor\scripts\render_report.py --help
```
