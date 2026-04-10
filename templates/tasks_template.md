# Tasks: {{FEATURE_NAME}}

**Feature Spec:** [spec.md](spec.md)  
**Design Doc:** [design.md](design.md) (if exists)

---

## Task Breakdown

### T1: {{Task Title}} [REQUIRED]
**What:** Describe what needs to be done  
**Where:** List affected files (src/auth.js, tests/auth.test.js)  
**Depends on:** None | T0, T2 (list task IDs)  
**Reuses:** Existing code/patterns (helps prevent reinventing wheel)  
**Done when:** Specific completion criteria  
**Tests:** Unit | Integration | E2E  
**Gate:** `npm test auth` (command to verify)  
**Parallel:** (Add [P] marker if can run in parallel with other tasks)

---

### T2: {{Another Task}} [OPTIONAL] [P]
**What:** Another thing to build  
**Where:** src/db/client.js, src/db/query.js  
**Depends on:** T1  
**Reuses:** Existing database patterns from project X  
**Done when:** Connection pool established, queries working  
**Tests:** Integration tests with real database  
**Gate:** `npm test db`  
**Parallel:** [P] (can run parallel with T3)

---

### T3: {{Third Task}} [REQUIRED] [P]
**What:** Yet another component  
**Where:** src/api/routes.js  
**Depends on:** T1  
**Reuses:** Express middleware pattern  
**Done when:** All routes respond with 200 OK  
**Tests:** E2E tests with Supertest  
**Gate:** `npm test api`  
**Parallel:** [P] (can run parallel with T2)

---

## Execution Order

**Sequential:**
```
T1 → T4 → T7
```

**Parallel batches:**
```
Batch 1: T2, T3 (both depend on T1, can run together)
Batch 2: T5, T6 (both depend on T4, can run together)
```

---

## Sub-Agent Delegation Strategy

**Delegate to sub-agents:**
- ✅ T2, T3 (parallel tasks)
- ✅ T5, T6 (parallel tasks)
- ✅ Any task with >50 lines of code

**Keep in main context:**
- Planning and coordination
- Validation reports
- Decision-making

---

## Progress Tracking

| Task | Status | Assignee | Gate Result | Commit |
|------|--------|----------|-------------|--------|
| T1 | ⏸️ Todo | - | - | - |
| T2 | ⏸️ Todo | - | - | - |
| T3 | ⏸️ Todo | - | - | - |

**Legend:**
- ⏸️ Todo
- 🔄 In Progress
- ✅ Done
- ❌ Blocked

---

## SPEC_DEVIATION Markers

**If implementation deviates from spec:**

```markdown
SPEC_DEVIATION: REQ-XXX-001
Reason: Original approach didn't account for edge case Y
Change: Used alternative pattern Z instead
Impact: None (still meets acceptance criteria)
Approved: 2026-04-10
```

*(Helps with traceability and audit trails)*

---

## Verification Checklist

After all tasks complete:

- [ ] All gates pass (`npm test`)
- [ ] Code review completed
- [ ] No SPEC_DEVIATION markers without approval
- [ ] Traceability chain intact (REQ → IMPL → TEST → COMMIT)
- [ ] STATE.md updated with lessons learned
- [ ] Documentation updated

---

**Created:** {{DATE}}  
**Last Updated:** {{DATE}}  
**Status:** {{Planning|In Progress|Complete}}
