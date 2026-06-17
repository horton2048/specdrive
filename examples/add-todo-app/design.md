## Context

A zero-friction personal todo tool. No accounts, no server, no network. The whole app is one HTML file the user opens in a browser. This keeps the user's data on their own device and makes the app trivial to share or host anywhere (or run by double-clicking the file).

## Goals / Non-Goals

**Goals:**
- One self-contained `index.html` (HTML + CSS + JS inline), no build step, no dependencies.
- Add / complete / delete tasks with instant UI feedback.
- Persist tasks locally so they survive reloads.
- Readable, commented code suitable for a beginner to inspect and learn from.

**Non-Goals:**
- No multi-device sync, sharing, or backend.
- No user accounts or authentication.
- No due dates, tags, reordering, or sub-tasks (could be future changes).

## Decisions

- **Single-file vanilla HTML/CSS/JS** over a framework (React/Vue): zero tooling, opens directly in a browser, and is easy for a non-coder to read top to bottom. Trade-off: no component reuse, but the app is too small to need it.
- **`localStorage` for persistence** over IndexedDB or a server: simplest API for a small array of tasks, synchronous, supported everywhere. Trade-off: ~5MB limit and single-device only — acceptable for a personal todo list.
- **Tasks stored as a JSON array** of `{ id, text, done }` under a single key (`todos`). Each `id` derived from an incrementing counter / timestamp so deletes and toggles can target a specific item.
- **Re-render from state** on every change (read array → rebuild list DOM) rather than surgically patching nodes: simpler to reason about and fast enough for a personal list.

## Risks / Trade-offs

- [Data lives only in one browser] → Acceptable by design; document it in the UI so users aren't surprised. Clearing browser data wipes the list.
- [Full re-render on every change could lag with thousands of items] → Out of scope; a personal list is small. Revisit only if needed.
- [No input sanitization could allow HTML injection into the list] → Mitigation: insert task text via `textContent`, never `innerHTML`, so typed text can never run as markup.
