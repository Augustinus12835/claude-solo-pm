---
allowed-tools: Bash, Read, Write, Edit, Glob, Grep
---

# Update Context

Update the project context documentation in `.claude/context/` to reflect the current state. Run this at the end of each development session or after completing a task.

## Usage
```
/context-update
```

## Required Rules

**IMPORTANT:** Before executing this command, read and follow:
- `.claude/rules/datetime.md` - For getting real current date/time

## Preflight Checklist

Before proceeding, complete these validation steps.
Do not bother the user with preflight checks progress. Just do them and move on.

### 1. Context Validation
- Run: `ls -la .claude/context/ 2>/dev/null`
- If directory doesn't exist or is empty:
  - Tell user: "No context to update. Please run /context-create first."
  - Exit gracefully
- Count existing files and report: "Found {count} context files to check"

### 2. Change Detection

Gather information about what has changed:

**Git Changes:**
```bash
git status --short
git log --oneline -10
git diff --stat HEAD~5..HEAD 2>/dev/null
```

**Dependency Changes:**
```bash
git diff HEAD~5..HEAD package.json requirements.txt pyproject.toml go.mod Cargo.toml 2>/dev/null
```

### 3. Get Current DateTime
- Run: `date -u +"%Y-%m-%dT%H:%M:%SZ"`
- Store for updating `last_updated` field

## Instructions

### 1. Check Each File for Updates

#### `progress.md` - **Always Update**
This file should always be updated to reflect current state.
- Check: Recent commits, current branch, uncommitted changes
- Update: Latest completed work, current focus, next steps, blockers
- Run: `git log --oneline -5` to get recent commits

#### `project-structure.md` - **Update if Structure Changed**
- Check: `git diff --name-status HEAD~10..HEAD | grep -E '^A'` for new files/directories
- Update only if: New directories added, files reorganized, new entry points
- Skip if: Only file contents changed, no structural changes

#### `tech-context.md` - **Update if Dependencies Changed**
- Check: Package files for new dependencies or version changes
- Update only if: New libraries added, versions upgraded, new tools adopted
- Skip if: No dependency changes

#### `system-patterns.md` - **Update if Architecture Changed**
- Check: New design patterns adopted, architectural refactoring done
- Update only if: Significant architectural changes, new patterns introduced
- Skip if: Only implementation details changed

#### `project-style-guide.md` - **Update if Conventions Changed**
- Check: New linting rules, style decisions, naming convention changes
- Update only if: Coding standards changed, new conventions adopted
- Skip if: No style changes

### 2. Update Strategy

**For each file that needs updating:**

1. **Read existing file** to understand current content
2. **Identify specific sections** that need updates
3. **Preserve frontmatter** but update `last_updated` field:
   ```yaml
   ---
   created: [preserve original]
   last_updated: [Use REAL datetime from date command]
   version: [increment minor version, e.g., 1.0 → 1.1]
   ---
   ```
4. **Make targeted updates** - don't rewrite entire file
5. **Add to Update History** at the bottom if significant change:
   ```markdown
   ## Update History
   - {date}: {summary of what changed}
   ```

### 3. Skip Optimization

**Skip files that don't need updates:**
- If no relevant changes detected, skip the file
- Report skipped files in summary
- Don't update timestamp if content unchanged
- This preserves accurate "last modified" information

### 4. Update Summary

```
Context Update Complete

Update Statistics:
  - Files Scanned: 5
  - Files Updated: {updated_count}
  - Files Skipped: {skipped_count} (no changes needed)

Updated Files:
  - progress.md - Updated recent commits, current status
  - tech-context.md - Added new dependencies
  {list updated files}

Skipped Files (no changes):
  - project-structure.md (no structural changes)
  - system-patterns.md (architecture unchanged)
  {list skipped files}

Last Update: {timestamp}
```

## Error Handling

- **File locked:** "Cannot update {file} - may be open in editor"
- **Permission denied:** "Cannot write to {file} - check permissions"

If update fails:
- Report which files were successfully updated
- Preserve original files (don't leave corrupted state)

## Important Notes

- **Only update files with actual changes** - preserve accurate timestamps
- **Always use real datetime** from system clock
- **Make surgical updates** - don't regenerate entire files
- **progress.md should always be updated** - it tracks current state
