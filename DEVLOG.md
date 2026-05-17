# CoWriteAI 开发日志

> 记录「为什么这样做」的决策日志，不是 git log 的替代品。

---

## 2026-05-17 — 里程碑 M1：架构重构

**问题：**
- Agent 文件命名错误：放在 `agents/<name>/SKILL.md` 子目录里，不符合官方规范
- CLAUDE.md 只是文档索引，不是操作手册；Claude 读完不知道怎么工作
- 工作流大脑分散在 `next` skill 里，用户必须记住 `/next` 命令才能推进流程
- `cowriteai` skill 同时承担项目管理、工作流调度、对话启动，职责混乱
- `approve`、`next`、`karpathy-guidelines` skill 在 CLAUDE.md 承担工作流后价值消失

**改动：**
- 5 个 agent 文件从 `agents/<name>/SKILL.md` 改为 `agents/<name>.md`（符合官方规范）
- CLAUDE.md 重写为完整操作手册：含会话启动协议、10 阶段处理细节（含视角表、RAWE 表）、推进规则、state.json 格式
- 删除 `next`、`approve`、`karpathy-guidelines` 三个 skill
- `cowriteai` skill 缩减为纯脚手架：只做 `new`（建目录+state.json）和 `list`（列项目）
- `finalize` skill 删掉虚假的「提取已确认方案」步骤，改为直接归档原文件
- 各 agent 内对 `/next`、`/approve` 的引用改为对话式引导

**核心决策：**
CLAUDE.md 应是系统大脑，不是目录。工作流逻辑（何时推进、调用哪个 Agent、写什么文件）属于 CLAUDE.md，不属于 skill。Skill 只封装有明确开始/结束的批量文件操作。

---

## 2026-05-17 — 里程碑 M0：项目初始化

**完成内容：**
- 根据 PRD（修订版）完成完整项目骨架搭建
- 按 Claude Code 官方规范创建 `.claude/skills/` 和 `.claude/agents/` 目录结构
- 实现 7 个 skill（cowriteai、next、approve、finalize、git-commit、done、karpathy-guidelines）
- 实现 5 个 subagent（dialogue-editor、topic-planner、stylist、shoot-director、distribution-expert）
- 写入 CLAUDE.md（含 Karpathy Guidelines）

**关键决策：**
- 删除 Stage 0（灵感入库）和 Stage 1/2 的本地素材库，简化为「口喷 + 联网搜索」输入
- 删除多轮会话 messages.jsonl 方案，改为每次 subagent 读取已有文件获取上下文
- CLI 命令与 Skill 合并，Section 6 即 Skill 列表，无单独 CLI 层
- 飞书/IMA 接入推迟到 P2
- git-commit skill 完全自动生成 commit message，不询问用户

**下一步：**
- 填写 `cowriteai/style_profile.md`
- 用 `/cowriteai new "第一个测试选题"` 走通完整流程
- 验证各 subagent 的上下文读取是否正确
