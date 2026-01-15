# Terminator Agent

**Meta-orchestration agent for parallel multi-terminal development workflows.**

Coordinate work across multiple Claude Code terminal instances, distributing tasks intelligently and generating autonomous, self-contained prompts for each agent.

## Quick Start

```bash
# Start each terminal with:
claude --dangerously-skip-permissions
```

> **Important:** Terminator is designed for frictionless automation. The `--dangerously-skip-permissions` flag prevents agents from stopping to approve every `git commit` or `npm test`.

## Features

- **Wave-based Parallelization** - Group tasks by dependencies, execute in parallel within waves
- **Plan-First Execution** - GSD-style planning before execution (PLAN → EXECUTE → VERIFY)
- **Autonomous Execution** - Agents handle deviations automatically (auto-fix bugs, auto-add deps)
- **Tech Stack Detection** - Auto-detect framework, test runner, build commands
- **GSD Integration** - Works with [Get Shit Done](https://github.com/glittercowboy/get-shit-done) workflow system
- **Multi-Terminal Coordination** - File locking, status broadcasting, cross-agent communication
- **GitHub Sync** - Auto-update issues, create PRs, manage labels
- **Compound Learning** - Patterns discovered in sessions improve future sessions

## Operating Modes

| Mode | Description | When to Use |
|------|-------------|-------------|
| `standard` | Generate prompts, user copies to terminals | Default - manual control |
| `autonomous` | Agents self-execute with deviation handling | "Terminator, autonomous mode" |
| `gsd-integrated` | Use GSD workflow if installed | Projects with `.planning/` |

## Installation

Copy the agent and command files to your Claude Code configuration:

```bash
# Clone this repo
git clone https://github.com/Julianb233/terminator-agent.git

# Copy agent file
cp terminator-agent/agents/terminator.md ~/.claude/agents/

# Copy command file
cp terminator-agent/commands/terminator.md ~/.claude/commands/

# Create state directory
mkdir -p ~/.terminator
```

## Usage

```bash
# Standard mode
/terminator
/terminator activate myorg/api-service

# Autonomous mode
/terminator autonomous myorg/backend

# Resume session
/terminator resume term-20250115-abc123

# Check status
/terminator status
```

### Natural Language

- "Terminator, activate for {project}"
- "Terminator, autonomous mode for {project} - 4 terminals, features"
- "Terminator, status"
- "Terminator, resume session {id}"

## Workflow Phases

### Phase 1: Activation
Gather project, terminal count, focus area (bugs/features/tech debt), and mode.

### Phase 2: Reconnaissance
- Scan GitHub issues
- Check local TODO.md, ROADMAP.md
- Analyze git status and open PRs
- **Detect tech stack** (framework, test runner, build commands)
- Detect GSD installation

### Phase 2.5: Planning (GSD-Style)
Create explicit mini-plans for each task:
```markdown
## Task Plan: #101 - Fix auth null pointer

### Objective
Handle null user in auth middleware

### Files to Modify
- `src/auth/middleware.ts` - Add null check

### Implementation Steps
1. Read existing middleware code
2. Add null check before accessing user properties
3. Run tests: `npm test`

### Verification
- [ ] `npm test` passes
- [ ] `npm run lint` passes
```

### Phase 3: Wave Analysis
Group tasks into waves based on dependencies:

```
+===============================================================+
|                    TERMINATOR ANALYSIS                        |
+===============================================================+
| Project: myorg/api       Terminals: 4       Mode: autonomous  |
| Total Tasks: 8           Waves: 2           Blocked: 1        |
+---------------------------------------------------------------+
| WAVE 1 (Parallel - No Dependencies):                          |
| [T1] #101 auth-fix       [T2] #102 api-endpoint               |
| [T3] #103 ui-component   [T4] #104 database-index             |
+---------------------------------------------------------------+
| WAVE 2 (Depends on Wave 1):                                   |
| [T1] #105 integration-test (needs #101, #102)                 |
| [T2] #106 e2e-flow (needs #103)                               |
+===============================================================+
```

### Phase 4: State Initialization
- Create `~/.terminator/state.json`
- Store to Claude Flow memory
- Create GSD-compatible STATE.md

### Phase 5: Autonomous Agent Prompts
Generate copy-paste ready prompts for each terminal with:
- **Tech stack info** (test/build/lint commands)
- **Task plans** with specific steps
- Deviation handling rules
- Atomic commit protocol
- File locking coordination

### Phase 6-9: Execution, Verification, GitHub Sync, Completion

## Agent Execution Protocol

Each agent follows **PLAN → EXECUTE → VERIFY**:

1. **PLAN:** Read the plan, understand the objective
2. **READ:** Read relevant files first - understand before changing
3. **EXECUTE:** Implement the fix/feature following the plan
4. **VERIFY:** Run verification: `{test_command}`
5. **COMMIT:** Atomic commit with proper message format

## Deviation Handling

In autonomous mode, agents handle discoveries automatically:

| Discovery | Action | Document |
|-----------|--------|----------|
| Bug found | Fix immediately | Add to DEVIATIONS |
| Missing import/dep | Add it | Note in commit |
| Test failing | Fix the test OR code | Add to DEVIATIONS |
| Security issue | Fix immediately | Flag as critical |
| Scope creep | STOP | Add to BLOCKED |
| Architectural change | STOP | Flag for human |

## GSD Integration

If project has GSD installed (`.planning/` exists):

1. Use GSD for planning: `/gsd:plan-phase {N}`
2. Use Terminator for parallel execution
3. Each terminal executes one GSD plan
4. Terminator coordinates wave execution
5. Use GSD verification: `/gsd:verify-work`

## Prime Directives

1. **Zero Context Bleed** - Each agent receives ONLY information needed for its tasks
2. **GitHub is Truth** - All progress syncs to GitHub
3. **Git Discipline** - 1 Task = 1 Branch = 1 PR
4. **Autonomous by Default** - Handle deviations automatically
5. **Fail Fast** - Blocked agents STOP and signal
6. **Wave Integrity** - Complete all tasks in wave before next
7. **Compound Learning** - Every session improves future sessions

## Requirements

- Claude Code CLI with `--dangerously-skip-permissions`
- GitHub CLI (`gh`) authenticated
- Claude Flow memory system (optional, for cross-terminal coordination)
- GSD (optional, for integrated planning)

## State Persistence

- Local: `~/.terminator/state.json`
- Memory: Claude Flow `terminator` namespace
- Archives: `~/.terminator/archive/`

## License

MIT
