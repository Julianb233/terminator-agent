# Terminator - Multi-Terminal Orchestration

Coordinate parallel development workflows across multiple terminal instances with autonomous execution.

## IMPORTANT: Terminal Setup

**Each terminal MUST be started with:**
```bash
claude --dangerously-skip-permissions
```

This enables frictionless automation.

## Arguments
$ARGUMENTS - Optional: "activate {project}", "autonomous {project}", "resume {session_id}", "status"

## Instructions

### If $ARGUMENTS starts with "activate" or is empty:

**Activate Terminator mode (Standard):**

1. **Identify Project:**
   - If in a git repo, use current repo: `git remote get-url origin`
   - If $ARGUMENTS includes project name, use that
   - Otherwise, ask: "Which project/repository?"

2. **Gather Terminal Count:**
   - Ask: "How many terminals do you have open? (e.g., 3)"

3. **Determine Focus:**
   - Ask: "What's your priority focus?"
   - Options: bugs, features, tech debt, balanced

4. **Launch Reconnaissance:**
   - Fetch GitHub issues: `gh issue list --state open --json number,title,labels,body`
   - Check local tasks: `TODO.md`, `ROADMAP.md`, `.planning/STATE.md`
   - Check git status: branches, PRs, uncommitted changes
   - **Detect tech stack:** package.json, pyproject.toml, Cargo.toml
   - Analyze dependencies for wave-based parallelization

5. **Create Task Plans (GSD-Style):**
   - For each task, create a mini-plan:
     - Objective
     - Files to modify
     - Implementation steps
     - Verification command
     - Acceptance criteria

6. **Present Wave Analysis:**
   - Show task distribution by wave (dependencies respected)
   - Include tech stack info
   - Identify blockers requiring input
   - Wait for user confirmation

7. **Initialize State:**
   - Create `~/.terminator/state.json`
   - Store to Claude Flow memory namespace
   - Create local STATE.md

8. **Generate Agent Prompts:**
   - Create self-contained prompt for each terminal
   - Include: tech stack, task plans, branch naming, commit format, deviation handling
   - **Add reminder:** `claude --dangerously-skip-permissions`
   - Present as copy-paste ready blocks

### If $ARGUMENTS starts with "autonomous":

**Activate Terminator mode (Autonomous):**

Same as standard mode, but agent prompts include:
- Full deviation handling rules (auto-fix, auto-add, escalation)
- PLAN → EXECUTE → VERIFY workflow
- Atomic commit protocol
- File locking coordination
- Status reporting to memory namespace

### If $ARGUMENTS starts with "resume":

1. Retrieve session from memory:
   ```javascript
   mcp__claude-flow__memory_usage({
     action: "retrieve",
     namespace: "terminator",
     key: "session-active"
   })
   ```

2. Or read local state:
   ```bash
   cat ~/.terminator/state.json
   ```

3. Check agent statuses:
   ```javascript
   mcp__claude-flow__memory_usage({
     action: "retrieve",
     namespace: "terminator",
     key: "agent-{N}-status"
   })
   ```

4. Show current status and pending tasks
5. Allow reassignment or continuation

### If $ARGUMENTS is "status":

1. Retrieve current session state
2. Show progress per terminal:
   - Completed tasks
   - In-progress tasks
   - Blocked items
   - Deviations logged
3. Show wave progress
4. Show overall metrics

## Monitoring Commands

During active session, user can say:

| Command | Action |
|---------|--------|
| `status` | Show all terminal progress |
| `wave` | Show current wave status |
| `reassign #{task} -> T{n}` | Move task to different terminal |
| `unblock T{n}` | Provide info to unblock agent |
| `add #{issue}` | Add task to queue |
| `blocked T{n}` | Mark T{n} blocked, suggest redistribution |
| `skip #{issue}` | Skip task, move to queue |
| `verify` | Run verification on completed work |
| `complete` | End session, generate report |

## GSD Integration

If project has `.planning/` directory (GSD installed):
- Offer GSD integration mode
- Distribute GSD plans across terminals
- Use wave-based execution for plan dependencies
- Run GSD verification after each wave

## Examples

```
/terminator
/terminator activate myorg/api-service
/terminator autonomous myorg/backend
/terminator resume term-20250115-abc123
/terminator status
```

## Natural Language

- "Terminator, activate for {project}"
- "Terminator, autonomous mode for {project}"
- "Terminator, status"
- "Terminator, resume session {id}"

## Quick Reference

- **Invoke:** `/terminator` or "Terminator, activate for [project]"
- **Agent:** terminator
- **Registry:** Tier 1.6
- **State:** `~/.terminator/state.json` + Claude Flow memory
- **Modes:** standard, autonomous, gsd-integrated
- **Terminal:** `claude --dangerously-skip-permissions`
