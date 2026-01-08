---
allowed-tools: Bash, Read, Write, Glob, Grep, Task
---

# Create Initial Context

Create the initial project context documentation in `.claude/context/` by analyzing the current project state.

## Usage
```
/context-create
```

## Required Rules

**IMPORTANT:** Before executing this command, read and follow:
- `.claude/rules/datetime.md` - For getting real current date/time

## Preflight Checklist

Before proceeding, complete these validation steps.
Do not bother the user with preflight checks progress. Just do them and move on.

### 1. Context Directory Check
- Run: `ls -la .claude/context/ 2>/dev/null`
- If directory exists and has files:
  - Ask user: "Found existing context files. Overwrite all? (yes/no)"
  - Only proceed with explicit 'yes' confirmation
  - If no, suggest: "Use /context-update to refresh existing context"

### 2. Project Type Detection
- Check for project indicators:
  - Node.js: `package.json`
  - Python: `requirements.txt` or `pyproject.toml`
  - iOS/Swift: `Package.swift` or `*.xcodeproj`
  - Go: `go.mod`
- Run: `git status 2>/dev/null` to confirm git repository

### 3. Directory Creation
- Create `.claude/context/` if it doesn't exist
- Verify write permissions

### 4. Get Current DateTime
- Run: `date -u +"%Y-%m-%dT%H:%M:%SZ"`
- Store for use in all context file frontmatter

## Instructions

### 1. Systematic Project Analysis

Use an exploration agent to analyze the project:

```yaml
Task:
  description: "Analyze project for context"
  subagent_type: "Explore"
  prompt: |
    Analyze this project thoroughly to gather context for documentation.

    Gather:
    1. Project type and primary language
    2. Directory structure and organization
    3. Dependencies and technologies used
    4. Architectural patterns observed
    5. Coding conventions and style patterns
    6. Current project status (git status, recent commits)

    Return a structured summary of findings.
```

### 2. Context Files to Create

Create these 5 context files in `.claude/context/`:

#### `progress.md`
Current project status and recent work.
```markdown
---
created: [datetime]
last_updated: [datetime]
version: 1.0
---

# Progress

## Current Status
- Branch: {current_branch}
- Last commit: {recent_commit}
- Uncommitted changes: {yes/no}

## Recent Work
{List of recent commits with summaries}

## Current Focus
{What is being worked on}

## Next Steps
{Immediate next tasks}

## Blockers
{Any current blockers or issues}
```

#### `project-structure.md`
Directory structure and file organization.
```markdown
---
created: [datetime]
last_updated: [datetime]
version: 1.0
---

# Project Structure

## Directory Layout
{Tree structure of key directories}

## Key Directories
- `src/` - {description}
- `tests/` - {description}
- etc.

## File Organization
{How files are organized, naming patterns}

## Entry Points
{Main entry points to the application}
```

#### `tech-context.md`
Dependencies, technologies, and development tools.
```markdown
---
created: [datetime]
last_updated: [datetime]
version: 1.0
---

# Technical Context

## Language & Runtime
- Primary Language: {language}
- Version: {version}

## Framework
- {framework name and version}

## Dependencies
### Production
{List key production dependencies}

### Development
{List key dev dependencies}

## Development Tools
- Package Manager: {npm/pip/etc}
- Build Tool: {if any}
- Linting: {eslint/prettier/etc}
- Testing: {jest/pytest/etc}

## Environment
- Required env variables
- Configuration files
```

#### `system-patterns.md`
Architectural patterns and design decisions.
```markdown
---
created: [datetime]
last_updated: [datetime]
version: 1.0
---

# System Patterns

## Architecture Style
{e.g., MVC, Clean Architecture, etc.}

## Design Patterns Used
- {Pattern 1}: Where and why
- {Pattern 2}: Where and why

## Data Flow
{How data flows through the application}

## State Management
{How state is managed}

## Error Handling
{Error handling approach}

## API Patterns
{If applicable - REST, GraphQL, etc.}
```

#### `project-style-guide.md`
Coding standards, conventions, and style preferences.
```markdown
---
created: [datetime]
last_updated: [datetime]
version: 1.0
---

# Project Style Guide

## Naming Conventions
- Files: {kebab-case/camelCase/etc}
- Variables: {camelCase/snake_case/etc}
- Functions: {conventions}
- Components: {conventions}

## Code Organization
- Import order
- File structure within modules

## Formatting
- Indentation: {tabs/spaces, size}
- Line length: {limit}
- Quotes: {single/double}

## Documentation
- Comment style
- JSDoc/docstring conventions

## Testing Conventions
- Test file naming
- Test structure
- Mocking approach

## Git Conventions
- Branch naming
- Commit message format
```

### 3. Quality Validation

After creating each file:
- Verify file was created successfully
- Check file has meaningful content (not just template)
- Ensure frontmatter is present and valid

### 4. Post-Creation Summary

```
Context Creation Complete

Created context in: .claude/context/
Files created: 5/5

Context Summary:
  - Project Type: {detected_type}
  - Language: {primary_language}
  - Dependencies: {count} packages

Files:
  - progress.md - Current status and recent work
  - project-structure.md - Directory organization
  - tech-context.md - Dependencies and tools
  - system-patterns.md - Architecture patterns
  - project-style-guide.md - Coding conventions

Next: Use /context-prime to load context in new sessions
Tip: Run /context-update regularly to keep context current
```

## Error Handling

- **No write permissions:** "Cannot write to .claude/context/. Check permissions."
- **File creation failed:** Report which files succeeded vs failed
- Never leave corrupted or incomplete files

## Important Notes

- **Always use real datetime** from system clock
- **Ask for confirmation** before overwriting
- **Validate each file** is created successfully
