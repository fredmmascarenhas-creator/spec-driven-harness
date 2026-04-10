# Example: SMALL Scope (Quick Mode)

**Task:** "Fix typo in installation command"

**Scope:** SMALL (1 file, one-sentence)

---

## Process

### No Formal Spec Needed

Just describe → implement → verify → commit

---

## Execution

```bash
# 1. Describe (1 line)
"Fix typo: 'npm instal' → 'npm install' in README.md"

# 2. Implement (edit file)
vim README.md
# Change line 23: npm instal → npm install

# 3. Verify
# Preview: typo fixed ✓

# 4. Commit
git add README.md
git commit -m "Quick: Fix typo in installation command

- Fixed: npm instal → npm install
- File: README.md line 23"

# Done! (2 minutes total)
```

---

## Why Quick Mode?

- ≤3 files
- Obvious change
- No architectural impact
- Overhead of full spec > value

---

## Anti-Pattern

**❌ DON'T do this:**

```bash
mkdir .specs/features/fix-typo/
cat > .specs/features/fix-typo/spec.md << EOF
# Feature: Fix Typo
REQ-TYP-001: Change 'instal' to 'install'
Acceptance: Typo is fixed
EOF
# ... create design.md, tasks.md ...
```

**This is bureaucracy theater.** Just fix it.

---

## When to Upgrade from SMALL

If during "Implement" you discover:
- More than 3 files need changes
- Edge cases you didn't anticipate
- Tests are failing unexpectedly

→ STOP, create formal spec.md, reassess as MEDIUM

---

**Time:** 2 minutes  
**Ceremony:** Zero  
**Value:** Fix deployed
