---
allowed-tools: Bash, Read, Write, Edit, Glob, Grep, Task, AskUserQuestion
---

# Code Review

Review code changes after implementing a task using the code-analyzer agent.

## Usage
```
/code-review <feature_name> <task_number>
```

- `feature_name`: The PRD/feature name
- `task_number`: The task number (e.g., 001, 002)

## Preflight Checklist

Before proceeding, complete these validation steps.
Do not bother the user with preflight checks progress. Just do them and move on.

### 1. Verify Task Exists
- Check if `.claude/tasks/<feature_name>/<task_number>.md` exists
- If not found: "Task not found. Please check the feature name and task number."

### 2. Check Task Status
- Read task frontmatter
- Verify status is "in-progress"
- If status is "open": "Task hasn't been started yet. Run /task-start first."
- If status is "closed": "Task is already closed."

### 3. Check for Changes
- Run: `git status --short`
- Run: `git diff --stat`
- If no changes: "No changes to review. Make some changes first."

### 4. Verify Branch
- Run: `git branch --show-current`
- Expected branch: `task/<feature_name>-<task_number>`
- If on different branch, warn user

## Instructions

### Phase 1: Gather Context

1. **Read the task file** to understand what was supposed to be implemented
2. **Get the list of changed files**:
   ```bash
   git diff --name-only HEAD
   git diff --cached --name-only
   ```
3. **Get the diff**:
   ```bash
   git diff HEAD
   ```

### Phase 2: Launch Code Analyzer

Use the Task tool to launch the code-analyzer agent:

```yaml
Task:
  description: "Analyze code changes for task"
  subagent_type: "general-purpose"
  prompt: |
    You are the code-analyzer agent. Read .claude/agents/code-analyzer.md for your instructions.

    Review the code changes for Task <task_number> of feature <feature_name>.

    Task requirements (from task file):
    <task>
    {contents of task file}
    </task>

    Changed files:
    {list of changed files}

    Diff:
    {git diff output}

    Analyze these changes and provide your review in the specified format.
    Focus on:
    1. Does the implementation match the task requirements?
    2. Are there any bugs or potential issues?
    3. Are there security vulnerabilities?
    4. Is error handling complete?
    5. Are there any edge cases not handled?

    Be thorough but concise.
```

### Phase 3: Present Review Results

Present the code analyzer's review to the user:

```markdown
## Code Review for Task <task_number>: <task_name>

{Code analyzer's review output}

---

### Files Changed
{list of files with brief change description}

### Summary
- Critical Issues: {count}
- Warnings: {count}
- Files Reviewed: {count}
```

### Phase 4: Handle Critical Issues

If critical issues were found:

Use `AskUserQuestion` to ask:
```
Critical issues were found in the code review.

Options:
1. Fix critical issues now
2. Review issues manually first
3. Skip fixes and proceed anyway (not recommended)
```

**If user chooses to fix:**
1. For each critical issue:
   - Show the issue and suggested fix
   - Apply the fix
   - Commit the fix: `git add <files> && git commit -m "fix: <issue description>"`
2. Re-run the code analyzer to verify fixes

**If user chooses to review manually:**
- List all critical issues with file locations
- Let user make their own fixes
- Suggest running `/code-review` again after fixes

### Phase 5: Review Complete

When review passes (no critical issues or issues fixed):

```
Code Review Complete

Task: <task_number> - <task_name>
Branch: task/<feature_name>-<task_number>

Review Status: PASSED / PASSED WITH WARNINGS

Changes:
- {count} files changed
- {count} insertions, {count} deletions

Commits on this branch:
{list of commits}

Next Steps:
- Push and create PR: /task-close <feature_name> <task_number>
```

## Error Handling

- **No changes found:** "Nothing to review. The working directory is clean."
- **Task file missing:** "Cannot find task file. Please verify the task exists."
- **Wrong branch:** Warn user but continue if they confirm

## Important Notes

- **Review before closing** - Always review code before closing a task
- **Fix critical issues** - Don't skip critical issues
- **Re-review after fixes** - Run review again if significant fixes were made
- **Keep commits clean** - Fix commits should be separate from feature commits
