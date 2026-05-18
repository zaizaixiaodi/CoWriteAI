---
name: cowriteai
description: 项目脚手架。新建脚本项目（创建目录和 state.json）或列出所有已有项目。
argument-hint: "new \"标题\" | list"
allowed-tools: Read, Write, Glob, Bash
---

**参数：** `$ARGUMENTS`

---

## `new "标题"`

1. Glob 扫描 `cowriteai/projects/` 下所有子目录，提取最大编号，新建时加 1（首个为 ep001）
2. 将标题转为英文 slug（小写字母+短横线，最长 30 字符，无法转换用拼音缩写）
3. 用 Bash 创建目录：`cowriteai/projects/<epXXX-slug>/research/`、`dialogue/`、`distribution/`
4. 写入 `state.json`：
   ```json
   {
     "episode_id": "epXXX",
     "slug": "...",
     "title": "...",
     "created_at": "<ISO 8601 +08:00>",
     "updated_at": "<ISO 8601 +08:00>",
     "current_stage": 1,
     "stages_completed": []
   }
   ```
5. 输出：`已立项 epXXX「标题」→ cowriteai/projects/epXXX-slug/`
6. 告知用户：「项目已建好，告诉我你想聊什么，我来启动对谈。」

---

## `list`

Glob 扫描所有 `cowriteai/projects/*/state.json`，读取后按 `updated_at` 降序输出：

```
EP     标题                    阶段           更新时间
ep002  AI 帮律师写合同          Stage 3 视角   2026-05-17
ep001  不懂 Prompt 的律师       Stage 8 脚本   2026-05-15
```

若 `projects/` 为空：「还没有项目，用 `/cowriteai new "标题"` 开始第一个。」
