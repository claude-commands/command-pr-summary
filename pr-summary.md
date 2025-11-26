---
argument-hint: "[base-branch]"
description: "Generate PR description from branch changes"
model: claude-opus-4-5-20251101
allowed-tools: ["Bash", "Read", "Glob", "Grep"]
---

**If `$ARGUMENTS` is empty or not provided:**

Generate a PR description comparing current branch to the default base (main or dev).

**Usage:** `/pr-summary [base-branch]`

**Examples:**

- `/pr-summary` - Compare to main/dev (auto-detected)
- `/pr-summary main` - Compare to main
- `/pr-summary dev` - Compare to dev
- `/pr-summary release/v2` - Compare to release branch

**Workflow:**

1. Analyze all commits in current branch
2. Review file changes and diff
3. Identify breaking changes and impacts
4. Generate structured PR description
5. Include test plan suggestions

Proceed with auto-detected base branch.

---

**If `$ARGUMENTS` is provided:**

Generate a comprehensive PR description comparing the current branch to the specified base branch.

## Configuration

- **Base Branch**: `$ARGUMENTS` (e.g., main, dev, release/v2)
- **Current Branch**: Auto-detected via `git branch --show-current`

## Steps

1. **Gather Branch Information**

   ```bash
   # Current branch
   git branch --show-current

   # Commits in this branch not in base
   git log $ARGUMENTS..HEAD --oneline

   # Files changed
   git diff --stat $ARGUMENTS..HEAD

   # Full diff for analysis
   git diff $ARGUMENTS..HEAD
   ```

2. **Analyze Commits**

   For each commit, extract:
   - Commit message (summary and body)
   - Type (feat, fix, refactor, etc.)
   - Scope if present
   - Breaking change indicators

   Group by type:
   - Features (new functionality)
   - Bug Fixes
   - Refactoring
   - Documentation
   - Tests
   - Chores

3. **Analyze Code Changes**

   Review the diff to identify:
   - New files created
   - Files modified significantly
   - Files deleted
   - API changes (new endpoints, changed signatures)
   - Database/schema changes
   - Configuration changes
   - Dependency updates

4. **Detect Breaking Changes**

   Look for:
   - Removed or renamed public functions/methods
   - Changed function signatures
   - Removed API endpoints
   - Database migrations
   - Environment variable changes
   - Commits with `BREAKING CHANGE:` footer

5. **Identify Impact Areas**

   Determine what parts of the system are affected:
   - Frontend / Backend / Full-stack
   - Which modules or services
   - User-facing vs internal
   - Performance implications

6. **Generate PR Description**

   Format:

   ```markdown
   ## Summary

   [2-3 sentence overview of what this PR does and why]

   ## Changes

   ### Features
   - Added user authentication flow
   - Implemented rate limiting for API endpoints

   ### Bug Fixes
   - Fixed memory leak in connection pool (#123)
   - Resolved race condition in cache invalidation

   ### Refactoring
   - Extracted validation logic into shared utilities
   - Improved error handling consistency

   ## Breaking Changes

   - `getUserById()` now returns `null` instead of throwing for missing users
   - Removed deprecated `/api/v1/legacy` endpoint

   ## Files Changed

   | Type | Files |
   |------|-------|
   | Added | 3 |
   | Modified | 8 |
   | Deleted | 1 |

   **Key files:**
   - `src/auth/login.ts` - New authentication handler
   - `src/api/users.ts` - Updated user endpoints
   - `src/utils/validation.ts` - Extracted validators

   ## Test Plan

   - [ ] Unit tests pass (`npm test`)
   - [ ] Manual testing of login flow
   - [ ] Verify API responses match documentation
   - [ ] Check for regressions in user dashboard

   ## Screenshots

   [If UI changes, note where screenshots should go]

   ## Related Issues

   Closes #123, #456
   Related to #789
   ```

7. **Extract Issue References**

   Search commits and branch name for:
   - `#123` - Issue numbers
   - `fixes #123`, `closes #456`
   - Branch naming patterns like `feat/123-description`

8. **Suggest Test Plan**

   Based on changes, recommend:
   - Which tests to run
   - Manual testing steps
   - Areas requiring extra attention
   - Potential regression areas

## Output Guidelines

- Keep summary concise (2-3 sentences max)
- Group changes logically by type
- Highlight breaking changes prominently
- Link to related issues when found
- Include realistic test plan items
- Avoid repeating every commit message verbatim
- Focus on the "what" and "why", not the "how"

## Notes

- This generates a description for the PR, not creates the PR itself
- Review and customize before submitting
- Add screenshots manually for UI changes
- Verify issue references are correct
- Consider adding deployment notes if relevant
