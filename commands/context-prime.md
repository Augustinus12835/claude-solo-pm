---
allowed-tools: Bash, Read, Glob
---

# Prime Context

Load essential project context for a new Claude Code session. Run this at the start of each session to understand the project state.

## Usage
```
/context-prime
```

## Preflight Checklist

Before proceeding, complete these validation steps.
Do not bother the user with preflight checks progress. Just do them and move on.

### 1. Context Availability Check
- Run: `ls -la .claude/context/ 2>/dev/null`
- If directory doesn't exist or is empty:
  - Tell user: "No context found. Please run /context-create first."
  - Exit gracefully
- Count available files and report: "Found {count} context files to load"

### 2. File Integrity Check
- For each context file:
  - Verify file is readable
  - Check file has content (not empty)
  - Check for valid frontmatter (starts with `---`)
- Report any issues:
  - Empty files: "{filename} is empty (skipping)"
  - Missing frontmatter: "{filename} missing frontmatter"

### 3. Project State Check
- Run: `git status --short 2>/dev/null`
- Run: `git branch --show-current 2>/dev/null`

## Instructions

### 1. Context Loading Order

Load context files in this priority order:

**Priority 1 - Current State (most important):**
1. `progress.md` - What's been done, what's next
2. `tech-context.md` - Technical stack and dependencies

**Priority 2 - Structure & Patterns:**
3. `project-structure.md` - Directory and file organization
4. `system-patterns.md` - Architecture and design patterns

**Priority 3 - Conventions:**
5. `project-style-guide.md` - Coding standards

### 2. For Each File

1. Read the file completely
2. Parse and validate frontmatter:
   - `created` date should be valid
   - `last_updated` should be ≥ created date
3. Extract key information for summary
4. Track success/failure

### 3. Supplementary Information

After loading context files:
- Read `README.md` if it exists
- Check for active PRDs: `ls .claude/prds/*.md 2>/dev/null`
- Check for active tasks: `ls .claude/tasks/*/*.md 2>/dev/null`
- Run: `git log --oneline -5` to see recent commits

### 4. Error Recovery

**If critical files are missing:**
- `progress.md` missing: Check recent git commits for status
- `tech-context.md` missing: Analyze package.json/requirements.txt directly
- Other files missing: Continue with partial context, note limitations

**If context is outdated:**
- Check `last_updated` dates
- If any file > 7 days old, suggest: "Context may be outdated. Run /context-update"

### 5. Loading Summary

```
Context Primed Successfully

Project Understanding:
  - Type: {project_type}
  - Language: {primary_language}
  - Branch: {git_branch}
  - Status: {from progress.md}

Context Loaded:
  - progress.md (updated: {date})
  - tech-context.md (updated: {date})
  - project-structure.md (updated: {date})
  - system-patterns.md (updated: {date})
  - project-style-guide.md (updated: {date})

Active Work:
  - PRDs: {count} in .claude/prds/
  - Tasks: {count} in .claude/tasks/

Recent Activity:
  {last 3-5 commits}

Current Focus:
  {from progress.md - what's being worked on}

Ready for development work.
```

### 6. Quick Project Summary

At the end, provide a 2-3 sentence summary:
```
Project Summary:
{What the project is, current state, and immediate focus}
```

## Important Notes

- **Load in priority order** - most important context first
- **Handle missing files gracefully** - don't fail completely
- **Provide clear summary** of project state
- **Note outdated context** - suggest update if stale
- **Show active work** - PRDs and tasks in progress
