# Context Fetch Command

You are tasked with generating a comprehensive context document for a feature or refactoring that the user wants to research with Claude.ai.

## User's Request
$ARGUMENTS

## Instructions

1. **Launch an Exploration Agent** to thoroughly understand the codebase related to the user's request. Focus on:
   - Finding all relevant files, modules, and components
   - Understanding the current architecture and patterns
   - Identifying entry points and data flows
   - Mapping dependencies and relationships
   - Noting any existing similar implementations

2. **Create a Context Document** at `docs/context-{feature-name}.md` with the following structure:

```markdown
# Context: {Feature/Refactoring Name}

Generated: {date}

## Overview
Brief description of what this context document covers and why.

## Relevant Files & Directories
List all relevant files with their purposes:
- `path/to/file.ts` - Description of what this file does and why it's relevant

## Architecture Overview
Describe the relevant parts of the codebase architecture:
- How components/modules are organized
- Key patterns being used (e.g., MVC, service layer, etc.)
- State management approach (if applicable)

## Key Code Sections
Highlight important code sections that Claude.ai should examine:
- Entry points for the feature area
- Core logic locations
- Configuration files
- Type definitions / interfaces

## Dependencies
### Internal Dependencies
- List internal modules/services this area depends on

### External Dependencies
- List relevant npm packages, APIs, or external services

## Current Implementation Patterns
Document patterns currently used that should be followed:
- Naming conventions
- Error handling approach
- Testing patterns
- API patterns (if applicable)

## Data Flow
Describe how data flows through the relevant parts:
- User input -> Processing -> Storage/Output
- State updates
- API calls

## Potential Impact Areas
Areas that might be affected by changes:
- Related features
- Shared components
- Tests that may need updates

## Notes for Claude.ai
Specific things to be aware of:
- Known constraints or limitations
- Technical debt to consider
- Performance considerations
- Security considerations (if any)

## Recommended Reading Order
Suggest the order in which Claude.ai should read the files for best understanding:
1. Start with...
2. Then look at...
3. Finally review...
```

3. **Be Thorough**: The goal is to provide enough context so that Claude.ai can give excellent architectural advice and implementation plans without needing to explore the codebase itself.

4. **Create the docs directory** if it doesn't exist.

5. After creating the document, summarize what was created and where to find it.
