# Claude Solo PM

A streamlined Claude Code project management system designed for solo developers. This is a customized `.claude` folder setup with commands, agents, rules, and skills for managing feature development, refactoring, and project context.

## Overview

Claude Solo PM simplifies the development workflow for solo developers building web applications and iOS apps. It removes the complexity of parallel agents, worktrees, and GitHub issue sync found in team-oriented tools, focusing instead on a clean, linear workflow.

## Workflow

```
1. /context-fetch     → Generate context for Claude.ai brainstorming
2. /prd-new           → Create PRD with in-depth interview
3. /task-create       → Create one task at a time from PRD
4. /task-start        → Start task (git branch + plan mode)
5. /code-review       → Review code with analyzer agent
6. /task-close        → Close task, push, create PR
7. /context-update    → Update project context
```

## Commands

### Feature Development
| Command | Description |
|---------|-------------|
| `/prd-new <name> [spec_file]` | Create PRD with interview process |
| `/task-create <feature> <description>` | Create a single task from PRD |
| `/task-start <feature> <number>` | Start task with planning workflow |
| `/code-review <feature> <number>` | Review code changes |
| `/task-close <feature> <number>` | Close task, push, create PR |

### Refactoring
| Command | Description |
|---------|-------------|
| `/refactor-task-create <name> <target>` | Create refactor task with analysis |

### Context Management
| Command | Description |
|---------|-------------|
| `/context-create` | Create initial project context |
| `/context-update` | Update context after changes |
| `/context-prime` | Load context for new session |
| `/context-fetch <description>` | Generate context doc for Claude.ai |

## Agents

| Agent | Purpose |
|-------|---------|
| `plan-reviewer` | Reviews implementation plans before execution |
| `code-analyzer` | Analyzes code for bugs and issues |
| `refactor-planner` | Creates comprehensive refactoring plans |

## Skills

| Skill | Purpose |
|-------|---------|
| `planning-with-files` | Manus-style persistent markdown planning |

## Rules

| Rule | Purpose |
|------|---------|
| `datetime` | Standardized datetime handling |
| `frontmatter-operations` | YAML frontmatter patterns |

## Folder Structure

```
.claude/
├── agents/
│   ├── code-analyzer.md
│   ├── plan-reviewer.md
│   └── refactor-planner.md
├── commands/
│   ├── code-review.md
│   ├── context-create.md
│   ├── context-fetch.md
│   ├── context-prime.md
│   ├── context-update.md
│   ├── prd-new.md
│   ├── refactor-task-create.md
│   ├── task-close.md
│   ├── task-create.md
│   └── task-start.md
├── context/           # Project context files
├── prds/              # Product requirement documents
├── rules/
│   ├── datetime.md
│   └── frontmatter-operations.md
├── skills/
│   └── planning-with-files/
└── tasks/
    ├── <feature>/     # Feature task files
    └── refactor/      # Refactoring task files
```

## Installation

1. Clone this repo into your project's `.claude` folder:
   ```bash
   git clone https://github.com/Augustinus12835/claude-solo-pm.git .claude
   ```

2. Or copy the contents to your existing `.claude` folder.

## Key Features

- **Plan Before Implement**: Every task starts with a planning phase reviewed by the plan-reviewer agent
- **Planning with Files**: Uses Manus-style `task_plan.md` for persistent progress tracking
- **One Task at a Time**: Create tasks incrementally as the feature evolves
- **Code Review Built-in**: Automated code analysis before closing tasks
- **Context Management**: Keep project context up-to-date for better AI assistance

## Acknowledgments

This project builds upon and is inspired by several excellent open-source projects and ideas:

- **[CCPM (Claude Code Project Manager)](https://github.com/automazeio/ccpm)** by Automazeio - The foundation for the PM workflow, commands structure, and many patterns adapted here.

- **[Claude Code Infrastructure Showcase](https://github.com/diet103/claude-code-infrastructure-showcase)** by diet103 - Agents (plan-reviewer, code-architecture-reviewer) and project structure inspiration.

- **[Planning with Files](https://github.com/OthmanAdi/planning-with-files)** by OthmanAdi - The Manus-style planning skill for persistent markdown-based progress tracking.

- **Thariq ([@trq212](https://x.com/trq212))** - The interview method for PRD creation ([original post](https://x.com/trq212/status/2005315275026260309)).

## License

MIT License - Feel free to use and modify for your own projects.

---

Built for solo developers who want AI-assisted project management without the complexity.
