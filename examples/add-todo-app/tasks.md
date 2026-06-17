## 1. Page scaffold

- [x] 1.1 Create `index.html` with a basic HTML5 skeleton, page title, and an `<h1>` heading
- [x] 1.2 Add inline `<style>` with clean, mobile-friendly CSS (centered card, readable fonts)

## 2. Markup for the UI

- [x] 2.1 Add the input field and "Add" button
- [x] 2.2 Add an empty `<ul>` list container for tasks
- [x] 2.3 Add a "N remaining" count element

## 3. State and persistence

- [x] 3.1 Add inline `<script>` with a `todos` array and load it from `localStorage` on startup
- [x] 3.2 Implement a `save()` function that writes `todos` back to `localStorage`

## 4. Core behaviors

- [x] 4.1 Implement `render()` that rebuilds the list from `todos` (using `textContent`, not innerHTML) and updates the remaining count
- [x] 4.2 Implement add: read input, reject empty/whitespace, push `{id, text, done:false}`, save, re-render, clear input
- [x] 4.3 Wire up the "Add" button and the Enter key to the add function
- [x] 4.4 Implement toggle: clicking a checkbox flips `done`, applies strikethrough styling, saves, re-renders
- [x] 4.5 Implement delete: removing a task from `todos`, save, re-render

## 5. Verify against spec

- [x] 5.1 Manually verify every scenario in `specs/todo-management/spec.md` (add, toggle, delete, persist on reload, live count)
