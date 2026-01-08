---
allowed-tools: Bash, Read, Write, Edit, Glob, Grep, Task
---

# Task Start

Begin work on a task: handle git, update task status, and enter plan mode.

## Usage
```
/task-start <feature_name> <task_number>
```

- `feature_name`: The PRD/feature name (kebab-case)
- `task_number`: The task number (e.g., 001, 002)

## Required Rules

**IMPORTANT:** Before executing this command, read and follow:
- `.claude/rules/datetime.md` - For getting real current date/time
- `.claude/skills/planning-with-files/SKILL.md` - For planning workflow

## Preflight Checklist

Before proceeding, complete these validation steps.
Do not bother the user with preflight checks progress. Just do them and move on.

1. **Verify task file exists:**
   - Check if `.claude/tasks/<feature_name>/<task_number>.md` exists
   - If not found, tell user: "Task not found. Create it with: /task-create <feature_name> <description>"

2. **Validate task frontmatter:**
   - Verify task has valid frontmatter with: name, status, created
   - Check task status is "open" (not already in-progress or closed)
   - If status is "in-progress", ask: "Task already in progress. Continue working on it?"
   - If status is "closed", tell user: "Task is already closed. Create a new task if needed."

3. **Check dependencies:**
   - Read `depends_on` from task frontmatter
   - For each dependency, check if that task's status is "closed"
   - If dependencies not met, warn: "Warning: Task depends on [X] which is not closed yet. Continue anyway?"

## Instructions

### Phase 1: Git Housekeeping

**Handle any uncommitted work first:**

```bash
# Check for uncommitted changes
if [ -n "$(git status --porcelain)" ]; then
  echo "Found uncommitted changes..."
fi
```

If there are uncommitted changes:
1. Ask user: "You have uncommitted changes. What would you like to do?"
   - Option A: Commit and push to current branch
   - Option B: Stash changes
   - Option C: Cancel task-start

If user chooses to commit:
```bash
git add .
git commit -m "WIP: Save work before starting task <task_number>"
git push
```

### Phase 2: Create/Enter Task Branch

```bash
# Get current branch
current_branch=$(git branch --show-current)

# Define task branch name
task_branch="task/<feature_name>-<task_number>"

# Check if branch exists
if git branch -a | grep -q "$task_branch"; then
  # Branch exists, switch to it
  git checkout $task_branch
  git pull origin $task_branch 2>/dev/null || true
  echo "Switched to existing branch: $task_branch"
else
  # Create new branch from main
  git checkout main
  git pull origin main
  git checkout -b $task_branch
  echo "Created new branch: $task_branch"
fi
```

### Phase 3: Update Task Status

Get current datetime: `date -u +"%Y-%m-%dT%H:%M:%SZ"`

Update the task file frontmatter:
- Change `status: open` to `status: in-progress`
- Update `updated:` field with current datetime
- Add `started:` field with current datetime
- Add `branch:` field with branch name

Updated frontmatter example:
```yaml
---
name: Task Title
status: in-progress
created: 2024-01-10T09:00:00Z
updated: 2024-01-15T14:30:00Z
started: 2024-01-15T14:30:00Z
branch: task/feature-name-001
prd: .claude/prds/feature-name.md
depends_on: []
---
```

### Phase 4: Enter Plan Mode (using planning-with-files skill)

**IMPORTANT:** Before implementing, you MUST plan first using the planning-with-files skill.

1. **Read the task file** completely
2. **Read the PRD** referenced in the task for full context
3. **Explore the codebase** to understand:
   - Current architecture relevant to this task
   - Existing patterns to follow
   - Files that need modification

4. **Create `task_plan.md`** in the working directory following the planning-with-files skill:

```markdown
# Task Plan: <task_number> - <task_name>

## Goal
[One sentence describing the end state for this task]

## Context
- Task File: .claude/tasks/<feature_name>/<task_number>.md
- PRD: .claude/prds/<feature_name>.md
- Branch: task/<feature_name>-<task_number>

## Phases
- [ ] Phase 1: Plan and setup
- [ ] Phase 2: Research/explore codebase
- [ ] Phase 3: Implement core changes
- [ ] Phase 4: Write tests
- [ ] Phase 5: Review and refine

## Implementation Steps
1. **Step 1**: Description
   - Files: `path/to/file.ts`
   - Changes: What we'll do

2. **Step 2**: Description
   - Files: `path/to/file.ts`
   - Changes: What we'll do

## Key Questions
1. [Question to answer during research]
2. [Question to answer during research]

## Decisions Made
- [Decision]: [Rationale]

## Files to Modify
- `path/to/file1.ts` - Description
- `path/to/file2.ts` - Description

## Testing Approach
- How we'll verify this works

## Risks/Considerations
- Any edge cases or concerns

## Errors Encountered
- [None yet]

## Status
**Currently in Phase 1** - Creating plan
```

5. **Create `notes.md`** if research is needed:
```markdown
# Notes: <task_name>

## Research Findings
[Findings from codebase exploration]

## Code Patterns Observed
[Existing patterns to follow]

## Dependencies Identified
[Any dependencies or prerequisites]
```

### Phase 5: Plan Review

**Before presenting to user, have the plan reviewed.**

Use the Task tool to launch the plan-reviewer agent:

```yaml
Task:
  description: "Review implementation plan"
  subagent_type: "general-purpose"
  prompt: |
    You are the plan-reviewer agent. Read .claude/agents/plan-reviewer.md for your instructions.

    Review this implementation plan for Task <task_number>:

    <plan>
    {the implementation plan created above}
    </plan>

    Context:
    - Task file: .claude/tasks/<feature_name>/<task_number>.md
    - PRD: .claude/prds/<feature_name>.md

    Provide your review in the specified format. Be thorough but concise.
```

### Phase 6: Present Plan and Review to User

Present both the plan AND the review to the user:

```markdown
## Implementation Plan for Task <task_number>: <task_name>

{The implementation plan}

---

## Plan Review (by plan-reviewer agent)

{The review from plan-reviewer}

---

Ready to proceed with implementation? (yes/no/modify)
```

If the review identifies critical issues:
- Revise the plan to address them
- Re-run the review if changes are significant
- Then present to user

If user requests modifications:
- Update the plan accordingly
- Re-run review if changes are significant
- Present updated plan for approval

### Phase 7: Implementation (following planning-with-files workflow)

Once the plan is approved, follow the planning-with-files skill workflow:

**Before each major action:**
```bash
Read task_plan.md  # Refresh goals in attention window
```

**Implementation loop:**
1. **Read task_plan.md** to refresh current phase and goals
2. **Execute the current phase** as planned
3. **Update task_plan.md** immediately after:
   - Mark completed phases with [x]
   - Update the Status section
   - Log any errors in "Errors Encountered"
   - Record decisions in "Decisions Made"
4. **Commit changes**:
   ```bash
   git add <files>
   git commit -m "task/<feature>-<number>: <specific change>"
   ```
5. **Repeat** for each phase until complete

**Critical rules from planning-with-files:**
- Read plan before every major decision
- Update plan after every phase completion
- Log ALL errors encountered (builds knowledge)
- Store large findings in notes.md, not in context

### Phase 8: Completion

When implementation is complete:

1. **Update task_plan.md** - mark all phases complete:
   ```markdown
   ## Status
   **COMPLETED** - All phases done, ready for review
   ```

2. **Final commit** (if not already committed):
   ```bash
   git add .
   git commit -m "task/<feature>-<number>: Complete implementation"
   ```

3. **DO NOT push** - wait for code review

4. **Update task file**:
   - Update `updated:` field with current datetime
   - Keep status as `in-progress` (will change to `closed` after review)

5. **Output summary**:
   ```
   Task implementation complete!

   Branch: task/<feature>-<number>
   Commits: <count> commits made

   Files changed:
   - path/to/file1.ts
   - path/to/file2.ts

   Planning files:
   - task_plan.md (all phases completed)
   - notes.md (if created)

   Next steps:
   - Review changes: /code-review <feature_name> <task_number>
   - Then close: /task-close <feature_name> <task_number>
   ```

## Error Handling

If any step fails:
- Clearly explain what went wrong
- Provide recovery steps
- Never leave git in an inconsistent state

If implementation differs from plan:
- Update the plan and get approval before continuing
- Document significant deviations in task file notes

## Important Notes

- **Always plan before implementing** - this is critical
- Keep commits small and focused
- Follow existing code patterns in the project
- Don't push until code review is done
