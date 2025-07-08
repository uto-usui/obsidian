# Project: Claude Code Hooks Documentation

**Goal:** To create a comprehensive document explaining the features and implementation examples of hooks in "Claude Code".

## Phase 1: Definition and Scope Clarification (Completed)

- [x] **Action:** Confirm the definition of "hooks" in the context of Claude Code with the user. (Official documentation provided)
- [x] **Action:** Search for any existing official or unofficial documentation on the subject. (Official documentation analyzed)

## Phase 2: Document Creation (In Progress)

- [ ] **Action:** Create a series of documents based on the Diataxis framework, focusing on one hook type per document.
- [x] **Current Task:** Write the document for the `PreToolUse` hook.
- [x] **Current Task:** Write the document for the `PostToolUse` hook.
- [x] **Current Task:** Write the document for the `Notification` hook.
- [x] **Current Task:** Write the document for the `Stop` hook.
- [x] **Current Task:** Write the document for the `SubagentStop` hook.
- [x] **Current Task:** Create a summary document.
    - **Category:** Explanation & How-to Guide
    - **Content:**
        - What is the `PreToolUse` hook?
        - How to configure it (`/hooks` command, `settings.json`).
        - Example 1: Blocking a specific command (e.g., `rm`).
        - Example 2: Requiring user confirmation before execution.
    - **Location:** `memo/claude/Claude Code Hooks - PreToolUse.md`

## Phase 3: Future Work

- [ ] **Action:** Write documents for the remaining hooks:
    - `PostToolUse`
    - `Notification`
    - `Stop`
    - `SubagentStop`
- [ ] **Action:** Create a summary document that links to all individual hook documents.
