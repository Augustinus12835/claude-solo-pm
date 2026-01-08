---
allowed-tools: Bash, Read, Write, Glob, Grep, AskUserQuestion
---

# Task Create

Create a single task file from a PRD, one at a time.

## Usage
```
/task-create <feature_name> <task_description>
```

- `feature_name`: The PRD name (kebab-case)
- `task_description`: Brief description of which task to create (e.g., "setup database models", "implement auth API")

## Required Rules

**IMPORTANT:** Before executing this command, read and follow:
- `.claude/rules/datetime.md` - For getting real current date/time

## Preflight Checklist

Before proceeding, complete these validation steps.
Do not bother the user with preflight checks progress. Just do them and move on.

1. **Verify PRD exists:**
   - Check if `.claude/prds/<feature_name>.md` exists
   - If not found, tell user: "PRD not found: <feature_name>. First create it with: /prd-new <feature_name>"
   - Stop execution if PRD doesn't exist

2. **Validate PRD frontmatter:**
   - Verify PRD has valid frontmatter with: name, description, status, created
   - If invalid, tell user: "Invalid PRD frontmatter. Please check: .claude/prds/<feature_name>.md"

3. **Check existing tasks:**
   - List any existing task files in `.claude/tasks/<feature_name>/`
   - Determine the next task number (001, 002, 003, etc.)

4. **Ensure directory exists:**
   - Create `.claude/tasks/<feature_name>/` if it doesn't exist

## Instructions

You are creating a single task for feature: **$ARGUMENTS**

### 1. Read the PRD
- Load the PRD from `.claude/prds/<feature_name>.md`
- Understand the technical requirements
- Review the Implementation Plan / Task Breakdown section
- Identify the specific task the user wants to create

### 2. Analyze the Request
- Match the user's task description to tasks in the PRD
- If the task isn't clearly in the PRD:
  - Ask for clarification using `AskUserQuestion`
  - Or suggest related tasks from the PRD

### 3. Determine Task Number
- Check existing tasks in `.claude/tasks/<feature_name>/`
- Use the next sequential number (001.md, 002.md, etc.)

### 4. Create the Task File

Create the task at `.claude/tasks/<feature_name>/<number>.md`:

```markdown
---
name: [Task Title]
status: open
created: [Current ISO date/time]
updated: [Current ISO date/time]
prd: .claude/prds/<feature_name>.md
depends_on: []
---

# Task: [Task Title]

## Description
Clear, concise description of what needs to be done.
Reference the relevant section from the PRD.

## Acceptance Criteria
- [ ] Specific criterion 1
- [ ] Specific criterion 2
- [ ] Specific criterion 3
- [ ] Tests written and passing

## Technical Approach
- Implementation approach from PRD
- Key files to create/modify
- Patterns to follow

## Files to Modify
- `path/to/file1.ts` - Description of changes
- `path/to/file2.ts` - Description of changes

## Dependencies
- [ ] Required before starting (other tasks, setup, etc.)
- [ ] External dependencies (packages, APIs, etc.)

## Effort Estimate
- Size: XS/S/M/L/XL
- Estimated time: [hours/days]

## Notes
Any additional context, edge cases, or considerations.
```

### 5. Frontmatter Guidelines
- **name**: Descriptive task title (without "Task:" prefix)
- **status**: Always "open" for new tasks
- **created**: Get REAL current datetime: `date -u +"%Y-%m-%dT%H:%M:%SZ"`
- **updated**: Same as created for new tasks
- **prd**: Reference to source PRD file
- **depends_on**: List task numbers that must complete first (e.g., ["001", "002"])

### 6. Quality Checks

Before saving the task, verify:
- [ ] Acceptance criteria are specific and testable
- [ ] Technical approach is clear and actionable
- [ ] Files to modify are identified
- [ ] Dependencies are listed
- [ ] Task is appropriately sized (ideally completable in 1-3 days)

### 7. Post-Creation

After successfully creating the task:
1. Confirm: "Task created: .claude/tasks/<feature_name>/<number>.md"
2. Show summary:
   - Task title
   - Effort estimate
   - Dependencies
3. Suggest next steps:
   - "Ready to start? Run: /task-start <feature_name> <number>"
   - "Create another task? Run: /task-create <feature_name> <description>"

## Error Recovery

If any step fails:
- Clearly explain what went wrong
- If task description doesn't match PRD, suggest matching tasks
- Never leave partial or corrupted files

## Tips

- Create tasks in logical implementation order
- First task should typically be setup/scaffolding
- Keep tasks focused - one clear objective per task
- If a task feels too big, split it into multiple tasks
