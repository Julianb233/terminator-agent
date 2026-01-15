# The Terminator

**Multi-terminal orchestration agent for Claude Code.**

Coordinate parallel development workflows across multiple terminal instances. Distribute tasks intelligently, generate self-contained agent prompts, and monitor progress across your team.

## Features

- **Task Distribution**: Automatically scan GitHub Issues and distribute across terminals
- **Parallel Execution**: Identify which tasks can run in parallel vs. sequential
- **Agent Prompts**: Generate copy-paste ready prompts for each terminal
- **Progress Monitoring**: Track status across all terminals
- **GitHub Integration**: Auto-update issues, create PRs, manage labels
- **Session Recovery**: Resume interrupted sessions

## Installation

### Option 1: Copy to your Claude Code config

```bash
# Copy the agent definition
cp agents/terminator.md ~/.claude/agents/

# Copy the slash command
cp commands/terminator.md ~/.claude/commands/

# Create state directory
mkdir -p ~/.terminator
```

### Option 2: Manual installation

1. Copy `agents/terminator.md` to `~/.claude/agents/`
2. Copy `commands/terminator.md` to `~/.claude/commands/`
3. Create `~/.terminator/` directory for state persistence

## Usage

### Activation

```
# Using slash command
/terminator

# Natural language
Terminator, activate for myorg/api-service - I have 3 terminals. Focus on bugs.
```

### During a Session

| Command | Action |
|---------|--------|
| `status` | Show progress of all terminals |
| `reassign #123 -> T2` | Move task to different terminal |
| `add "new task"` | Add new task to queue |
| `blocked T3` | Mark terminal as blocked |
| `complete` | End session, generate report |

### Resume a Session

```
Terminator, resume session term-20250115-abc123
```

## How It Works

1. **Reconnaissance**: Scans GitHub Issues, local TODOs, git status
2. **Analysis**: Identifies parallelizable tasks and blockers
3. **Distribution**: Proposes optimal task distribution across terminals
4. **Generation**: Creates self-contained prompts for each terminal
5. **Monitoring**: Tracks progress and handles reassignments
6. **Reporting**: Generates session summary with metrics

## Example

```
+===============================================================+
|                    TERMINATOR ANALYSIS                        |
+===============================================================+
| Project: myorg/api-service   Terminals: 3                     |
| Total Bugs: 7                Parallelizable: 5   Blocked: 2   |
+---------------------------------------------------------------+
| PROPOSED DISTRIBUTION:                                        |
|                                                               |
| [T1] Terminal 1: #401 (null pointer), #405 (timeout)          |
| [T2] Terminal 2: #402 (auth bypass)                           |
| [T3] Terminal 3: #403 (race condition), #404 (memory leak)    |
+---------------------------------------------------------------+
| BLOCKERS (need input):                                        |
| - #406: Requires access to prod DB credentials                |
| - #407: Unclear reproduction steps                            |
+===============================================================+
```

## Requirements

- [Claude Code](https://claude.ai/claude-code) CLI
- GitHub CLI (`gh`) installed and authenticated
- Git repository with GitHub Issues

## Prime Directives

1. **Zero Context Bleed**: Each agent receives ONLY information needed for its tasks
2. **GitHub is Truth**: All progress syncs to GitHub
3. **Git Discipline**: 1 Task = 1 Branch = 1 PR
4. **Fail Fast**: Blocked agents STOP and signal immediately
5. **Human Authority**: When ambiguous, ASK

## License

MIT
