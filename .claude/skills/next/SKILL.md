---
name: next
description: 完成当前创作阶段，推进到下一阶段。在对谈/选题/视角/结构/改稿等阶段完成后使用。
allowed-tools: Read, Write, Glob
---

推进当前项目到下一阶段。

## 步骤

1. **找到当前项目**
   Glob 扫描所有 `cowriteai/projects/*/state.json`，读取后按 `updated_at` 取最新

2. **读取 state.json**，获取 `current_stage` 和 `stages_completed`

3. **更新 state.json**：
   - 将 `current_stage` 加入 `stages_completed`（去重）
   - `current_stage` 加 1
   - 更新 `updated_at` 为当前时间

4. **根据新阶段执行对应动作**：

| 新阶段 | 名称 | 动作 |
|--------|------|------|
| 2 | 选题收敛 | 读取所有 `dialogue/round_*.md` + `research/web_findings.md`（若有），调用 @agent-topic-planner 生成选题方案 |
| 3 | 视角确认 | 读取 `topic.md`，展示五种视角 + 各自示范开头，请用户选择 |
| 4 | 结构确认 | 读取 `angle.md`，根据视角推荐 RAWE 排序，展示每种排序效果，请用户确认 |
| 5 | 初稿撰写 | 读取 `topic.md` + `angle.md` + `structure.md` + 所有 `dialogue/` 文件，写完整初稿 → 保存 `draft_v1.md` |
| 6 | 文风打磨 | 调用 @agent-stylist，读取 `draft_v1.md` + `cowriteai/style_profile.md` → 输出 `draft_v2.md` |
| 7 | 用户改稿 | 展示 `draft_v2.md`，告知用户直接说想改什么，或 `/approve` 批准进入脚本化 |
| 8 | 脚本化 | 调用 @agent-shoot-director，读取最新 `draft_vN.md` → 输出 `script_v1.md` |
| 9 | 传播包装 | 调用 @agent-distribution-expert，读取 `script_v1.md` → 输出四份传播材料 |
| 10 | 终稿 | 提示：「传播材料已就绪，用 `/finalize` 输出终稿并归档。」 |

5. **输出确认**：`已推进到 Stage N：[阶段名]`

---

## 视角表（Stage 3 用）

| 视角 | 关系 | 示范开头 |
|------|------|---------|
| 俯视 | 专家教育粉丝 | 「今天讲清楚 AI 合同审查的三层能力边界」 |
| 平视 | 嘴替/家人们谁懂啊 | 「家人们我真的栓Q了，AI 写的合同……」 |
| 仰视 | 成长型/挑战 | 「挑战 30 天，让 AI 帮我打赢一场合同纠纷」 |
| 透视 | 反共识 | 「你以为 AI 不能做法律？错了」 |
| 侧视 | 叛逆 | 「别人都在卷大模型，我偏要让它当律师助理」 |

## RAWE 排序参考（Stage 4 用）

- **R** Result 结果（何同学式开头：「这是一个 XXX，我做的」）
- **A** Action 做法（干货/过程）
- **W** Why 为什么（痛点共鸣：「你是不是也遇到过……」）
- **E** Emotion 情绪（Vlog 常见：「哇好美啊」）

推荐排序参考：
- 俯视+干货 → 推荐 W-A-R-E
- 平视/吐槽 → 推荐 W-E-A-R
- Vlog 挑战 → 推荐 E-W-A-R
- 反共识 → 推荐 R-W-A-E
