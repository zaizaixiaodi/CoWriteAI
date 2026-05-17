# CoWriteAI PRD — 自媒体脚本协作 Agent（修订版）

> 一个跑在 Claude Code 里的多 Agent 系统，把"一个口喷的点子"变成"一份可拍摄的脚本"。
> 服务对象：聚焦 AI × 文科/法律/自媒体方向的口播 / Vlog 博主。

---

## 1. 产品定位

### 一句话

CoWriteAI 是一个本地运行的 Claude Code Agent（folder as an app），模拟一支"对谈编辑 + 选题策划 + 文风打磨 + 拍摄统筹 + 传播专家"的小型内容团队，陪博主从灵感聊到脚本和封面。

### 它不是什么

- 不是「输入主题 → 一键吐脚本」的傻瓜工具。一键产物是平庸的，传播力的来源是博主本人的观点和情绪密度。
- 不是 SaaS。没有前端、没有账号系统，跑在 Claude Code CLI，所有数据落本地文件。
- 不是通用写作助手。工作流、人设、产物结构都为「短视频脚本」专门设计。

### 设计原则

1. **激发 > 代笔**。AI 的价值是逼用户表达，而不是替用户表达。
2. **可中断、可恢复**。任何阶段关掉终端，下次进来都能续上。
3. **Markdown 优先，JSON 只用于状态**。内容产物人类直接可读；只有项目元数据用 JSON。
4. **博主是导演**。每个关键节点必须用户拍板，AI 不替用户做选题决策。

---

## 2. 用户与场景

### 用户画像

- 自媒体博主一名，主打 AI × 文科 / 法律 / 自媒体应用方向
- 形式：口播 + Vlog

### 典型一周

- 周一冒出一个想法，在脑子里酝酿
- 周三晚上想做这期，打开 Claude Code → `/cowriteai new "题目"` → 口喷聊一小时
- 周四白天断断续续打磨初稿
- 周五出脚本，周末拍摄

### 痛点对照

| 痛点 | CoWriteAI 的回应 |
|------|-----------------|
| 灵感来了不知道从哪个角度切 | 对谈编辑反复追问、抬杠，逼出最锐角度 |
| 选题模糊，写到一半发现不传播 | 选题策划给 1-3 个传播向选题 |
| 写出来的稿子像 AI，不像自己 | 文风 agent 用博主风格描述做最后一道打磨 |
| 文字稿 ≠ 拍摄脚本 | 脚本化 agent 拆短句、配 B-roll、加气口 |
| 标题封面随便起，点击率拉胯 | 传播专家专门设计标题、封面、开头钩子 |
| 跨天工作记不住到哪了 | 项目状态 JSON + 阶段产物 MD，随时续 |

---

## 3. 核心工作流

### 阶段总览

```
Stage 1  对谈激发（口喷 + 可选联网搜索，≥3 轮）
Stage 2  选题收敛（选题策划给 1-3 个方向，用户拍板）
Stage 3  视角确认（俯/平/仰/透/侧）
Stage 4  结构确认（RAWE 排序）
Stage 5  初稿撰写
Stage 6  文风打磨
Stage 7  用户改稿循环
Stage 8  脚本化（拆句 + B-roll + 气口）
Stage 9  传播包装（标题 + 封面 + 钩子，传播专家负责）
Stage 10 终稿输出
```

每个阶段产出一个或多个 markdown 文件，状态记在 `state.json`。

> **二期功能**：飞书多维表格接入（读灵感库）、IMA 知识库接入。一期输入只有口喷 + 联网搜索。

### 详细规格

#### Stage 1 · 对谈激发

**对谈编辑**（@agent-dialogue-editor）接管，全程主导：

1. 博主说说这期想聊什么（无需整理，随便说）
2. 编辑回应、抬杠、追问——每轮固定结构：
   - 回应上一轮的观点（认同/质疑/补充）
   - 抛出 1-3 个追问（引出具体经历、情绪、立场）
3. 联网搜索：若博主提到不确定的案例/数据，编辑主动搜索补充
4. 每轮保存到 `dialogue/round_NN.md`
5. 最少 3 轮，用户随时 `/next` 进入选题

#### Stage 2 · 选题收敛

**选题策划**（@agent-topic-planner）接管：

- 读完所有对谈记录
- 产出 1-3 个候选选题，每个含：标题、钩子、传播逻辑、天花板、风险
- 用户 `/approve topic:N` 锁定选题 → 写入 `topic.md`

#### Stage 3 · 视角确认

主控展示五种视角 + 示范开头，用户选定 → `/approve` → 写入 `angle.md`

| 视角 | 关系 | 示例 |
|------|------|------|
| 俯视 | 专家教育粉丝 | 「今天讲清楚 AI 合同审查的三层能力边界」 |
| 平视 | 嘴替/家人们谁懂啊 | 「家人们我真的栓Q了，AI 写的合同……」 |
| 仰视 | 成长型/挑战 | 「挑战 30 天，让 AI 帮我打赢一场合同纠纷」 |
| 透视 | 反共识 | 「你以为 AI 不能做法律？错了」 |
| 侧视 | 叛逆 | 「别人都在卷大模型，我偏要让它当律师助理」 |

#### Stage 4 · 结构确认（RAWE）

- **R** Result 结果（何同学式开头）
- **A** Action 做法（干货/过程）
- **W** Why 为什么（痛点共鸣）
- **E** Emotion 情绪（Vlog 常见）

主控根据视角推荐排序，用户确认 → `/approve` → 写入 `structure.md`

#### Stage 5 · 初稿撰写

主控综合 topic + angle + structure + 所有对谈记录，写完整初稿 → `draft_v1.md`

#### Stage 6 · 文风打磨

**文风**（@agent-stylist）接管，读取 `cowriteai/style_profile.md` + `draft_v1.md`：
- 改句式、替换 AI 腔、加口头禅
- 输出 `draft_v2.md`

#### Stage 7 · 用户改稿循环

用户自然语言提改稿意见 → 主控修改 → `draft_vN.md`，直到 `/approve`

#### Stage 8 · 脚本化

**拍摄统筹**（@agent-shoot-director）接管：
- 确认类型：口播 / Vlog
- 拆短句（8-15 字）、配 B-roll、插气口
- 输出 `script_v1.md`（三栏格式）

#### Stage 9 · 传播包装

**传播专家**（@agent-distribution-expert）接管，产出四件事：

1. **标题方案**（3 个备选，含平台适配建议和传播策略分析）
2. **封面设计**（2-3 个方案，含 AI 生图 prompt 中英双语）
3. **开头钩子**（前 3 秒，2-3 个版本）
4. **传播备注**（发布时间、话题标签、评论区引导语）

输出到 `distribution/` 目录下四个文件。

#### Stage 10 · 终稿输出

`/finalize` → 生成 `script_final.md` + `shotlist.md` + `distribution/final/`，归档项目。

---

## 4. Agent 架构

### 角色与职责

| Agent | 类型 | 人设 | 何时上线 |
|-------|------|------|---------|
| 主控 | 主 agent | 项目经理，统筹流程、调度 subagent | 全程 |
| 对谈编辑 | subagent | 资深特稿编辑，懂行、有观点、不怕抬杠 | Stage 1 |
| 选题策划 | subagent | 短视频选题老炮，对传播敏感 | Stage 2 |
| 文风 | subagent | 模仿者，吃博主风格 | Stage 6、7 |
| 拍摄统筹 | subagent | 短视频导演，懂镜头语言 | Stage 8 |
| 传播专家 | subagent | 平台运营老手，懂标题封面钩子 | Stage 9 |

### 上下文传递方式

每次 subagent 被调用时，从项目目录的 markdown 文件中读取上下文：
- `state.json` — 项目状态
- `dialogue/round_*.md` — 对谈记录
- `research/web_findings.md` — 联网搜索结果
- `topic.md` / `angle.md` / `structure.md` — 已确认的方向
- `draft_vN.md` / `script_vN.md` — 最新草稿

> 不使用 messages.jsonl 式的会话历史持久化，改为读文件获取上下文。

---

## 5. 数据与文件结构

```
.claude/                           # Claude Code 配置（官方规范）
├── settings.json                  # 权限预配置
├── skills/                        # 斜杠命令（/name 触发）
│   ├── cowriteai/SKILL.md         # 主入口
│   ├── next/SKILL.md              # 推进阶段
│   ├── approve/SKILL.md           # 批准产物
│   ├── finalize/SKILL.md          # 终稿输出
│   ├── git-commit/SKILL.md        # 自动提交（开发用）
│   ├── done/SKILL.md              # 里程碑记录（开发用）
│   └── karpathy-guidelines/SKILL.md  # 开发规范
└── agents/                        # 子代理（@agent-name 触发）
    ├── dialogue-editor/SKILL.md
    ├── topic-planner/SKILL.md
    ├── stylist/SKILL.md
    ├── shoot-director/SKILL.md
    └── distribution-expert/SKILL.md

CLAUDE.md                          # 项目指令（含 Karpathy Guidelines）
DEVLOG.md                          # 开发决策日志

cowriteai/
├── style_profile.md               # 博主风格描述（使用前必须填写）
└── projects/epXXX-slug/
    ├── state.json
    ├── research/web_findings.md
    ├── dialogue/round_NN.md
    ├── topic.md / angle.md / structure.md
    ├── draft_v1.md ... draft_vN.md
    ├── script_v1.md / script_final.md
    ├── shotlist.md
    └── distribution/
        ├── titles.md / covers.md / hooks.md / notes.md
        └── final/
```

### state.json 结构

```json
{
  "episode_id": "ep042",
  "slug": "ai-lawyer-contract",
  "title": "AI 帮律师写合同到底靠不靠谱",
  "created_at": "2026-05-05T14:30:00+08:00",
  "updated_at": "2026-05-06T09:12:00+08:00",
  "current_stage": 3,
  "stages_completed": [1, 2]
}
```

---

## 6. 斜杠命令（Skill）参考

> CLI 命令即 Skill，无单独 CLI 层。以下所有命令均为 `.claude/skills/` 下的 Skill。

### 创作命令

| 命令 | 作用 |
|------|------|
| `/cowriteai new "标题"` | 新建项目，立即进入 Stage 1 |
| `/cowriteai resume [epXXX]` | 恢复项目（不传 ID 默认最近） |
| `/cowriteai list` | 列出所有项目及当前阶段 |
| `/cowriteai status` | 当前项目状态摘要 |
| `/next` | 当前阶段完成，进下一阶段 |
| `/approve [topic:N\|draft\|script]` | 批准当前产物 |
| `/finalize` | 输出终稿，归档 |

### 开发命令（仅开发过程使用）

| 命令 | 作用 |
|------|------|
| `/git-commit` | 自动生成 commit message 并推送 |
| `/done` | 记录 DEVLOG + 自动提交推送 |
| `/karpathy-guidelines` | 按规范审查当前代码/prompt |

### 子代理直接调用

| @-mention | 作用 |
|-----------|------|
| `@agent-dialogue-editor` | 直接进入对谈模式 |
| `@agent-topic-planner` | 直接生成选题方案 |
| `@agent-stylist` | 直接打磨当前最新草稿 |
| `@agent-shoot-director` | 直接脚本化 |
| `@agent-distribution-expert` | 直接生成传播材料 |

---

## 7. 接入与集成

### P1（下一版本）
- 飞书 MCP 接入：通过飞书多维表格 API 同步灵感库
- 对标帖子 web_fetch：用户提供 URL，爬取内容作参考

### P2（二期）
- ima 知识库接入
- OpenAI DALL-E 封面生成
- 多项目并行管理仪表板
- 终稿一键导出到剪映 / Notion

---

## 8. MVP 范围

**P0（必须有，能跑通一个完整选题）**

- Stage 1-10 全流程
- 主控 + 五个 subagent
- 本地文件系统的状态管理与中断恢复
- 命令：`/cowriteai new|resume|list|status`、`/next`、`/approve`、`/finalize`
- 联网搜索（Stage 1）

**P1（提升质量）**
- 飞书 MCP 接入
- 对标帖子 web_fetch
- 传播专家接 OpenAI DALL-E API 生成封面预览

---

## 9. 开发流程规范

### 9.1 GitHub 仓库

https://github.com/zaizaixiaodi/CoWriteAI.git

主分支即生产分支，按里程碑打 tag。

### 9.2 DEVLOG

项目根目录维护 `DEVLOG.md`，记录每次开发的进展和关键决策。
格式：日期 + 里程碑名 + 完成内容 + 关键决策 + 下一步。

### 9.3 开发命令

- `/git-commit` — 自动分析变更生成 commit message，一键提交推送，不询问用户
- `/done` — 自动生成 DEVLOG 条目 + 调用 `/git-commit`
- `/karpathy-guidelines` — 审查代码/prompt 是否符合开发规范

### 9.4 核心开发规范

详见 `CLAUDE.md` 中的 Karpathy Guidelines，以及 `.claude/skills/karpathy-guidelines/SKILL.md`。

简要版：
1. 编码前先想清楚，明确陈述假设
2. 最少代码解决问题，不加猜测性功能
3. 只碰必须碰的，不顺手改周边
4. 把任务转化为可验证目标再动手

### 9.5 权限预配置

`.claude/settings.json` 已预授权所有 git、python、文件操作命令，开发过程无需逐条确认。

### 9.6 Windows 兼容

- 脚本用 Python（3.11+）
- 路径用 `pathlib.Path`
- 不依赖 bash-only 语法

---

## 10. 风险与未决

| 项 | 说明 | 当前结论 |
|----|------|---------|
| Subagent 上下文长度 | 对谈轮数多了以后 dialogue 文件会很长 | 设轮数上限（如 10 轮），超出提示用 `/next` |
| 对谈编辑可能"为了反对而反对" | prompt 设计要平衡 | system prompt 里写死：抬杠目标是补角度，不是赢 |
| 风格 profile 怎么来 | 手动写 | 后续可加：从历史稿件自动提炼风格 |
| Windows 路径与 shell 兼容 | 你的环境是 Windows | 脚本用 Python，避开 bash-only 语法 |
