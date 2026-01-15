# Terminator - Multi-Terminal Orchestration

Coordinate parallel development workflows across multiple terminal instances.

## Arguments
$ARGUMENTS - Optional: "activate {project}", "resume {session_id}", "status"

## Instructions

### If $ARGUMENTS starts with "activate" or is empty:

**Activate Terminator mode and gather inputs:**

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
   - Fetch GitHub issues: `gh issue list --state open --json number,title,labels`
   - Check local tasks: `TODO.md`, `ROADMAP.md`, inline TODOs
   - Check git status: branches, PRs, uncommitted changes
   - Analyze dependencies for parallelization

5. **Present Analysis:**
   - Show task distribution visualization
   - Identify blockers requiring input
   - Wait for user confirmation

6. **Generate Agent Prompts:**
   - Create self-contained prompt for each terminal
   - Include: tasks, branch naming, commit format, completion protocol
   - Present as copy-paste ready blocks

### If $ARGUMENTS starts with "resume":

1. Retrieve session from memory:
   ```javascript
   mcp__claude-flow__memory_usage({
     action: "retrieve",
     namespace: "coordination",
     key: "terminator-session"
   })
   ```

2. Or read local state:
   ```bash
   cat ~/.terminator/state.json
   ```

3. Show current status and pending tasks
4. Allow reassignment or continuation

### If $ARGUMENTS is "status":

1. Retrieve current session state
2. Show progress per terminal:
   - Completed tasks
   - In-progress tasks
   - Blocked items
3. Show overall metrics

## Monitoring Commands

During active session, user can say:

| Command | Action |
|---------|--------|
| `status` | Show all terminal progress |
| `reassign #123 -> T2` | Move task to Terminal 2 |
| `add "new task"` | Add to queue |
| `blocked T3` | Mark T3 blocked, suggest redistribution |
| `complete` | End session, generate report |

## Examples

```
/terminator
/terminator activate myorg/api-service
/terminator resume term-20250115-abc123
/terminator status
```

## Quick Reference

- **Invoke:** `/terminator` or "Terminator, activate for [project]"
- **Agent:** terminator
- **Registry:** Tier 1.6
- **State:** `~/.terminator/state.json` + Claude Flow memory
