## ADDED Requirements

### Requirement: Add a task
The system SHALL allow the user to add a new todo item by entering non-empty text and submitting it via the Enter key or an "Add" button.

#### Scenario: Add a task with the button
- **WHEN** the user types "Buy milk" into the input and clicks "Add"
- **THEN** a new incomplete task "Buy milk" appears at the bottom of the list and the input is cleared

#### Scenario: Add a task with the Enter key
- **WHEN** the user types "Call mom" and presses Enter
- **THEN** a new incomplete task "Call mom" appears in the list

#### Scenario: Reject empty input
- **WHEN** the user submits with an empty or whitespace-only input
- **THEN** no task is added and the list is unchanged

### Requirement: Toggle task completion
The system SHALL allow the user to mark a task complete or incomplete by clicking its checkbox.

#### Scenario: Mark complete
- **WHEN** the user clicks the checkbox of an incomplete task
- **THEN** the task is marked complete and its text is shown with a strikethrough

#### Scenario: Mark incomplete again
- **WHEN** the user clicks the checkbox of a completed task
- **THEN** the task returns to incomplete and the strikethrough is removed

### Requirement: Delete a task
The system SHALL allow the user to permanently remove a task from the list.

#### Scenario: Delete a task
- **WHEN** the user clicks the delete control on a task
- **THEN** that task is removed from the list and does not reappear after reload

### Requirement: Persist tasks
The system SHALL store all tasks in the browser so they survive a page reload.

#### Scenario: Tasks survive reload
- **WHEN** the user has added tasks and reloads the page
- **THEN** the same tasks, with their completion states, are displayed

### Requirement: Show remaining count
The system SHALL display a live count of incomplete tasks.

#### Scenario: Count updates on change
- **WHEN** the user adds, completes, or deletes a task
- **THEN** the displayed "remaining" count updates to reflect the number of incomplete tasks
