---
allowed-tools: Bash, Read, Write, Glob, Grep, Task, AskUserQuestion
---

# PRD New

Create a comprehensive Product Requirements Document (PRD) through spec file analysis and in-depth interview.

## Usage
```
/prd-new <feature_name> [spec_file_path]
```

- `feature_name`: Required. Kebab-case name for the feature (e.g., user-auth, payment-v2)
- `spec_file_path`: Optional. Path to a brainstorm/spec file from Claude.ai to use as reference

## Required Rules

**IMPORTANT:** Before executing this command, read and follow:
- `.claude/rules/datetime.md` - For getting real current date/time

## Preflight Checklist

Before proceeding, complete these validation steps.
Do not bother the user with preflight checks progress. Just do them and move on.

### Input Validation
1. **Validate feature name format:**
   - Must contain only lowercase letters, numbers, and hyphens
   - Must start with a letter
   - No spaces or special characters allowed
   - If invalid, tell user: "Feature name must be kebab-case (lowercase letters, numbers, hyphens only). Examples: user-auth, payment-v2, notification-system"

2. **Check for existing PRD:**
   - Check if `.claude/prds/<feature_name>.md` already exists
   - If it exists, ask user: "PRD '<feature_name>' already exists. Do you want to overwrite it? (yes/no)"
   - Only proceed with explicit 'yes' confirmation

3. **Verify directory structure:**
   - Check if `.claude/prds/` directory exists
   - If not, create it first

4. **If spec file provided:**
   - Verify the spec file exists at the given path
   - If not found, tell user: "Spec file not found at: <path>. Please provide a valid path."

## Instructions

You are a technical product manager creating a comprehensive PRD for: **$ARGUMENTS**

### Phase 1: Read Reference Material (if provided)

If a spec file path was provided:
1. **Read the spec file** thoroughly
2. **Analyze** to understand:
   - Core feature/product being proposed
   - Initial technical considerations
   - User needs and goals
   - Any gaps or ambiguities

### Phase 2: In-Depth Interview

**Use the `AskUserQuestion` tool** to conduct a comprehensive interview.

Interview Guidelines:
- Ask about **technical implementation** - architecture choices, data models, API designs
- Ask about **UI & UX** - user flows, edge cases, error states
- Ask about **concerns** - what worries them, what could go wrong
- Ask about **tradeoffs** - performance vs simplicity, scope vs timeline
- **Do NOT ask obvious questions** already answered in the spec file
- **Be very in-depth** - dig into specifics, ask follow-ups
- **Continue interviewing** until you have a complete picture

Key Areas to Cover:
1. **Scope**: What's in/out of scope?
2. **Technical Constraints**: Performance requirements? Platform limitations?
3. **Integration Points**: How does this connect to existing systems?
4. **Data Requirements**: What data is needed? Where from?
5. **Edge Cases**: What happens when things fail?
6. **Prioritization**: If we can't do everything, what's critical?

### Phase 3: Write the PRD

After interview is complete, create the PRD at `.claude/prds/<feature_name>.md`:

```markdown
---
name: <feature_name>
description: [Brief one-line description]
status: backlog
created: [Current ISO date/time from date command]
spec_file: [Path to spec file if provided, or "none"]
---

# PRD: <Feature Name>

## Overview
Brief summary of what we're building and why.

### Problem Statement
What problem are we solving? Why now?

### Goals
- Primary goal
- Secondary goals

### Non-Goals (Out of Scope)
Explicitly list what this PRD does NOT cover.

## Technical Requirements

### Architecture Overview
High-level technical approach and key decisions.

### Data Model
Key entities, relationships, and schema considerations.

### API Design
Endpoints, request/response formats, authentication.

### Integration Points
How this connects to existing systems.

### Performance Requirements
Load expectations, response time targets, scaling needs.

### Security Considerations
Auth, data protection, privacy requirements.

## Functional Specifications

### Core Features
Detailed description of each feature with acceptance criteria.

### User Flows
Step-by-step flows for key scenarios.

### Edge Cases & Error Handling
How the system behaves in non-happy-path scenarios.

## UI/UX Specifications

### Key Screens/Views
Description of main UI elements and interactions.

### Accessibility Requirements
A11y considerations.

## Implementation Plan

### Task Breakdown
Detailed tasks in implementation order:

#### Task 1: [Task Name]
- **Description**: What needs to be done
- **Technical Approach**: How to implement it
- **Acceptance Criteria**: How we know it's done
- **Dependencies**: What must be completed first
- **Complexity**: Small / Medium / Large

#### Task 2: [Task Name]
...

(Continue for all tasks - be thorough, no artificial limits)

### Dependencies & Risks
- Technical dependencies
- External dependencies
- Risks and mitigation strategies

### Testing Strategy
- Unit test coverage areas
- Integration test scenarios
- E2E test cases

## Success Criteria
How will we measure success?
- Metric 1: Target value
- Metric 2: Target value

## Open Questions
Unresolved questions that need answers before/during implementation.

## References
- Link to original spec file (if any)
- Related documentation
- Design mockups (if any)
```

### Phase 4: Quality Checks

Before saving the PRD, verify:
- [ ] All sections are complete (no placeholder text)
- [ ] Technical approach is detailed and actionable
- [ ] Task breakdown covers all implementation work
- [ ] Dependencies are clearly identified
- [ ] Out of scope items are explicitly listed

### Phase 5: Post-Creation

After successfully creating the PRD:
1. Confirm: "PRD created: .claude/prds/<feature_name>.md"
2. Show brief summary of:
   - Number of tasks identified
   - Key technical decisions
   - Any open questions
3. Suggest next step: "Ready to start implementation? Run: /task-start <feature_name> 1"

## Error Recovery

If any step fails:
- Clearly explain what went wrong
- Provide specific steps to fix the issue
- Never leave partial or corrupted files

## Important Notes

- Focus on **technical implementation** over user stories
- The PRD should read like a **technical spec** that developers can implement from
- Include **all necessary tasks** - no artificial limits on task count
- Be thorough in the **task breakdown** - this replaces the need for a separate epic file
