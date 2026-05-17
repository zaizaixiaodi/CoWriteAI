# CoWriteAI 开发日志

> 记录「为什么这样做」的决策日志，不是 git log 的替代品。

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
