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
5. (code-review)      → Review code with plugin (see Plugins section)
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

## Plugins (Recommended)

This system works well with official Claude Code plugins. Install them using the `/plugin` command or load locally.

### Installing Plugins

```bash
# Interactive installation
/plugin install

# Or load locally for testing
claude --plugin-dir /path/to/plugin
```

### Recommended Plugins

| Plugin | Description | Install |
|--------|-------------|---------|
| **[code-review](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/code-review)** | Automated code review on PRs using multiple specialized agents (compliance, bug detection, history analysis) | Essential for code quality |
| **[commit-commands](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/commit-commands)** | Smart commit message generation and git workflow commands | Streamlines git operations |
| **[ralph-loop](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/ralph-loop)** | Continuous improvement loop for iterative development | Great for refinement cycles |
| **[frontend-design](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/frontend-design)** | UI/UX design assistance and frontend development patterns | Useful for web/app development |
| **[feature-dev](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/feature-dev)** | Full feature development workflow with planning and implementation | Alternative to this PM system |

### Plugin Configuration

Plugins are configured in settings files:

```
~/.claude/settings.json              # User-level (all projects)
<project>/.claude/settings.json      # Project-level (shared)
<project>/.claude/settings.local.json # Local override (not committed)
```

Example configuration:
```json
{
  "enabledPlugins": {
    "code-review@anthropic-agent-plugins": true,
    "commit-commands@anthropic-agent-plugins": true
  }
}
```

## Folder Structure

```
.claude/
├── agents/
│   ├── plan-reviewer.md
│   └── refactor-planner.md
├── commands/
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

3. Install recommended plugins (see Plugins section above).

## Key Features

- **Plan Before Implement**: Every task starts with a planning phase reviewed by the plan-reviewer agent
- **Planning with Files**: Uses Manus-style `task_plan.md` for persistent progress tracking
- **One Task at a Time**: Create tasks incrementally as the feature evolves
- **Plugin Integration**: Works seamlessly with official Claude Code plugins
- **Context Management**: Keep project context up-to-date for better AI assistance

## Acknowledgments

This project builds upon and is inspired by several excellent open-source projects and ideas:

- **[CCPM (Claude Code Project Manager)](https://github.com/automazeio/ccpm)** by Automazeio - The foundation for the PM workflow, commands structure, and many patterns adapted here.

- **[Claude Code Infrastructure Showcase](https://github.com/diet103/claude-code-infrastructure-showcase)** by diet103 - Agents (plan-reviewer, code-architecture-reviewer) and project structure inspiration.

- **[Planning with Files](https://github.com/OthmanAdi/planning-with-files)** by OthmanAdi - The Manus-style planning skill for persistent markdown-based progress tracking.

- **Thariq ([@trq212](https://x.com/trq212))** - The interview method for PRD creation ([original post](https://x.com/trq212/status/2005315275026260309)).

- **[Claude Plugins Official](https://github.com/anthropics/claude-plugins-official)** by Anthropic - Official plugins for code review, commit commands, and more.

## License

MIT License - Feel free to use and modify for your own projects.

---

Built for solo developers who want AI-assisted project management without the complexity.
