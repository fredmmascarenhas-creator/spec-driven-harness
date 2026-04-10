# METHODOLOGY Git Hooks

Automatic validation hooks to enforce METHODOLOGY compliance.

## Installation

### Per-project setup
```bash
# Copy to your project
cp git-hooks/pre-commit .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

### Global setup (all repos)
```bash
# Set global hooks directory
git config --global core.hooksPath /path/to/spec-driven-harness/git-hooks

# Or create symlinks
ln -s /path/to/spec-driven-harness/git-hooks/pre-commit ~/.git-templates/hooks/pre-commit
```

## What It Checks

### 1. Commit Message Traceability ❌ BLOCKING
Ensures every commit references a task/requirement.

**Format:** `TASK-XXX-001: Brief description`

**Valid prefixes:**
- `TASK-XXX-NNN:` - Implementation task
- `REQ-XXX-NNN:` - Requirement
- `DESIGN-XXX-NNN:` - Design decision
- `FIX-XXX-NNN:` - Bug fix
- `IMPL-XXX-NNN:` - Implementation detail
- `TEST-XXX-NNN:` - Test case

**Examples:**
```
✓ TASK-SUP-006: Integrate KnowledgeBase with Model Router
✓ REQ-AUTH-001: Add Google OAuth support
✓ FIX-BUG-123: Fix login timeout issue
✗ Add OAuth support (no traceability)
✗ WIP (not descriptive)
```

### 2. STATE.md Update Reminder ⚠️ WARNING
Warns if code changed but STATE.md wasn't updated.

**Why:** Decisions should be documented.

**When to update STATE.md:**
- You made an architectural decision
- You encountered a blocker
- You learned something important
- Scope changed (MEDIUM → LARGE)

**To skip:** `git commit --no-verify` (use sparingly)

### 3. Orphan Requirement Detection ⚠️ WARNING
Warns about REQ-XXX references without specs.

**Example:**
```javascript
// File: src/auth.js
// REQ-AUTH-001: Implement OAuth
```

Hook searches `.specs/` for `REQ-AUTH-001`. If not found:
```
⚠️  WARNING - Orphan requirement: REQ-AUTH-001 in src/auth.js
  No spec found in .specs/ directory
  Consider creating .specs/features/REQ-AUTH-001/spec.md
```

## Bypassing Hooks

### Single commit
```bash
git commit --no-verify -m "Emergency fix"
```

### Disable globally
```bash
git config --global core.hooksPath ""
```

## Testing

```bash
# Test pre-commit hook
./git-hooks/pre-commit

# Test with staged changes
echo "// REQ-TEST-001" > test.js
git add test.js
git commit -m "Test commit"
# Should fail (no traceability)

git commit -m "TASK-TEST-001: Test commit"
# Should warn (no STATE.md update)
```

## Customization

Edit `pre-commit` to:
- Change traceability pattern (`grep -qE "^(TASK|REQ)..."`)
- Make STATE.md check blocking (`exit 1` instead of warning)
- Add custom checks (code style, tests, etc)

## Troubleshooting

**Hook not running?**
```bash
# Check if executable
ls -l .git/hooks/pre-commit
# Should show: -rwxr-xr-x

# Make executable
chmod +x .git/hooks/pre-commit
```

**Want to see what's checked?**
```bash
# Run manually
.git/hooks/pre-commit
```

**Conflicts with other hooks?**
```bash
# Merge with existing pre-commit
cat .git/hooks/pre-commit >> old-pre-commit
mv old-pre-commit .git/hooks/pre-commit
```

## CI/CD Integration

### GitHub Actions
```yaml
- name: Validate METHODOLOGY compliance
  run: |
    # Check all commits in PR
    for commit in $(git log origin/main..HEAD --format=%H); do
      msg=$(git log -1 --format=%s $commit)
      if ! echo "$msg" | grep -qE "^(TASK|REQ|FIX)-"; then
        echo "❌ Commit $commit missing traceability: $msg"
        exit 1
      fi
    done
```

### GitLab CI
```yaml
methodology-check:
  script:
    - ./git-hooks/pre-commit
  only:
    - merge_requests
```

## License

MIT (same as METHODOLOGY)
