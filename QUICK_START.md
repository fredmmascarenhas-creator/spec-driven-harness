# METHODOLOGY Quick Start (5 minutes)

**What is it?** Adaptive software development that routes around problems.

---

## 🎯 When to Use

**Decision Tree:**

```
Is it a code change?
├─ YES → How many files affected?
│  ├─ 1-3 files → SMALL (Spec + Execute)
│  ├─ 4-9 files → MEDIUM (Spec + Execute, skip Design/Tasks)
│  ├─ 10+ files → LARGE (Full: Spec + Design + Tasks + Execute)
│  └─ Unclear? → COMPLEX (Full + UAT + stakeholders)
└─ NO → Just do it (docs, config, etc)
```

---

## 📐 Three Scopes, Three Templates

### SMALL (≤3 files, <2 hours)

**Steps:** Specify → Execute

**Example:** Fix bug

```bash
# 1. Quick spec (2 min)
echo "REQ-BUG-001: Fix login timeout" > .specs/bug-001.md

# 2. Execute (30 min)
# Fix code, commit with REQ-BUG-001 in message

# 3. Update STATE.md (1 min)
# Add to Lessons: "Timeout was cache issue"
```

---

### MEDIUM (<10 tasks, <2 days)

**Steps:** Specify → Execute (skip Design/Tasks)

**Example:** Add feature

```bash
# 1. Write spec (30 min)
cat > .specs/features/auth/spec.md << 'SPEC'
# REQ-AUTH-001: Google OAuth
## Problem: Users want Google login
## Solution: OAuth 2.0 with PKCE
## Success: <2s login time
SPEC

# 2. Execute with sub-agents (4 hours)
# Let agent implement, commit with TASK-AUTH-001

# 3. Update STATE.md (5 min)
# Decision: Why Google over GitHub?
```

---

### LARGE (>10 tasks, >2 days)

**Steps:** Specify → Design → Tasks → Execute

**Example:** Database layer (MedPark Phase 3)

```bash
# 1. Spec (1 hour)
# Write spec.md: Requirements (REQ-SUP-001 to 004)

# 2. Design (2 hours)
# Write design.md: Architecture, components, data flows

# 3. Tasks (1 hour)
# Write tasks.md: 10 atomic tasks, sub-agent strategy

# 4. Execute (8 hours)
# Spawn sub-agents for parallel tasks
# Commit each with TASK-SUP-001, TASK-SUP-002...

# 5. Update STATE.md (30 min)
# Decisions, blockers, lessons, progress
```

---

## 📝 Files You Need

```
project/
├── .specs/
│   ├── project/
│   │   └── STATE.md          ← Sacred file (read first)
│   └── features/
│       └── your-feature/
│           ├── spec.md        ← What to build (always)
│           ├── design.md      ← How to build (LARGE only)
│           └── tasks.md       ← Task breakdown (LARGE only)
└── METHODOLOGY.md            ← Full guide (read once)
```

---

## 🎓 Core Principles

1. **Auto-size:** Right process for right scope (no bureaucracy)
2. **Traceability:** REQ → DESIGN → TASK → COMMIT (always know why)
3. **Adaptability:** Plans fail, harness reroutes (like GPS)
4. **Context isolation:** Sub-agents prevent overflow (fresh 200k)
5. **STATE.md is sacred:** Read at session start, update after decisions

---

## ⚡ Copy-Paste Templates

### STATE.md Template
```markdown
# Project State

**Last Updated:** YYYY-MM-DD HH:MM

## 🎯 Current Focus
Phase X - What you're building now

## ✅ Decisions Made
### D1: Decision name
**Date:** YYYY-MM-DD
**Decision:** What you chose
**Rationale:** Why
**Impact:** Effect on project

## 🚧 Active Blockers
*None or list them*

## 💡 Lessons Learned
### L1: Lesson name
**Lesson:** What went wrong
**Solution:** How you fixed it
**Applied:** Where you used it

## 🔄 Session Handoff
**Current state:** Where you are
**Next task:** What's next
**Context:** Anything important
```

### Commit Message Template
```
TASK-XXX-001: Brief description (imperative)

- Detailed change 1
- Detailed change 2

Files: file1.js, file2.js
Status: What's done/pending
```

---

## ✅ Success Checklist

Before committing:
- [ ] Commit message has TASK-XXX-NNN
- [ ] Files changed match task scope
- [ ] STATE.md updated (if decision made)
- [ ] Tests pass (if they exist)
- [ ] Next task identified

---

## 🚀 Start Now

1. Copy STATE.md template to `.specs/project/STATE.md`
2. Read full METHODOLOGY.md (15 min)
3. Try SMALL scope first (get muscle memory)
4. Graduate to MEDIUM, then LARGE
5. Share your experience (issues/PRs)

---

**That's it!** You're ready. 🎯

**Full docs:** [METHODOLOGY.md](../METHODOLOGY.md)  
**Examples:** [examples/](../examples/)  
**Questions:** Open issue on GitHub
