---
allowed-tools: Bash, Read, Write, Edit, Glob, AskUserQuestion
---

# Task Close

Close a completed task: update status, commit, push, and create PR.

## Usage
```
/task-close <feature_name> <task_number> [completion_notes]
```

- `feature_name`: The PRD/feature name
- `task_number`: The task number (e.g., 001, 002)
- `completion_notes`: Optional notes about what was completed

## Required Rules

**IMPORTANT:** Before executing this command, read and follow:
- `.claude/rules/datetime.md` - For getting real current date/time
- `.claude/rules/frontmatter-operations.md` - For frontmatter updates

## Preflight Checklist

Before proceeding, complete these validation steps.
Do not bother the user with preflight checks progress. Just do them and move on.

### 1. Verify Task File Exists
- Check if `.claude/tasks/<feature_name>/<task_number>.md` exists
- If not found: "Task not found. Please check the feature name and task number."

### 2. Check Task Status
- Read task frontmatter
- Verify status is "in-progress"
- If status is "open": "Task hasn't been started yet. Run /task-start first."
- If status is "closed": "Task is already closed."

### 3. Verify on Correct Branch
- Run: `git branch --show-current`
- Expected branch: `task/<feature_name>-<task_number>`
- If on different branch, warn: "Not on expected branch. Current: {branch}, Expected: task/<feature>-<task_number>. Continue anyway?"

### 4. Check for Code Review
- Ask user: "Has this task been code reviewed?"
- If not reviewed, suggest: "Consider using the code-review plugin first (see README for installation)"
- Allow user to continue if they choose

## Instructions

### Phase 1: Update Task Status

Get current datetime: `date -u +"%Y-%m-%dT%H:%M:%SZ"`

Update the task file frontmatter:
- Change `status: in-progress` to `status: closed`
- Update `updated:` field with current datetime
- Add `completed:` field with current datetime

Updated frontmatter example:
```yaml
---
name: Task Title
status: closed
created: 2024-01-10T09:00:00Z
updated: 2024-01-15T16:30:00Z
started: 2024-01-15T14:30:00Z
completed: 2024-01-15T16:30:00Z
branch: task/feature-name-001
prd: .claude/prds/feature-name.md
depends_on: []
---
```

### Phase 2: Final Commit

Check for any uncommitted changes:
```bash
git status --short
```

If there are uncommitted changes:
```bash
git add .
git commit -m "task/<feature>-<number>: Close task - <completion_notes or 'Implementation complete'>"
```

### Phase 3: Push to Remote

Push the branch to remote:
```bash
git push -u origin task/<feature_name>-<task_number>
```

### Phase 4: Create Pull Request

Create a PR using GitHub CLI:

```bash
gh pr create \
  --title "Task <task_number>: <task_name>" \
  --body "$(cat <<'EOF'
## Summary
<Brief description of what was implemented>

## Task Reference
- Task: `.claude/tasks/<feature_name>/<task_number>.md`
- PRD: `.claude/prds/<feature_name>.md`
- Branch: `task/<feature_name>-<task_number>`

## Changes
<List of key changes made>

## Testing
<How this was tested>

## Checklist
- [ ] Code reviewed
- [ ] Tests pass
- [ ] Documentation updated (if needed)

---
🤖 Generated with Claude Code
EOF
)"
```

### Phase 5: Update PRD Progress (Optional)

If this completes a task from the PRD's task breakdown:
- Read the PRD file
- Note which task was completed
- (User can manually update PRD if needed)

### Phase 6: Cleanup Planning Files

If `task_plan.md` exists in working directory:
- Ensure it shows COMPLETED status
- Optionally move to `.claude/tasks/<feature_name>/` for archival

### Phase 7: Output Summary

```
Task Closed Successfully!

Task: <task_number> - <task_name>
Status: closed
Branch: task/<feature_name>-<task_number>

Git:
  - All changes committed
  - Pushed to origin

Pull Request:
  - PR created: <PR_URL>
  - Ready for review

Next Steps:
  - Review and merge the PR
  - Create next task: /task-create <feature_name> <description>
  - Or start another task: /task-start <feature_name> <next_task_number>

Context:
  - Run /context-update to refresh project context
```

## Error Handling

- **Push fails:** Check if remote exists, authentication is valid
- **PR creation fails:** Check if gh CLI is authenticated (`gh auth status`)
- **Branch not found:** Verify you're on the correct branch

If any step fails:
- Report what succeeded vs failed
- Provide specific recovery steps
- Never leave task in inconsistent state

## Important Notes

- **Always code review first** - Use the code-review plugin before closing
- **Commit everything** - Don't leave uncommitted changes
- **Create PR** - PRs enable proper review workflow
- **Update context** - Run /context-update after closing tasks
