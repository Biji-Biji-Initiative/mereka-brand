# Agent Instructions - BBBI Mereka Brand Assets

## Workflow Rules

1. **Read CLAUDE.md first** - Understand asset organization
2. **Maintain structure** - Follow existing directory conventions
3. **Update guides** - Keep MD/YAML/JSON in sync with PDF
4. **URL-encode spaces** - Legacy filenames may contain spaces
5. **Commit work** - Push changes before ending session

## Session End Checklist

```bash
git status && git add -A && git commit -m "..."
git push
```

## Common Tasks

| Task | Location |
|------|----------|
| Add new logo | `brands/{brand}/logos/{png,svg}/` |
| Update colors | `brands/{brand}/colors/` |
| Edit brand guide | `brands/{brand}/guides/` |
| Add favicon sizes | `brands/{brand}/icons/favicon/` |

## Landing the Plane (Session Completion)

**When ending a work session**, you MUST complete ALL steps below. Work is NOT complete until `git push` succeeds.

**MANDATORY WORKFLOW:**

1. **File issues for remaining work** - Create issues for anything that needs follow-up
2. **Run quality gates** (if code changed) - Tests, linters, builds
3. **Update issue status** - Close finished work, update in-progress items
4. **PUSH TO REMOTE** - This is MANDATORY:
   ```bash
   git pull --rebase
   bd sync
   git push
   git status  # MUST show "up to date with origin"
   ```
5. **Clean up** - Clear stashes, prune remote branches
6. **Verify** - All changes committed AND pushed
7. **Hand off** - Provide context for next session

**CRITICAL RULES:**
- Work is NOT complete until `git push` succeeds
- NEVER stop before pushing - that leaves work stranded locally
- NEVER say "ready to push when you are" - YOU must push
- If push fails, resolve and retry until it succeeds
