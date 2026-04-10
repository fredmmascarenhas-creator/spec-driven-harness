# MedPark Development Methodology

**Official Standard** | **Version 1.0** | **Mandatory for all development**

---

## 🎯 CORE PRINCIPLES

MedPark development follows **Spec-Driven + Harness Engineering**, a hybrid methodology that combines:

1. **Spec-Driven Development** (TLC) - Clear contracts, traceable requirements
2. **Harness Engineering** - Adaptive runtime execution, not fixed pipelines
3. **Auto-Sizing** - Complexity determines process depth
4. **Sub-Agent Architecture** - Context isolation & parallel execution

**Origin:**
- Tech Lead's Club - tlc-spec-driven skill
- Waldemar Neto - "Spec Driven chegou no limite — Harness Engineering é o próximo passo"
- Applied to healthcare AI on 2026-04-10

---

## 📐 THE FRAMEWORK

### **Spec = Contract. Harness = Adaptive Engine.**

```
┌──────────┐     ┌──────────┐     ┌─────────┐     ┌─────────┐
│ SPECIFY  │ ──→ │  DESIGN  │ ──→ │  TASKS  │ ──→ │ EXECUTE │
└──────────┘     └────┬─────┘     └─────────┘     └────┬────┘
  Required            │                                  │
                      └────── FEEDBACK LOOP ─────────────┘
                      (Harness adapts plan based on reality)
```

**Not a waterfall. Not agile. It's adaptive specification.**

---

## 🎚️ AUTO-SIZING RULE

> **"Complexity determines depth, not a fixed pipeline."**

| Scope | Description | Specify | Design | Tasks | Execute |
|-------|-------------|---------|--------|-------|---------|
| **SMALL** | ≤3 files, one-sentence | Quick | ❌ Skip | ❌ Skip | ✅ Do |
| **MEDIUM** | Clear feature, <10 tasks | Brief | ❌ Skip | ❌ Implicit | ✅ Do + verify |
| **LARGE** | Multi-component, >10 tasks | Full | ✅ Do | ✅ Full | ✅ Do + verify |
| **COMPLEX** | Ambiguous, new domain | Full + discuss | ✅ Research | ✅ Parallel | ✅ Do + UAT |

### **Decision Tree:**

```
New feature/task arrives
↓
How many files affected?
├─ ≤3 files → SMALL (quick mode)
├─ 4-10 files, clear requirements → MEDIUM
├─ >10 files OR multi-component → LARGE
└─ Ambiguous OR new domain → COMPLEX

Determine phases:
├─ SMALL: Specify (1 line) + Execute
├─ MEDIUM: Specify (brief) + Execute
├─ LARGE: Specify + Design + Tasks + Execute
└─ COMPLEX: Specify + Discuss + Design + Tasks + Execute + UAT
```

**Safety Valve:**
Even when Tasks is skipped, Execute ALWAYS lists atomic steps inline.
If listing reveals >5 steps or dependencies → STOP, create formal tasks.md.

---

## 📁 PROJECT STRUCTURE (Mandatory)

```
medpark/
├── .specs/                      # ALL specifications live here
│   ├── project/
│   │   ├── PROJECT.md          # Vision, goals (from README)
│   │   ├── ROADMAP.md          # Features, milestones
│   │   └── STATE.md            # ⭐ CRITICAL: decisions, blockers, todos
│   ├── features/
│   │   └── [feature-name]/
│   │       ├── spec.md         # Requirements with REQ-IDs
│   │       ├── context.md      # User decisions (COMPLEX only)
│   │       ├── design.md       # Architecture (LARGE/COMPLEX)
│   │       └── tasks.md        # Atomic tasks (LARGE/COMPLEX)
│   └── quick/
│       └── NNN-slug/
│           ├── TASK.md
│           └── SUMMARY.md
├── src/                         # Implementation
├── docs/                        # Public documentation
└── tests/                       # Test suite
```

**STATE.md is sacred:**
- MUST be read at start of every session
- MUST be updated after every decision
- MUST track: decisions, blockers, todos, lessons, deferred ideas
- MUST include traceability (REQ-IDs)

---

## 🤖 SUB-AGENT DELEGATION (Critical!)

### **When to Delegate:**

| Activity | Delegate? | Why |
|----------|-----------|-----|
| Research | ✅ YES | Large output, only summary matters |
| Implementing a task | ✅ YES | File edits consume context |
| Parallel tasks [P] | ✅ YES | Only way to truly parallelize |
| Sequential tasks | ✅ YES | Keeps artifacts out of main context |
| Planning | ❌ NO | Needs full accumulated context |
| Validation reports | ❌ NO | Needs coherent view |
| Quick fixes | ❌ NO | Overhead > benefit |

### **Sub-Agent Context Package:**

Each sub-agent receives:
- ✅ Task definition (from tasks.md)
- ✅ Coding principles & conventions
- ✅ TESTING.md (for gate checks)
- ✅ Relevant spec/design context
- ❌ Other tasks' definitions
- ❌ Full chat history
- ❌ STATE.md (unless task explicitly references decision)

**Context Budget:**
- Main context: <40k tokens (monitoring required)
- Reserve: 160k+ tokens for reasoning & outputs
- Sub-agent: Fresh 200k window each

---

## ⚡ HARNESS ADAPTATION RULES

**Unlike traditional spec-driven:**

### **1. Plan Adapts to Reality**

```javascript
// Traditional (fails on error)
spec → design → tasks → execute
                          ↓
                        ERROR → STOP ❌

// Harness (adapts on error)
spec → design → tasks → execute
         ↑                ↓
         └──── ERROR ─────┘
               ↓
         BACKTRACK + ADAPT + RETRY ✅
```

**Implementation:**
- Dynamic Planner detects failures
- Generates alternative plan
- Tries fallback tools/approaches
- Only stops if all alternatives exhausted

### **2. Quality Enforced at Runtime**

```javascript
// Traditional (manual review)
execute → output → human reviews → maybe rework ❌

// Harness (auto-grading)
execute → output → Quality Grader (0-10)
                        ↓
                   score <7?
                   ↓        ↓
                  YES      NO
                   ↓        ↓
          REGENERATE    ACCEPT ✅
```

**Implementation:**
- Quality Grader evaluates every response
- Threshold: 7.0/10
- Auto-regeneration if below threshold
- Learning loop: stores improvements

### **3. Context Isolated via Sub-Agents**

```javascript
// Traditional (context overflow)
task1 → accumulate → task2 → accumulate → OVERFLOW ❌

// Harness (isolation)
main_context (clean)
    ├─ spawn sub-agent(task1) → result → aggregate
    └─ spawn sub-agent(task2) → result → aggregate ✅
```

**Implementation:**
- Sub-Agent Manager spawns isolated workers
- Each gets fresh 200k context
- Main context only receives results
- Prevents degradation over long sessions

---

## 📋 WORKFLOW (Step-by-Step)

### **Starting a New Feature:**

```bash
# 1. Assess Scope
- Count files affected
- Check for ambiguity
- Determine: SMALL | MEDIUM | LARGE | COMPLEX

# 2. Create Feature Folder
mkdir -p .specs/features/[feature-name]

# 3. SPECIFY (always required)
# Create spec.md with:
- Feature description
- Requirement IDs (REQ-XXX-001, REQ-XXX-002...)
- Acceptance criteria
- Success metrics

# 4. DESIGN (if LARGE or COMPLEX)
# Create design.md with:
- Architecture overview
- Component diagram
- Data flows
- Dependencies
- Tech stack decisions

# 5. TASKS (if LARGE or COMPLEX)
# Create tasks.md with:
┌────────────────────────────────────────┐
│ ## Task ID: Brief Description          │
│ **What:** What needs to be done        │
│ **Where:** Which files                 │
│ **Depends on:** Task IDs               │
│ **Reuses:** Existing code/patterns     │
│ **Done when:** Completion criteria     │
│ **Tests:** Test types                  │
│ **Gate:** Command to verify            │
│ **Parallel:** [P] if can run parallel  │
└────────────────────────────────────────┘

# 6. EXECUTE (always required)
# For each task:
- If formal tasks.md exists → delegate to sub-agent
- If inline steps → implement directly
- Atomic git commit per task
- Run gate checks
- Update STATE.md

# 7. VERIFY
# LARGE: Unit + integration tests
# COMPLEX: Full UAT walkthrough
```

---

## 🎯 REQUIREMENT TRACEABILITY

**Every requirement MUST have:**

```markdown
REQ-[COMPONENT]-[NUMBER]

Examples:
- REQ-SUP-001: Supabase connection
- REQ-ONC-001: Uro-oncologist specialist
- REQ-GUI-001: Cochrane scraper

Traceability chain:
REQ-SUP-001 (spec.md)
  → DESIGN-SUP-001 (design.md)
    → TASK-SUP-001 (tasks.md)
      → IMPL-SUP-001 (src/db/supabase-client.js)
        → TEST-SUP-001 (tests/db/supabase-client.test.js)
          → COMMIT-SUP-001 (git hash)
```

**In git commits:**
```bash
git commit -m "TASK-SUP-001: Create Supabase client

- Implements REQ-SUP-001
- Connection established to erkbovwfcngywbagjikl
- Gate: npm test db/supabase-client (✅ PASS)
- Files: src/db/supabase-client.js, tests/..."
```

---

## 🧪 TESTING GATES

**Every task MUST define gate check:**

```markdown
## T1: Create Supabase Client
**Gate:** `npm test db/supabase-client`
**Pass criteria:** Connection test returns 200 OK

## T2: Implement RAG Search
**Gate:** `npm test db/knowledge-base`
**Pass criteria:** Search returns relevant results, embeddings match >0.7 similarity
```

**Gate types:**
- Unit tests (fast, isolated)
- Integration tests (with real Supabase)
- E2E tests (full flow)
- Manual UAT (COMPLEX features only)

---

## 📊 STATE MANAGEMENT

### **STATE.md Sections (mandatory):**

```markdown
# Project State

## Current Focus
- Phase, status, scope

## Decisions Made
- D1: Decision title
  - Date, rationale, impact

## Active Blockers
- None | List of blockers

## Todos (by priority)
- P0 (critical), P1 (high), P2 (medium), P3 (low)

## Lessons Learned
- L1: Lesson title
  - What happened, solution, applied where

## Deferred Ideas
- Idea title
  - Why deferred, when revisit

## Metrics to Track
- Cost, quality, performance

## Session Handoff Notes
- For resuming work
```

**Update frequency:**
- After every decision → add to Decisions
- After every blocker → add to Blockers
- After every lesson → add to Lessons
- At end of day → update Session Handoff

---

## 🚀 QUICK MODE (for small fixes)

**When scope is SMALL:**

```bash
# No formal specs needed
# Just: describe → implement → verify → commit

Example:
"Fix typo in README"
→ Edit README.md
→ Verify: preview looks correct
→ Commit: "Quick: Fix typo in installation command"
→ Done (no spec.md needed)
```

**Criteria for Quick Mode:**
- ≤3 files
- One-sentence description
- Obvious implementation
- No architectural impact

---

## ⚠️ WHAT NOT TO DO

### **❌ Anti-Patterns:**

1. **Skip STATE.md**
   - Decisions get lost
   - Blockers forgotten
   - Lessons not learned

2. **Force full pipeline on simple tasks**
   - Overhead > value
   - Slows velocity

3. **Skip auto-sizing assessment**
   - COMPLEX treated as SMALL → fails
   - SMALL treated as COMPLEX → waste

4. **Accumulate context in main session**
   - Degradation over time
   - "I'll be more concise" syndrome

5. **No traceability**
   - Can't track REQ → IMPL → TEST
   - Can't justify decisions later

6. **Fabricate when uncertain**
   - Cascading failures
   - "I don't know" > inventing APIs

---

## 🎓 KNOWLEDGE VERIFICATION CHAIN

**Strict order (never skip):**

```
1. Codebase → existing code, conventions
   ↓
2. Project docs → README, .specs/
   ↓
3. Web search → official docs, reputable sources
   ↓
4. Flag as uncertain → "I'm not certain, verify this"

NEVER:
❌ Assume
❌ Fabricate APIs
❌ Invent patterns
```

**If you can't find it → say "I don't know"**

Uncertainty > Fabrication (always)

---

## 📈 SUCCESS METRICS

**Methodology is working when:**

- ✅ STATE.md is always current
- ✅ Requirements are traceable (REQ → IMPL → TEST)
- ✅ Context stays <40k tokens
- ✅ Features auto-sized correctly
- ✅ Sub-agents used for implementation
- ✅ Quality scores trend upward
- ✅ Decisions documented
- ✅ Blockers resolved quickly

**Methodology is failing when:**
- ❌ STATE.md outdated
- ❌ Can't trace requirements
- ❌ Context overflow
- ❌ Wrong scope assessment
- ❌ Main context accumulates artifacts
- ❌ Quality degrades
- ❌ Same mistakes repeated

---

## 🔄 CONTINUOUS IMPROVEMENT

**This methodology evolves:**

1. Every lesson learned → update METHODOLOGY.md
2. Every anti-pattern discovered → add to warnings
3. Every success pattern → add to examples
4. Quarterly review → refine based on data

**Version history tracked in git**

---

## 📚 REFERENCES

- **TLC Spec-Driven:** https://agent-skills.techleads.club/skills/tlc-spec-driven/
- **Harness Engineering:** "Spec Driven chegou no limite" - Waldemar Neto
- **Applied:** MedPark development, 2026-04-10

---

## ⚖️ ENFORCEMENT

**This is not optional.**

All MedPark development MUST follow this methodology:

- ✅ Core contributors
- ✅ External contributors (via PR reviews)
- ✅ AI agents (this document is loaded as context)
- ✅ Future team members

**Non-compliance = PR rejected**

---

**Methodology Owner:** Dr. Frederico Mascarenhas  
**Version:** 1.0  
**Effective Date:** 2026-04-10  
**Status:** ACTIVE AND MANDATORY
