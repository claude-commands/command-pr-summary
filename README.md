# command-pr-summary

A Claude Code slash command for generating PR descriptions from branch changes.

## Installation

```bash
# Clone to your preferred location
git clone git@github.com:claude-commands/command-pr-summary.git <clone-path>/command-pr-summary

# Symlink (use full path to cloned repo)
ln -s <clone-path>/command-pr-summary/pr-summary.md ~/.claude/commands/pr-summary.md
```

## Usage

```text
/pr-summary           # Compare to main/dev (auto-detected)
/pr-summary main      # Compare to main
/pr-summary dev       # Compare to dev
/pr-summary release/v2  # Compare to release branch
```

## What it does

1. Analyzes all commits in current branch
2. Reviews file changes and full diff
3. Groups changes by type (features, fixes, etc.)
4. Identifies breaking changes and impacts
5. Generates structured PR description with test plan

## Output Format

```markdown
## Summary

Added user authentication with JWT tokens and rate limiting.

## Changes

### Features
- Added user authentication flow
- Implemented rate limiting for API endpoints

### Bug Fixes
- Fixed memory leak in connection pool (#123)

## Breaking Changes

- `getUserById()` now returns `null` instead of throwing

## Files Changed

| Type | Files |
|------|-------|
| Added | 3 |
| Modified | 8 |

## Test Plan

- [ ] Unit tests pass (`npm test`)
- [ ] Manual testing of login flow

## Related Issues

Closes #123, #456
```

## Features

- **Smart grouping**: Changes grouped by type (feat, fix, refactor)
- **Breaking change detection**: Highlights API/signature changes
- **Issue linking**: Extracts `#123`, `fixes #456` references
- **Test plan generation**: Suggests testing based on changes
- **File summary**: Shows what was added/modified/deleted

## Requirements

- Git repository with commits
- Current branch different from base branch
- Claude Code with Sonnet model access

## Updates

```bash
cd <clone-path>/command-pr-summary && git pull
```
