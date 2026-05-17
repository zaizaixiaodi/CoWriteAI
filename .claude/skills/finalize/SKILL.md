---
name: finalize
description: 输出终稿文件并归档项目。在 Stage 9 传播包装完成、用户确认所有材料后使用。
allowed-tools: Read, Write, Glob, Bash
---

输出终稿并归档当前项目。

## 步骤

1. **找到当前项目**
   Glob 扫描所有 `cowriteai/projects/*/state.json`，读取后按 `updated_at` 取最新

2. **检查必要文件**，若缺失则列出并提示：
   - `script_v1.md`（必须）
   - `distribution/titles.md`（必须）
   - `distribution/covers.md`（建议）
   - `distribution/hooks.md`（建议）

3. **生成 script_final.md**
   读取最新 `script_vN.md`（按版本号最大的），在文件头部加入终稿标注：
   ```markdown
   # 终稿：[标题]
   **Episode：** epXXX
   **定稿时间：** yyyy-mm-dd
   ---
   [原脚本内容]
   ```
   写入 `script_final.md`

4. **生成 shotlist.md**
   从 `script_final.md` 的三栏表格中提取所有 B-roll 建议，整理成拍摄清单：
   ```markdown
   # 拍摄清单：[标题]
   
   | # | 场景/B-roll | 拍摄要点 | 时长估计 |
   |---|------------|---------|---------|
   | 1 | 痛苦皱眉特写 | 强调情绪 | 2-3秒 |
   ```

5. **创建 distribution/final/ 目录**
   从 `distribution/titles.md`、`covers.md`、`hooks.md`、`notes.md` 中提取用户已确认的方案，复制/摘录到 `distribution/final/` 下各自文件

6. **更新 state.json**：
   ```json
   {
     "current_stage": 10,
     "stages_completed": [...所有阶段...],
     "finalized_at": "<当前时间 ISO 8601 +08:00>",
     "updated_at": "<当前时间>"
   }
   ```

7. **输出归档确认**：
   ```
   ✅ epXXX「标题」已归档

   终稿文件：
   · script_final.md      — 完整脚本
   · shotlist.md          — 拍摄清单
   · distribution/final/  — 传播材料终版
   ```
