# Spec-Driven + Harness Engineering

**A methodology for adaptive software development that actually works.**

Not Agile. Not Waterfall. **Spec + Harness.**

---

## 🎯 What Is This?

A development methodology that combines:

1. **Spec-Driven Development** - Clear contracts, traceable requirements
2. **Harness Engineering** - Adaptive runtime execution (not fixed pipelines)
3. **Auto-Sizing** - Complexity determines process depth
4. **Sub-Agent Architecture** - Context isolation & parallel execution

**Origin:**
- Inspired by [Tech Lead's Club - tlc-spec-driven](https://agent-skills.techleads.club/skills/tlc-spec-driven/)
- Extended with [Harness Engineering concepts](https://youtu.be/dLs-Pbn8stU) (Waldemar Neto)
- Battle-tested on healthcare AI at scale (MedPark, OncoPlan)

---

## 🚀 Quick Start

### 1. Copy METHODOLOGY.md to your project

```bash
curl -O https://raw.githubusercontent.com/fredmmascarenhas-creator/spec-driven-harness/main/METHODOLOGY.md
```

### 2. Create .specs/ structure

```bash
mkdir -p .specs/{project,features,quick}
```

### 3. Initialize STATE.md

```bash
curl -O https://raw.githubusercontent.com/fredmmascarenhas-creator/spec-driven-harness/main/templates/STATE_TEMPLATE.md
cp STATE_TEMPLATE.md .specs/project/STATE.md
# Edit STATE.md with your project details
```

### 4. Start building

```bash
# Assess scope
# SMALL? → Quick mode (Specify + Execute)
# MEDIUM? → Skip Design/Tasks
# LARGE? → Full pipeline
# COMPLEX? → Full + UAT

# Read METHODOLOGY.md for details
```

---

## 📐 The Framework

### Spec = Contract. Harness = Adaptive Engine.

```
┌──────────┐     ┌──────────┐     ┌─────────┐     ┌─────────┐
│ SPECIFY  │ ──→ │  DESIGN  │ ──→ │  TASKS  │ ──→ │ EXECUTE │
└──────────┘     └────┬─────┘     └─────────┘     └────┬────┘
  Required            │                                  │
                      └────── FEEDBACK LOOP ─────────────┘
                      (Harness adapts plan based on reality)
```

**Traditional Spec-Driven (fails):**
```
spec → design → tasks → execute → ERROR → STOP ❌
```

**Harness Engineering (adapts):**
```
spec → design → tasks → execute
         ↑                ↓
         └──── ERROR ─────┘
               ↓
         BACKTRACK + ADAPT + RETRY ✅
```

---

## 🎚️ Auto-Sizing Rule

> **"Complexity determines depth, not a fixed pipeline."**

| Scope | Specify | Design | Tasks | Execute |
|-------|---------|--------|-------|---------|
| **SMALL** (≤3 files) | Quick | ❌ Skip | ❌ Skip | ✅ Do |
| **MEDIUM** (<10 tasks) | Brief | ❌ Skip | ❌ Implicit | ✅ Do + verify |
| **LARGE** (>10 tasks) | Full | ✅ Do | ✅ Full | ✅ Do + verify |
| **COMPLEX** (ambiguous) | Full + discuss | ✅ Research | ✅ Parallel | ✅ Do + UAT |

**Example:**
- "Fix typo in README" → **SMALL** → Quick mode (1 min)
- "Add user authentication" → **MEDIUM** → Specify + Execute (2 hours)
- "Refactor database layer" → **LARGE** → Full pipeline (2 days)
- "Build AI agent system" → **COMPLEX** → Full + research (2 weeks)

---

## 📁 Project Structure

```
project/
├── METHODOLOGY.md          # This methodology (copy from repo)
├── .specs/
│   ├── project/
│   │   ├── PROJECT.md     # Vision, goals
│   │   ├── ROADMAP.md     # Features, milestones
│   │   └── STATE.md       # ⭐ Decisions, blockers, todos
│   ├── features/
│   │   └── [feature]/
│   │       ├── spec.md    # Requirements with REQ-IDs
│   │       ├── design.md  # Architecture (LARGE/COMPLEX)
│   │       └── tasks.md   # Atomic tasks (LARGE/COMPLEX)
│   └── quick/
│       └── NNN-slug/
│           └── TASK.md
├── src/                   # Implementation
└── tests/                 # Test suite
```

---

## 🤖 Sub-Agent Architecture

**Problem:** Long sessions accumulate context → degradation

**Solution:** Spawn isolated sub-agents for heavy work

### When to Delegate

| Activity | Delegate? | Why |
|----------|-----------|-----|
| Research | ✅ YES | Large output, only summary matters |
| Implementing tasks | ✅ YES | File edits consume context |
| Parallel tasks | ✅ YES | Only way to truly parallelize |
| Planning | ❌ NO | Needs full context |
| Quick fixes | ❌ NO | Overhead > benefit |

**Example:**
```javascript
// Traditional (context overflow)
task1 → accumulate → task2 → accumulate → OVERFLOW ❌

// Harness (isolation)
main_context (clean)
    ├─ spawn sub-agent(task1) → result
    └─ spawn sub-agent(task2) → result ✅
```

---

## 📋 Requirement Traceability

**Every requirement gets an ID:**

```
REQ-[COMPONENT]-[NUMBER]

Examples:
- REQ-AUTH-001: User login
- REQ-DB-001: PostgreSQL connection
- REQ-API-001: REST endpoints

Traceability chain:
REQ-AUTH-001 (spec.md)
  → DESIGN-AUTH-001 (design.md)
    → TASK-AUTH-001 (tasks.md)
      → IMPL-AUTH-001 (src/auth.js)
        → TEST-AUTH-001 (tests/auth.test.js)
          → COMMIT-AUTH-001 (git abc123)
```

**Git commits:**
```bash
git commit -m "TASK-AUTH-001: Implement user login

- Implements REQ-AUTH-001
- Gate: npm test auth (✅ PASS)
- Files: src/auth.js, tests/auth.test.js"
```

---

## 🧪 Testing Gates

**Every task defines a gate check:**

```markdown
## T1: Create Database Client
**Gate:** `npm test db/client`
**Pass criteria:** Connection test returns 200 OK
```

No gate passes → task not done.

---

## 📊 STATE.md (Sacred)

**The single source of truth for project state.**

Required sections:
1. **Current Focus** - What phase/status
2. **Decisions Made** - Why you chose X over Y
3. **Active Blockers** - What's stuck
4. **Todos** - Prioritized (P0-P3)
5. **Lessons Learned** - What failed, what worked
6. **Deferred Ideas** - Good ideas, wrong time
7. **Session Handoff** - Resume context

**Read at start of every session. Update after decisions.**

---

## 🎯 Who This Is For

### ✅ You Should Use This If:

- Building complex software (not trivial CRUD apps)
- Working with AI coding agents
- Need to pause/resume work across sessions
- Want decisions documented, not lost
- Hate "where was I?" syndrome
- Need traceability (compliance, audits)
- Work solo or small team (<10)

### ❌ This Is Overkill If:

- Building simple landing pages
- One-day hackathon projects
- Throwaway prototypes
- Large enterprise teams (use SAFe/Scrum)

---

## 📚 Real-World Examples

### MedPark (Healthcare AI)

**Scope:** COMPLEX (new domain, ambiguous requirements)

**Process:**
1. **Specify** → Full spec + discussed gray areas (Cochrane auth, rate limits)
2. **Design** → Research scraping strategies, architecture diagrams
3. **Tasks** → 24 specialists, parallel plan with [P] markers
4. **Execute** → Sub-agents per specialist, atomic commits, UAT with doctors

**Result:** 70% cost reduction, maintained quality, 100% traceable

### OncoPlan V2 (Cancer Management)

**Scope:** LARGE (19 agents, 50 tables)

**Process:**
1. **Specify** → Requirements for each agent
2. **Design** → Multi-agent architecture
3. **Tasks** → Breakdown with dependencies
4. **Execute** → Sub-agents + quality grading

**Blocker:** 13 agents stopped → documented in STATE.md → debug plan created

---

## 🛠️ Templates

All templates are in `/templates/`:

- `STATE_TEMPLATE.md` - Initialize STATE.md
- `spec_template.md` - Feature specification
- `design_template.md` - Architecture doc
- `tasks_template.md` - Task breakdown

---

## 📖 Full Documentation

Read **METHODOLOGY.md** (12 KB) for:
- Complete auto-sizing decision tree
- Sub-agent delegation rules
- Knowledge verification chain
- Anti-patterns to avoid
- Success metrics
- Enforcement guidelines

---

## 🤝 Contributing

This methodology evolves based on real-world use.

**Submit issues for:**
- ❌ Anti-patterns you discovered
- ✅ Success patterns that worked
- 📝 Template improvements
- 🎓 Lessons learned

**Don't submit:**
- Philosophical debates (this is practical, not academic)
- "But we use [framework]..." (use yours, this is different)

---

## 📄 License

MIT License - Use freely, commercially or personally.

**Attribution appreciated but not required.**

If this saves you time, star the repo ⭐

---

## 🔗 Links

- **Full Methodology:** [METHODOLOGY.md](METHODOLOGY.md)
- **Templates:** [/templates](templates/)
- **Examples:** [/examples](examples/)
- **Origin (TLC):** https://agent-skills.techleads.club/skills/tlc-spec-driven/
- **Harness Video:** https://youtu.be/dLs-Pbn8stU

---

## 💬 Questions?

**"Is this Agile?"**  
No. Agile is iterative sprints. This is adaptive specifications.

**"Is this Waterfall?"**  
No. Waterfall is fixed phases. Harness adapts at runtime.

**"Do I need AI agents to use this?"**  
No, but it's optimized for agent-assisted development.

**"Can I modify the methodology?"**  
Yes! Fork it, adapt it, make it yours. Just keep the core principles.

**"What if my task is between MEDIUM and LARGE?"**  
Start MEDIUM. If Execute reveals >5 steps, STOP and create formal tasks.md.

---

## ⭐ Star History

If this methodology helps your projects, give it a star!

---

**Created by:** Dr. Frederico Mascarenhas ([@fredmmascarenhas-creator](https://github.com/fredmmascarenhas-creator))  
**Applied to:** MedPark, OncoPlan, Patient Bots, and more healthcare AI  
**Status:** Battle-tested in production  
**Version:** 1.0
