---
name: clarify
description: 通过一次一个问题的追问，帮助用户把粗糙想法、模糊需求或口述内容澄清成可继续设计或规划的输入。用户想“聊清楚”“澄清一下”“整理想法”“帮我把想法问清楚”“grill 一下”时使用本技能。执行时读取 `.workflow/decisions.md`、`.workflow/progress.md` 和 `.workflow/templates/decisions.md`；只把已确认的原始意图、关键决策和决策变更写入 `.workflow/decisions.md`，不更新 `.workflow/progress.md`。
---

# clarify

追问用户关于当前想法的每个关键不确定点，直到双方对原始意图、边界和下一步去向形成共识。

一次只问一个问题，并等待用户反馈后再继续。每个问题都给出你的推荐答案，方便用户确认或纠正。

如果问题可以通过阅读项目文件回答，先阅读文件，不要把问题丢给用户。

开始前读取：

- `.workflow/decisions.md`
- `.workflow/progress.md`
- `.workflow/templates/decisions.md`

`.workflow/progress.md` 只作为上下文，不要更新。

当用户确认原始意图、关键决策或决策变更时，更新 `.workflow/decisions.md`。写入前先读取 `.workflow/templates/decisions.md`，沿用其中的字段和示例风格。

不要设计方案、规划版本、拆解任务或创建长期 docs 文档。clarify 只负责把想法澄清到可以交给后续 Design 或 Version Planning。
