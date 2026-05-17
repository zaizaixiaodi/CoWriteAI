---
name: done
description: 开发里程碑记录命令。自动从 git diff 分析本次完成的工作，追加一条记录到 DEVLOG.md，然后自动提交推送。开发过程使用，每完成一个功能节点后调用。
allowed-tools: Read, Write, Bash, Glob
---

记录本次开发里程碑并提交代码。不询问用户，全程自动执行。

## 步骤

1. **读取 DEVLOG.md** 了解已有格式和当前里程碑

2. **运行 `git diff HEAD`** 和 `git status` 分析本次变更内容

3. **自动生成 DEVLOG 条目**，从变更中推断：
   ```markdown
   ## [yyyy-mm-dd] — [里程碑名称，从变更推断]

   **完成内容：**
   - [从 diff 提取的关键变更1]
   - [关键变更2]

   **关键决策：**
   - [若有明显的设计选择，列出；否则省略此节]

   **下一步：**
   - [根据当前状态推断的合理下一步]
   ```

4. **追加到 DEVLOG.md 末尾**（不覆盖已有内容）

5. **执行 git 提交**：
   - `git add .`
   - 自动生成 commit message（格式：`docs: 更新 DEVLOG + [本次变更摘要]`）
   - `git commit -m "..."`
   - `git push origin HEAD`

6. 输出：`已记录里程碑并推送`
