# Examples

A real, end-to-end run of `specdrive`, so you can see what each phase actually produces.
*(一个跑通的真实例子，让你看清每个阶段到底产出什么。)*

## `add-todo-app/` — a tiny single-file todo web app

This is the output of running specdrive on *"build a simple todo web page."*

| File | Phase | What it is |
|------|-------|-----------|
| [`proposal.md`](./add-todo-app/proposal.md) | PROPOSE | **What & why** — the problem, the changes, the scope boundary |
| [`specs/todo-management/spec.md`](./add-todo-app/specs/todo-management/spec.md) | PROPOSE | **Must-satisfy behaviors** — testable `WHEN/THEN` scenarios (incl. the dark side: empty input, reload persistence) |
| [`design.md`](./add-todo-app/design.md) | PROPOSE | **How & why this way** — single-file vanilla JS, `localStorage`, trade-offs |
| [`tasks.md`](./add-todo-app/tasks.md) | PROPOSE → APPLY | **Checkable steps** — all `[x]` because APPLY completed them |
| [`index.html`](./add-todo-app/index.html) | APPLY | **The result** — the actual working app the tasks produced |

### How to read it

1. Start with `proposal.md` — the "why."
2. Read `specs/.../spec.md` — this is the **acceptance checklist** and the anti-drift anchor. Note how each requirement has at least one `WHEN/THEN` scenario, including failure/edge states (empty input, reload).
3. `design.md` records the technical choices *and the alternatives rejected* — so six months later you know why.
4. `tasks.md` is the breakdown APPLY worked through, checking each box.
5. `index.html` is what came out the other end.

> This example is single-file and tiny, so specdrive's APPLY phase would **triage it to serial** (parallel subagents aren't worth it for one file). It's here to show the **spec artifacts**, which look the same regardless of project size. Parallelism kicks in on multi-file changes.

— 中文导读：先看 `proposal.md`（为什么做）→ `spec.md`（验收清单 + 防跑偏锚，注意每条都有 WHEN/THEN 场景、含空输入/刷新这些"暗面"）→ `design.md`（技术取舍 + 否决了哪些替代方案）→ `tasks.md`（拆解步骤，全打勾）→ `index.html`（最终成品）。这个例子是单文件，APPLY 会判定"不值得并发"直接串行；放在这里是为了展示 4 份说明书长什么样——不管项目大小，它们形态一致。
