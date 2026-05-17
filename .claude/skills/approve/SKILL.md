---
name: approve
description: 批准当前阶段的产物，将其确认为正式版本。在选定选题、确认视角/结构、或草稿满意时使用。可选参数指定批准内容（如 topic:2）。
argument-hint: "[topic:N | draft | script]"
allowed-tools: Read, Write, Glob
---

批准当前产物并记录确认状态。

**参数：** `$ARGUMENTS`

## 步骤

1. **找到当前项目**
   Glob 扫描所有 `cowriteai/projects/*/state.json`，读取后按 `updated_at` 取最新

2. **读取 state.json**，确定当前阶段

3. **根据阶段和参数执行批准**：

   **Stage 2（选题收敛）**
   - 读取 `topic.md`
   - 若参数包含 `topic:N`，在文件中标记第 N 个选题为已选（加 `✅ 已选` 标注）
   - 若无参数，提示用户指明选哪个：「请告诉我选几号，或直接说标题」
   - 确认后在 `state.json` 中记录 `"topic_approved": true`

   **Stage 3（视角确认）**
   - 读取用户在对话中说的视角选择，写入 `angle.md`
   - 格式：
     ```markdown
     # 视角确认
     **选定视角：** [视角名]
     **示范开头：** [对应示范]
     **博主补充：** [用户的补充说明，若有]
     ```

   **Stage 4（结构确认）**
   - 读取用户确认的 RAWE 排序，写入 `structure.md`
   - 格式：
     ```markdown
     # 结构确认
     **RAWE 排序：** [如 W-A-R-E]
     **逻辑说明：** [每个要素对应哪段内容]
     ```

   **Stage 7（改稿循环）**
   - 确认最新 `draft_vN.md` 为批准版本，在 `state.json` 记录版本号
   - 提示：「已批准 draft_vN，用 `/next` 进入脚本化。」

   **其他阶段**
   - 在 `state.json` 记录当前阶段已批准，提示用 `/next` 继续

4. **更新 state.json** 的 `updated_at`
