---
name: git-commit
description: 自动分析当前所有变更，生成 commit message，提交并推送到 GitHub。不询问用户任何问题，完全自动执行。开发过程使用。
allowed-tools: Bash
---

自动提交当前所有变更到 GitHub。不询问用户任何问题，直接执行。

## 步骤

1. 运行 `git status` 查看变更文件列表

2. 运行 `git diff HEAD` 查看具体变更内容

3. **根据变更自动生成 commit message**：
   - 格式：`<type>: <summary>`
   - type 规则：
     - `feat` — 新增 skill、agent、功能
     - `fix` — 修复问题
     - `docs` — DEVLOG、CLAUDE.md、README 更新
     - `refactor` — 重构已有 skill/agent prompt
     - `chore` — 配置、目录结构、gitignore 等杂项
   - summary：一句话描述核心变更（中文即可，30 字以内）
   - 若涉及多个独立变更，取最重要的作主 message，其余列为 body bullet points

4. 执行以下命令（依次运行）：
   ```
   git add .
   git commit -m "<生成的 commit message>"
   git push origin HEAD
   ```

5. 输出推送结果（成功或失败原因）

若 git 仓库未初始化，先运行：
```
git init
git remote add origin https://github.com/zaizaixiaodi/CoWriteAI.git
```
然后继续执行步骤 4。
