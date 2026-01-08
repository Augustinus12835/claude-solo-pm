---
allowed-tools: Bash, Read, Write, Edit, Glob, Grep, Task, AskUserQuestion
---

# Refactor Task Create

Create a refactoring task using the refactor-planner agent to analyze and plan.

## Usage
```
/refactor-task-create <refactor_name> <target_description>
```

- `refactor_name`: Kebab-case name for the refactoring (e.g., auth-module, api-layer)
- `target_description`: Description of what to refactor (e.g., "the authentication module", "duplicate code in services/")

## Required Rules

**IMPORTANT:** Before executing this command, read and follow:
- `.claude/rules/datetime.md` - For getting real current date/time
- `.claude/rules/frontmatter-operations.md` - For frontmatter format

## Preflight Checklist

Before proceeding, complete these validation steps.
Do not bother the user with preflight checks progress. Just do them and move on.

### 1. Validate Refactor Name Format
- Must contain only lowercase letters, numbers, and hyphens
- Must start with a letter
- If invalid: "Refactor name must be kebab-case (lowercase letters, numbers, hyphens only). Examples: auth-module, api-cleanup, duplicate-removal"

### 2. Check for Existing Refactor Task
- Check if `.claude/tasks/refactor/<refactor_name>.md` already exists
- If it exists, ask: "Refactor task '<refactor_name>' already exists. Overwrite? (yes/no)"
- Only proceed with explicit 'yes'

### 3. Ensure Directory Exists
- Create `.claude/tasks/refactor/` if it doesn't exist

### 4. Get Current DateTime
- Run: `date -u +"%Y-%m-%dT%H:%M:%SZ"`

## Instructions

### Phase 1: Launch Refactor Planner Agent

Use the Task tool to launch the refactor-planner agent for analysis:

```yaml
Task:
  description: "Analyze codebase for refactoring"
  subagent_type: "general-purpose"
  prompt: |
    You are the refactor-planner agent. Read .claude/agents/refactor-planner.md for your instructions.

    Analyze the codebase for refactoring: <target_description>

    Your task:
    1. Explore the relevant parts of the codebase
    2. Identify issues and refactoring opportunities
    3. Create a comprehensive refactoring plan following the output format in your instructions

    Be thorough in your analysis. Provide specific file paths and code references.

    Return your complete analysis and plan.
```

### Phase 2: Review Analysis with User

Present the refactor-planner's analysis to the user:

```markdown
## Refactoring Analysis: <refactor_name>

{Analysis from refactor-planner agent}

---

Does this refactoring plan look good? (yes/modify/cancel)
```

If user wants modifications:
- Ask what to change
- Re-run analysis with adjustments
- Present updated plan

### Phase 3: Create Refactor Task File

Once approved, create the task file at `.claude/tasks/refactor/<refactor_name>.md`:

```markdown
---
name: <refactor_name>
type: refactor
status: open
created: [Current ISO datetime]
updated: [Current ISO datetime]
target: <target_description>
---

# Refactor: <Refactor Name>

## Overview
<Brief description of this refactoring effort>

## Analysis Summary
<Summary from refactor-planner>

## Issues Identified
<List of issues found>

## Refactoring Plan

### Phase 1: [Phase Name]
- **Goal**: [What this phase achieves]
- **Changes**:
  - [ ] Change 1
  - [ ] Change 2
- **Files Affected**: [list]
- **Risk Level**: Low/Medium/High

### Phase 2: [Phase Name]
...

## Risk Assessment
<Risks and mitigation strategies>

## Testing Strategy
<How to verify the refactoring>

## Success Metrics
- [ ] Metric 1
- [ ] Metric 2

## Notes
<Any additional notes>
```

### Phase 4: Post-Creation Summary

```
Refactor Task Created!

Task: .claude/tasks/refactor/<refactor_name>.md
Target: <target_description>
Status: open

Analysis Summary:
  - Issues Found: <count> (Critical: X, Major: Y, Minor: Z)
  - Phases Planned: <count>
  - Estimated Effort: <estimate>

Next Steps:
  - Start refactoring: /task-start refactor <refactor_name>
  - Or review the task file first

Tip: Refactoring tasks follow the same workflow as feature tasks:
  /task-start → implement → /code-review → /task-close
```

## Error Handling

- **Analysis fails:** Report what was found, suggest narrowing scope
- **No issues found:** Report codebase is in good shape, ask if user wants to analyze different area
- **Scope too large:** Suggest breaking into smaller refactor tasks

## Important Notes

- **Analysis first** - Always analyze before creating the task
- **Incremental phases** - Break large refactors into safe, incremental steps
- **Risk awareness** - Each phase should have a rollback strategy
- **Testing emphasis** - Refactoring without tests is risky
