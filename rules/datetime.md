# DateTime Rule

## Getting Current Date and Time

When any command requires the current date/time (for frontmatter, timestamps, or logs), you MUST obtain the REAL current date/time from the system rather than estimating or using placeholder values.

### How to Get Current DateTime

Use the `date` command to get the current ISO 8601 formatted datetime:

```bash
# Get current datetime in ISO 8601 format (works on Linux/Mac)
date -u +"%Y-%m-%dT%H:%M:%SZ"
```

### Required Format

All dates in frontmatter MUST use ISO 8601 format with UTC timezone:
- Format: `YYYY-MM-DDTHH:MM:SSZ`
- Example: `2024-01-15T14:30:45Z`

### Usage in Frontmatter

When creating or updating frontmatter in any file (PRD, Task, Progress), always use the real current datetime:

```yaml
---
name: feature-name
created: 2024-01-15T14:30:45Z  # Use actual output from date command
updated: 2024-01-15T14:30:45Z  # Use actual output from date command
---
```

### Implementation Instructions

1. **Before writing any file with frontmatter:**
   - Run: `date -u +"%Y-%m-%dT%H:%M:%SZ"`
   - Store the output
   - Use this exact value in the frontmatter

2. **For commands that create files:**
   - PRD creation: Use real date for `created` field
   - Task creation: Use real date for both `created` and `updated` fields

3. **For commands that update files:**
   - Always update the `updated` field with current real datetime
   - Preserve the original `created` field

### Important Notes

- **Never use placeholder dates** like `[Current ISO date/time]` or `YYYY-MM-DD`
- **Never estimate dates** - always get the actual system time
- **Always use UTC** (the `Z` suffix) for consistency across timezones

## Rule Priority

This rule has **HIGHEST PRIORITY** and must be followed by all commands that:
- Create new files with frontmatter
- Update existing files with frontmatter
- Track timestamps or progress
