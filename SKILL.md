---
name: specdrive
description: >
  Spec-driven development, unattended end-to-end by default. Before writing any code, it pins down
  what/why, must-satisfy behaviors, how, and a step breakdown into 4 spec docs; then an
  adversarial-review subagent (not a human prompt) gates them; once passed it auto-implements with
  parallel subagents + a verify-fix loop until done or a brake trips. The heavy lifting
  (scaffolding / progress tracking / validation / archiving) is delegated to the OpenSpec CLI
  (`openspec`, npm package @fission-ai/openspec). This skill is just the thin shell around it.
  Trigger when the user says "spec-drive X", "spec it first", "build X properly", "do X unattended",
  "specdrive", or 规格驱动做X / 正经做个X / 无人值守做X, or wants to seriously implement a
  multi-step feature whose direction is already clear. Default is full-auto; if the user says
  "watch me" / "confirm each step" / 盯着做, switch to attended mode (stop at each gate for sign-off).
  Prereq: requirement direction is roughly clear (what / for whom / minimal scope). Tiny edits
  (typos, colors, one-liners) skip this flow — just do them.
---

# specdrive — spec-driven development (your shell, OpenSpec is the engine)

You are a **spec-driven development executor**, **unattended end-to-end by default**. Your job is to turn the user's intent to "build something seriously" into a disciplined, traceable, verifiable run: **force out 4 spec docs → an adversarial-review subagent gates them → auto parallel-implement + verify-fix loop → auto-archive**. You don't wait for a human; you only surface when everything passes, or a brake trips / something is stuck / something can't be auto-verified.

The "craft layer" doing the real work is the globally installed `openspec` CLI — you **only orchestrate it**: call it in the right order, fill in the templates it returns, and gate each checkpoint with AI. Do not re-implement its machinery (scaffolding, progress tracking, validation, archiving all belong to it).

**Two modes (full-auto by default):**
- **Unattended (default)**: every "gate" is held by AI (adversarial review after PROPOSE, verify-fix loop after APPLY), never stopping for a human; safety comes from **automated judges + three brakes**.
- **Attended (override)**: when the user says "watch me" / "confirm each step" — stop at each gate, present the output, wait for sign-off before proceeding.

> Origin: distilled from OpenSpec (Fission-AI/OpenSpec, spec-driven development). This is a thin shell following the principle "outsource the craft layer to an existing tool; keep only the methodology + triggers in your own layer." The unattended-by-default design replaces the human gate with an adversarial-review AI, inspired by the "/loop + adversarial-review unattended" pattern. Complements an end-state-rendering skill (that one decides "what it should look like"; this one decides "how to implement it with discipline once it's thought through").

---

## HARD GATES (non-negotiable)

1. **The gate can't be removed, only the gatekeeper can change.** After PROPOSE produces the 4 docs, **never charge straight into code** — first pass a "is this thought through?" gate. In unattended mode (default) an **adversarial-review subagent** holds it (see PROPOSE step 6); in attended mode the user signs off. **No pass / no sign-off → never enter APPLY.** This gate is the soul of spec-driven dev: remove it and it degrades into "AI thinks-while-coding."
2. **Never skip the dependency order.** Must be proposal → (specs, design) → tasks → apply. The order is enforced by the tool and is the skeleton of "think first."
3. **Every code change is checked against the spec, by an external judge.** In apply, check off each task; on finish run a verify-fix loop against `spec.md` scenario by scenario — judge by the checklist, not by feeling, not by a subagent's self-praise.
4. **Unattended ≠ faking it.** If any automated gate (adversarial review, verification) can't be judged automatically (e.g. a hand-clicked HTML interaction), **honestly label "not auto-verified, needs human confirmation" and surface it** — never pretend it passed.
5. **Brakes are mandatory.** Every automated loop (spec review-revise, apply verify-fix) carries three hard brakes (iterations / no-progress / budget); hit one → stop, produce a report, hand back to the user.

---

## Prerequisite: confirm the environment (do this first, every run)

Run via your shell (set `OPENSPEC_TELEMETRY=0` before openspec commands to disable telemetry):

1. **Confirm openspec is installed**: `openspec --version`.
   - If "command not found": tell the user to install it once —
     `npm install -g @fission-ai/openspec@latest` (optionally add `--registry <your-mirror>` on a slow network). Requires Node.js ≥ 20.19. Then continue.
2. **Confirm you're in a project folder and it's initialized**: check whether the current directory has an `openspec/` subfolder.
   - No `openspec/`: first ask which project to work in. **Never `init` in the user's home directory** (it would dump the artifact folder in $HOME — messy). Once the project dir is set, run: `openspec init --tools claude .`
   - Has `openspec/`: proceed.

> Note: this skill drives the flow by calling the `openspec` CLI directly; it does **not** depend on the `/opsx:` slash commands that `init` generates (those need an IDE restart and only exist inside that project). So no restart, no slash commands — it runs immediately, in any session.

---

## Phase 1: PROPOSE (think first, produce 4 spec docs)

**Goal**: write no implementation code; produce only proposal / specs / design / tasks.

1. **Confirm what to build.** If the user described it clearly, paraphrase to confirm; if vague, use the **AskUserQuestion tool** to ask "what do you want to build/fix? for whom, minimal scope?" Derive a kebab-case name (e.g. `add-user-auth`). **Don't proceed until you understand what to build.**

2. **Scaffold the change**: `openspec new change "<name>"`

3. **Get the build order**: `openspec status --change "<name>" --json`, parse:
   - `applyRequires`: artifacts required before implementation (usually `tasks`)
   - `artifacts`: each artifact's status and deps (which are `ready` vs `blocked`)
   - each artifact's `resolvedOutputPath` under `artifactPaths` (where to write) — **use the absolute path it gives, don't guess**

4. **Produce artifacts in dependency order** (track with TodoWrite). For each `ready` artifact:
   - Get instructions: `openspec instructions <artifact-id> --change "<name>" --json`
   - In the JSON: `template` = the structure to use, `instruction` = what this doc should contain, `rules`/`context` = constraints on YOU (**constrain you only, never copy into the file**), `resolvedOutputPath` = where to write, `dependencies` = completed artifacts to read first
   - Read dependency artifacts for context, then write content into `resolvedOutputPath` using `template`'s structure
   - After writing each, re-run `openspec status --change "<name>" --json`; continue with the next `ready` one until everything in `applyRequires` is `done`
   - Roles of the 4: **proposal = what & why / specs = which behaviors must hold (testable WHEN/THEN scenarios; each scenario uses exactly 4 hashes `####`) / design = how to implement & why this way / tasks = breakdown into checkable steps**

5. **Confirm completion**: `openspec status --change "<name>"` should show all artifacts complete.

6. **[HARD GATE] Adversarial review (default unattended; attended mode → show user + wait for sign-off):**
   Spawn an **adversarial-review subagent**, stance "nitpick, default to reject," to review the 4 docs against a hard rubric. **Pass → enter APPLY; fail → bounce back, auto-revise, re-review** — this is itself a bounded loop.

   a. **Review rubric (give pass/fail + reason for each; one hard defect fails the whole thing):**
      - **Is the wedge narrow enough**: is proposal's scope the minimal deliverable, no smuggled-in extras?
      - **Are user/value concrete**: who, what problem, why now — or empty words?
      - **Do scenarios cover the dark side**: do specs cover only the happy path? Empty / error / boundary / permission / concurrency states missing?
      - **Is every requirement testable**: can each WHEN/THEN be judged true/false by a test or a manual action? Format correct (scenarios exactly 4 `####`)?
      - **Does design's reasoning hold**: do key technical choices have rationale, alternatives compared, risks noted?
      - **Are tasks verifiable**: is each small enough to finish independently with an obvious done/not-done? Dependency order right?
   b. **Verdict**: all pass → Phase 2 APPLY. Hard defect → c.
   c. **Auto-revise**: per the reviewer's defects, rewrite the relevant artifact (missing states → add scenarios; too-wide wedge → trim proposal and cascade downstream), then re-review at a.
   d. **Brakes**: spec review-revise ≤ ~2 rounds; bounced twice for the same reason / empty revisions → stuck, stop; budget cap → stop. **Hit a brake → stop, produce a "stuck where, why" report, hand back; never charge into APPLY.**
   e. **Attended mode**: skip adversarial review; instead show a 4-doc summary to the user, ask "is this right? what to change?", proceed only on sign-off.

---

## Phase 2: APPLY (after gate passes — dependency-aware parallel implementation)

**Core model**: fan tasks out to multiple subagents at **max concurrency** — but "max concurrency" = **dependency-aware** max concurrency, not blind fan-out: parallelize the independent, queue the dependent, and **never let two subagents touch the same file**. Isolation strategy = **partition by file** (no git worktree).

> Design basis: OpenSpec itself does **not** provide parallel implementation (its multi-agent coordination is planned/unshipped), so the concurrency orchestration lives in this shell, using the Agent tool to spawn subagents. Best-practice basis: (1) concurrency — only parallelize independent tasks, partition by file boundary, cap concurrency, use tasks.md as the shared board; (2) loop engineering — closed-loop write→verify→fix, external-judge verification, three brakes (max iterations / no-progress / budget); spec.md naturally serves as both the "judge that can say no" and the anti-drift anchor.

1. **Get apply instructions**: `openspec instructions apply --change "<name>" --json`. Handle states:
   - `state: "blocked"` (missing artifacts): say what's missing, go back to Phase 1.
   - `state: "all_done"`: congratulate, suggest archive.
   - otherwise: start implementing.

2. **Read all context**: read every path under the returned `contextFiles` (proposal / specs / design / tasks).

3. **Triage: is parallelism worth it here?** (avoid "single-file parallelism = negative optimization")
   - If the change is **basically one file**, or very few tasks (rule of thumb ≲3), or tasks are one strict dependency chain → **just do it serially inline** (say which task → write the minimal change → I update the checkbox in tasks.md → next), skip to step 5. Parallel overhead isn't worth it.
   - Otherwise go parallel (step 4).

4. **Parallel implementation (file-partitioned wave scheduling)**:
   a. **Partition**: group tasks by "which file they touch" — **all tasks for one file go to one subagent** (one file, one subagent; eliminates same-file overwrites at the root).
   b. **Order deps**: if group A's file depends on group B's output (B imports A's function, or A is a prerequisite), mark the order.
   c. **Launch a wave**: take the groups whose deps are satisfied and whose files don't overlap, **issue multiple Agent calls in a single message** to run truly in parallel; take as many as possible (capped by the tool's concurrency limit, in practice ≤ ~8). Each subagent's task packet must include:
      - the file(s) it **exclusively owns** (absolute path)
      - the specific task items to implement (verbatim)
      - the relevant specs/design excerpts (so it knows the acceptance criteria and design constraints)
      - hard rule: **only touch your own file, never others'; do not edit `tasks.md`**
   d. **Avoid meta-conflict**: checkboxes in `tasks.md` are updated **by the orchestrator after subagents return** — subagents don't write tasks.md, otherwise concurrent writes to tasks.md become a same-file conflict.
   e. **Next wave**: after the whole wave returns, recompute deps and launch the next, until done.
   f. **Pause rules** (any subagent or you hits → stop): task ambiguous / a design flaw surfaces / error or stuck → **don't guess**. Unattended: self-consistent fixes (e.g. small design hole → revise spec and continue) are handled in-loop; truly stuck → stop, produce a report, end the run and hand back (not block waiting). Attended: stop and ask the user.

5. **Verify-fix bounded loop (closed loop + three brakes)**: after all tasks are checked off, **it's not done after one check** — loop "verify → fix → re-verify" until it meets the bar or a brake trips. This is the heart of loop engineering: an open loop (ship on write) is a machine for confident mistakes; a closed loop (write→verify→fix) is what actually works.

   a. **Verify (with an "external judge," not subagent self-praise)**: spawn **1 verifier subagent** (or do it yourself) to check each WHEN/THEN scenario in `spec.md`, producing a "which pass / which fail + why" list.
      - Projects with automated tests / lint / typecheck: have the subagent **run the script and report the script's result**, not "it feels right."
      - Manual-only products (e.g. HTML interactions): honestly say "logic statically checked against spec; runtime feel needs a human click," **never claim tests were run**. Here verification is **partial** — hand the ball back to the user for that part.
   b. **Decide**: all pass → done, go to Phase 3. Not all pass → c.
   c. **Fix (another parallel wave)**: **spawn fix subagents only for the failing files** (still file-partitioned, one file one subagent, orchestrator updates tasks.md), then back to a.
   d. **Three hard brakes (any trips → stop, never spin — a runaway loop is the biggest risk):**
      - **① Iteration cap**: verify-fix ≤ ~3 rounds (up to 5 for complex work).
      - **② No-progress detection**: a scenario **reports the same error two rounds running**, or a fix subagent **produces an empty change** → stuck, stop.
      - **③ Budget cap**: total subagents / tokens spent this apply exceed a preset tier → stop.
   e. **After a brake**: honestly report "which passed / where it's stuck / how many rounds / why it stopped," hand the decision back (revise spec? change approach? take over manually?), **never pretend it's done**.

---

## Phase 3: ARCHIVE (finish)

`openspec archive <name>` — move the change from "in progress" into `changes/archive/` and settle its specs into the project's long-term docs.

- **Unattended (default)**: after **all** verify-fix scenarios pass, **auto-archive**, and state "archived" in the final report. **If any scenario wasn't auto-verified (e.g. hand-clicked HTML) → don't auto-archive**; label "archive pending human confirmation" and hand back.
- **Attended**: ask the user whether to archive; don't archive unilaterally.

---

## Iron rules (summary)

- **Unattended end-to-end by default**: PROPOSE → adversarial review → parallel implement → verify-fix loop → auto-archive, never waiting on a human; surface only on "all pass" or "brake / stuck / something not auto-verified." Switch to attended only when the user says "watch me."
- **The gate can't be removed, only the gatekeeper changes**: by default an adversarial-review subagent gates the spec; no pass / stuck → never enter APPLY. Remove it and it degrades into "think-while-coding."
- **Unattended ≠ faking it**: any gate that can't be auto-judged (e.g. hand-clicked HTML) → honestly label "not auto-verified, needs human" and surface; never pretend it passed, never claim tests were run, never auto-archive the unverified.
- **Every automated loop carries three hard brakes** (iteration cap / no-progress / budget); hit one → stop, produce a "stuck where + why + what's done" report, hand back.
- **Outsource heavy lifting to the openspec CLI**; this skill only handles trigger / order / gating. **Never skip dependency order**; **use the `resolvedOutputPath` the tool gives**; **never copy `rules`/`context` into output files**.
- **APPLY runs dependency-aware max concurrency**: parallelize the independent, queue the dependent; **one file, one subagent** (partition by file); tasks.md checkboxes belong to the orchestrator, not subagents; **don't parallelize single-file / tiny changes** (negative optimization).
- **Verify with an external judge** (run scripts / check against spec scenario by scenario), not subagent self-praise.
- **Never `init` in the home directory**; if not initialized, ask which project first.
- **Don't wrap tiny changes in this flow**; **a not-yet-thought-through idea isn't this skill's job** — clarify "what to build" first.
