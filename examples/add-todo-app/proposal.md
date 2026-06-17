## Why

People need a dead-simple way to jot down tasks and check them off without signing up, installing anything, or trusting a server with their data. A single self-contained web page that remembers tasks in the browser solves this with zero friction.

## What Changes

- Add a single-page todo web app (one `index.html` with inline CSS and JavaScript, no build step).
- Users can add a task by typing text and pressing Enter or clicking an "Add" button.
- Users can mark a task complete/incomplete by clicking its checkbox; completed tasks show a strikethrough.
- Users can delete a task.
- Tasks persist across page reloads using the browser's local storage.
- Show a live count of remaining (incomplete) tasks.

## Capabilities

### New Capabilities
- `todo-management`: Adding, completing, deleting, and persisting todo items in a single browser page.

### Modified Capabilities
<!-- None — this is a greenfield change. -->

## Impact

- New file: `index.html` at the project root (self-contained: HTML + CSS + JS).
- No backend, no dependencies, no build tooling. Runs by opening the file in any modern browser.
- Data is stored only in the user's browser (localStorage); nothing leaves the device.
