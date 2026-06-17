# specdrive

**An unattended, spec-driven development skill for [Claude Code](https://www.claude.com/product/claude-code).**
A thin shell over [OpenSpec](https://github.com/Fission-AI/OpenSpec) that turns "build this seriously" into a disciplined, traceable, mostly-autonomous run.

*([中文说明见下方](#中文说明) / Chinese below.)*

---

## What it does

`specdrive` makes Claude **think before it codes**, then run **end-to-end on its own**:

```
PROPOSE              GATE                 APPLY                    ARCHIVE
think first    →  adversarial review  →  parallel implement  →   auto-archive
4 spec docs       (AI gatekeeper)        + verify-fix loop        (on full pass)
```

1. **PROPOSE** — produces 4 spec docs (`proposal` = what & why, `specs` = testable WHEN/THEN behaviors, `design` = how & trade-offs, `tasks` = checkable steps). No code yet.
2. **GATE** — instead of stopping for you, an **adversarial-review subagent** ("nitpick, default to reject") grades the docs against a hard rubric. Fail → auto-revise → re-review. This is the soul of spec-driven dev: the gate stays, only the gatekeeper changes from human to AI.
3. **APPLY** — dependency-aware **parallel subagents** (one file = one subagent, no same-file conflicts), then a **verify-fix loop** that checks against the spec and fixes failures until it passes.
4. **ARCHIVE** — on full pass, auto-archives and settles specs into long-term docs.

The heavy lifting (scaffolding, progress, validation, archiving) is delegated to the **OpenSpec CLI** — this skill only orchestrates it.

## Why it's safe to run unattended

- **Three hard brakes on every loop**: iteration cap, no-progress detection, budget cap. Hit one → stop, report, hand back.
- **External-judge verification**: it runs your tests/linters and reports the *script's* result — never "it feels right."
- **No faking**: if something can't be auto-verified (e.g. a hand-clicked HTML interaction), it labels it "not auto-verified, needs human" and surfaces — it never pretends, and never auto-archives the unverified.

## Two modes

- **Unattended (default)** — runs the whole pipeline without waiting on you.
- **Attended** — say *"watch me"* / *"confirm each step"* and it stops at each gate for your sign-off.

## Requirements

- [Claude Code](https://www.claude.com/product/claude-code)
- [OpenSpec](https://github.com/Fission-AI/OpenSpec) installed globally:
  ```bash
  npm install -g @fission-ai/openspec@latest
  ```
  (Node.js ≥ 20.19. On a slow network add `--registry <your-mirror>`.)

## Install

Copy the skill into your Claude Code skills directory:

```bash
# pick ONE language version as SKILL.md
mkdir -p ~/.claude/skills/specdrive
cp SKILL.md     ~/.claude/skills/specdrive/SKILL.md     # English
# or:
cp SKILL.zh.md  ~/.claude/skills/specdrive/SKILL.md     # 中文
```

Then in any project, just say: **"spec-drive a todo web app"** (or `规格驱动做一个待办网页`). It handles `openspec init` for you on first use.

> Note: specdrive drives the `openspec` CLI directly, so it works immediately in any session — it does **not** depend on the `/opsx:` slash commands that `openspec init` generates.

## Credit

The methodology and the engine come from **[OpenSpec](https://github.com/Fission-AI/OpenSpec)** (Fission-AI). `specdrive` adds three things on top: an **adversarial-review gate** (so it runs unattended), **dependency-aware parallel implementation**, and a **verify-fix loop with brakes**.

## License

[MIT](./LICENSE)

---

## 中文说明

**给 [Claude Code](https://www.claude.com/product/claude-code) 用的「默认无人值守」规格驱动开发 skill。**
它是 [OpenSpec](https://github.com/Fission-AI/OpenSpec) 之上的一层薄壳，把"认真做个东西"变成一次有纪律、留痕、基本全自动的开发。

### 它干什么

让 Claude **先想清楚再写代码**，然后**自己一镜到底跑完**：

```
PROPOSE          命门              APPLY                ARCHIVE
先想清楚    →  对抗审核把关   →   并发实现+验修闭环  →  达标自动归档
4份说明书       (AI 当把关人)
```

1. **PROPOSE**——产出 4 份说明书（proposal 做什么&为什么 / specs 可测试的 WHEN-THEN 行为 / design 怎么做&取舍 / tasks 可打勾步骤）。先不写代码。
2. **命门**——不停下等你，而是派一个**对抗审核分身**（"挑刺、默认不过"）按硬标准审；没过就自动改、再审。命门不拆，只是把关人从"你"换成"AI"。
3. **APPLY**——依赖感知的**并发分身**（一个文件一个分身，杜绝同文件冲突），再走**验-修闭环**对着规格验、没过就修，直到达标。
4. **ARCHIVE**——全部通过则自动归档，把规格沉淀进长期文档。

重活（建文件/追踪进度/校验/归档）外包给 **OpenSpec 命令行**，本 skill 只负责指挥。

### 为什么敢无人值守

- **每个循环都有三道硬刹车**：圈数封顶 / 原地踏步即停 / 预算到顶即停。撞了就停、出报告、交回你。
- **外部裁判验证**：跑你的测试/检查器，报告**脚本的结果**，不靠"感觉对"。
- **绝不蒙混**：没法自动验证的（如人工点的 HTML）会如实标"未自动验证、需人工确认"并浮出来，绝不假装通过、绝不把未验证的东西自动归档。

### 两档
- **无人值守（默认）**：整条流水线不等你跑完。
- **盯着做**：说「盯着做 / 每步确认」，它就在每道闸停下等你点头。

### 依赖 & 安装
依赖 [OpenSpec](https://github.com/Fission-AI/OpenSpec)：`npm install -g @fission-ai/openspec@latest`（Node ≥ 20.19）。
把 `SKILL.md`（英文）或 `SKILL.zh.md`（中文）**二选一**复制成 `~/.claude/skills/specdrive/SKILL.md` 即可。然后在任意项目说「规格驱动做一个待办网页」就会跑起来（首次会自动 `openspec init`）。

### 致谢
方法论与引擎来自 **[OpenSpec](https://github.com/Fission-AI/OpenSpec)**（Fission-AI）。`specdrive` 在其上加了三样：**对抗审核闸**（无人值守）、**依赖感知并发实现**、**带刹车的验-修闭环**。
