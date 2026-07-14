[English](README.md) | **简体中文**

# 学者第一/通讯作者论文核验 Skill

> 找到一位学者确实署名为第一作者或通讯作者的论文，同时排除混入的同名作者记录。

[GitHub 仓库](https://github.com/lwq-star/scholar-authorship-auditor)

`scholar-authorship-auditor` 是一个用于核验学者署名论文的 Codex skill。你只需要提供学者姓名和至少一条身份线索，例如任职机构、研究领域、ORCID、个人主页或代表作。Codex 会重建其教育与任职时间线，区分同名作者，核验署名角色，并生成便于阅读的 Markdown 和 Word 报告。

默认结果只包括目标学者担任**第一作者、共同第一作者、通讯作者或共同通讯作者**的论文。普通中间作者论文不会纳入，除非你明确要求更广泛的合作论文清单。

## 在哪里使用

这个 skill 要在支持本地 skills 的 Codex Desktop 或 Codex CLI 对话中使用。它不是独立网站、文献数据库，也不是可以在 Python 中 `import` 的包。

适合用它完成这些任务：

- 查找一位学者的第一作者和通讯作者论文；
- 检查 CV、ORCID、Google Scholar 或已有论文清单中是否存在遗漏或错归；
- 区分姓名相同或相近的不同学者；
- 先重建教育和任职经历，再判断论文归属；
- 把核验结果整理成易读的 Markdown 和 Word 报告。

## 为什么需要这个项目

姓名不是可靠的作者标识。学者可能使用不同拼写发表论文，也可能更换机构、与其他研究者同名，或在公开学术主页中混入他人的记录。仅凭作者顺序，也不能证明谁是通讯作者或共同第一作者。

普通文献检索适合发现候选记录，但结果多并不等于论文清单可靠。本 skill 增加了一套基于证据的核验流程：

- 先确认“这个学者是谁”，再开始归集论文；
- 用任职机构、时间、作者标识、合作者和研究主题核对每条记录；
- 从论文级证据核验第一作者、共同第一作者、通讯作者和共同通讯作者身份；
- 合并预印本与正式发表版本，避免同一成果重复计数；
- 无法解决的记录不冒充已确认结果。

## 安装

### 让 Codex 从 GitHub 安装

给 Codex 发送下面这段请求：

```text
请使用 $skill-installer 从 https://github.com/lwq-star/scholar-authorship-auditor 安装 scholar-authorship-auditor。该 skill 位于仓库根目录（路径 `.`），安装名称使用 `scholar-authorship-auditor`。
```

安装后可在下一轮调用。如果 Codex 还没有识别新 skill，请新建一个任务并用名称调用。

### 手动安装

1. 下载或克隆 [GitHub 仓库](https://github.com/lwq-star/scholar-authorship-auditor)。
2. 将仓库文件夹复制到 `$CODEX_HOME/skills/scholar-authorship-auditor`。如果没有设置 `CODEX_HOME`，常见位置是：
   - Windows：`%USERPROFILE%\.codex\skills\scholar-authorship-auditor`
   - macOS/Linux：`~/.codex/skills/scholar-authorship-auditor`
3. 确认该目录下直接包含 `SKILL.md`、`references/`、`scripts/` 和 `assets/`。
4. 下一轮使用 `$scholar-authorship-auditor` 调用。如果没有识别，请新建一个 Codex 任务后重试。

## 使用前需要准备

请提供：

- 学者全名；
- 至少一条身份线索：现任或曾任机构、研究领域、ORCID、官方主页或 CV、代表作、本国语言姓名、导师、实验室或长期合作者；
- 如果报告语言与提问语言不同，再说明希望使用的语言。

如果姓名很常见，提供任职机构、ORCID、个人主页或代表作可以显著减少错配。你不需要选择年份范围或署名规则：默认范围固定为第一作者、共同第一作者、通讯作者和共同通讯作者论文。

## 快速开始

```text
请使用 $scholar-authorship-auditor。
学者：朴世龙
身份线索：北京大学，生态学
任务：核验其所有符合默认署名范围的论文。
输出：中文 Markdown 和 Word 报告。
```

```text
Please use $scholar-authorship-auditor.
Scholar: Giovanni Forzieri
Identity clue: European Commission, Joint Research Centre
Task: Find all papers that match the skill's default authorship scope.
Output: English Markdown and Word reports.
```

论文题名、期刊名、作者名、标识符和引用的来源原文默认保留原语言，除非你明确要求翻译。

## 目录

- [在哪里使用](#在哪里使用)
- [为什么需要这个项目](#为什么需要这个项目)
- [安装](#安装)
- [使用前需要准备](#使用前需要准备)
- [快速开始](#快速开始)
- [真实结果预览](#真实结果预览)
- [它会怎样处理任务](#它会怎样处理任务)
- [你会得到什么](#你会得到什么)
- [哪些论文会被纳入](#哪些论文会被纳入)
- [可选 OpenAlex API Key](#可选-openalex-api-key)
- [使用限制](#使用限制)
- [反馈与联系](#反馈与联系)
- [致谢](#致谢)

## 真实结果预览

下图来自已经完成的学者论文审计。完整运行结果仍保留在公开仓库之外。

![朴世龙审计结果预览](docs/images/piao-shilong-result-preview.png)

![Giovanni Forzieri 审计结果预览](docs/images/giovanni-forzieri-result-preview.png)

## 它会怎样处理任务

1. Codex 先根据姓名和身份线索确认目标学者。
2. 重建该学者的教育与任职时间线，包括姓名变体和机构变动。
3. 从文献数据库和官方来源收集候选论文；条件允许时优先批量读取 OpenAlex 记录。
4. 用时间线、署名机构、作者标识、合作者和研究主题逐条排除同名作者。
5. 核对作者顺序、共同贡献说明、通讯信息，以及出版社或数据库证据。
6. 合并重复版本，排除无法确认的记录，并生成最终报告。

只有结构化元数据无法解决问题时，才进一步检查出版社页面或 PDF。这样既能保持检索效率，也能认真处理模糊或高风险案例。

## 你会得到什么

一次正常的完整运行会生成：

- 一段执行摘要，说明最终核验论文数量；
- 学者身份简介和教育/任职时间线；
- 一张紧凑论文表：`年份 | 题名 | 期刊 | 学者角色`；
- 便于检查和复用的 Markdown 报告；
- 便于分享和继续编辑的 Word 报告。

默认文件名：

```text
<scholar-name>-authorship-audit-report.md
<scholar-name>-authorship-audit-report.docx
```

默认输出位置：

```text
<当前工作目录>/outputs/scholar-authorship-auditor/<YYYYMMDD>-<scholar-name-slug>/
```

## 哪些论文会被纳入

默认纳入：

- 第一作者论文；
- 有明确共同贡献证据的共同第一作者论文；
- 有可靠数据库或论文证据支持的通讯作者论文；
- 有明确证据支持的共同通讯作者论文。

默认排除：

- 普通中间作者论文；
- 没有通讯作者证据的最后作者论文；
- 专利、数据集、学位论文、新闻、社论、更正、海报和仅以联盟署名的记录；
- 独立预印本、讨论稿和其他未正式发表的稿件，除非你明确要求纳入暂定记录；
- 无法确认学者身份或署名角色的记录。

在本项目中，“所有论文”“所有文章”或“全部发表文章”均指这个默认署名范围内经过核验的全部论文。

## 可选 OpenAlex API Key

如果 OpenAlex 请求可以使用免费的 API key，本 skill 的运行效果会更好。对于论文数量较多的学者，API key 有助于完整读取其作者作品记录。请只在运行时通过环境变量或其他安全的 secret 渠道提供。

不要把 API key 写进 `SKILL.md`、脚本、README、报告、日志或公开 commit。如果 OpenAlex 暂时不可用或额度耗尽，本 skill 可以改用其他来源继续检索，但会说明由此产生的覆盖范围限制。

## 使用限制

- 审计依赖公开且能够访问的证据；付费墙、元数据缺失或贡献说明不可见，都可能导致部分角色无法确认。
- “高覆盖率”不代表世界上所有数据库都已经被逐一检索。
- 默认报告是一份经过核验的第一/通讯作者论文清单，不是该学者参与过的所有合作论文全集。
- 报告用于辅助人工复核，不能替代官方 CV、出版社记录、机构证明或学者本人确认。

## 反馈与联系

欢迎反馈 bug、说明不清和改进建议。请优先提交 [GitHub Issue](https://github.com/lwq-star/scholar-authorship-auditor/issues)；条件允许时，请附上使用过的学者身份线索、异常结果和相关来源链接。请勿公开 API key、密码、保密 CV 或其他敏感信息。

如不便公开反馈，可以发送邮件至 [laiwenqinstar@gmail.com](mailto:laiwenqinstar@gmail.com)。

## 致谢

本项目受益于开放学术元数据生态。感谢 OpenAlex、Crossref、ORCID、Semantic Scholar、PubMed / Europe PMC、dblp、arXiv、出版社论文页面、机构主页和官方 CV 等公开来源，使学者身份、论文记录和出版版本能够被发现与核验。

同时感谢 [LINUX DO](https://linux.do/) 社区与平台提供的技术交流、反馈和支持。

本项目为独立项目，不代表上述数据来源或平台对本项目的背书。
