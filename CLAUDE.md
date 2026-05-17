# CoWriteAI — 自媒体脚本协作 Agent

本地运行的 Claude Code Agent（folder as an app）。输入：博主口喷 + 可选联网搜索。输出：完整脚本 + 传播包装。

## 文件结构

```
.claude/
├── settings.json               # 权限预配置
├── skills/                     # 斜杠命令（/name 触发）
│   ├── cowriteai/              # /cowriteai new|resume|list|status
│   ├── next/                   # /next — 推进阶段
│   ├── approve/                # /approve — 批准当前产物
│   ├── finalize/               # /finalize — 输出终稿并归档
│   ├── git-commit/             # /git-commit — 自动提交推送（开发用）
│   ├── done/                   # /done — 开发里程碑记录（开发用）
│   └── karpathy-guidelines/    # /karpathy-guidelines — 代码审查
└── agents/                     # 子代理（@agent-name 触发）
    ├── dialogue-editor/        # Stage 1: 对谈激发
    ├── topic-planner/          # Stage 2: 选题收敛
    ├── stylist/                # Stage 6: 文风打磨
    ├── shoot-director/         # Stage 8: 脚本化
    └── distribution-expert/    # Stage 9: 传播包装

cowriteai/
├── style_profile.md            # ⚠️ 博主风格描述，使用前必须填写
└── projects/epXXX-slug/        # 每期节目目录
    ├── state.json
    ├── research/web_findings.md
    ├── dialogue/round_NN.md
    ├── topic.md / angle.md / structure.md
    ├── draft_v1.md ... draft_vN.md
    ├── script_v1.md / script_final.md
    ├── shotlist.md
    └── distribution/titles.md / covers.md / hooks.md / notes.md
```

## 工作流（10 个阶段）

| 阶段 | 名称 | 负责 |
|------|------|------|
| 1 | 对谈激发 | @agent-dialogue-editor |
| 2 | 选题收敛 | @agent-topic-planner |
| 3 | 视角确认 | 主控直接处理 |
| 4 | 结构确认（RAWE）| 主控直接处理 |
| 5 | 初稿撰写 | 主控综合材料写稿 |
| 6 | 文风打磨 | @agent-stylist |
| 7 | 用户改稿循环 | 自然语言迭代 |
| 8 | 脚本化 | @agent-shoot-director |
| 9 | 传播包装 | @agent-distribution-expert |
| 10 | 终稿输出 | /finalize |

## GitHub 仓库

https://github.com/zaizaixiaodi/CoWriteAI.git

---

## 核心开发原则（Karpathy Guidelines）

开发本项目的所有代码、prompt、skill 文件，必须遵守以下原则。完整版：`/karpathy-guidelines`

**1. 编码前先想清楚**
明确陈述假设。不确定则提问，不默默猜测。有更简单方案时说出来并推回。

**2. 简洁第一**
最少代码解决问题，不加猜测性功能。不为单次使用写抽象。200 行能写成 50 行就重写。

**3. 外科手术式修改**
只碰必须碰的，不"顺手"改周边代码。保持现有风格。

**4. 目标驱动执行**
把任务转化为可验证目标再动手。多步骤任务先列计划，逐步验证。

---

## Windows 兼容要求

- 所有脚本用 Python（3.11+），不用 bash-only 命令
- 路径用 `pathlib.Path`，不硬写 `/` 或 `\`
- 时区统一 +08:00，时间戳用 ISO 8601
