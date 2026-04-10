# Example: LARGE Scope (Full Pipeline)

**Feature:** "Add user authentication system"

**Scope:** LARGE (multi-component, >10 tasks)

---

## 1. SPECIFY

Create `.specs/features/auth/spec.md`:

```markdown
# Feature: User Authentication

## Requirements

### REQ-AUTH-001: User Registration
- Users can create accounts with email/password
- Email validation required
- Password strength: min 8 chars, 1 uppercase, 1 number

### REQ-AUTH-002: User Login
- Users can login with email/password
- JWT token issued on success
- Token expires after 24h

### REQ-AUTH-003: Password Reset
- Users can request password reset via email
- Reset link expires after 1h
- Old passwords cannot be reused (last 3)

## Success Metrics
- Registration: <2s response time
- Login: <1s response time
- Security: Zero plaintext passwords in DB
```

---

## 2. DESIGN

Create `.specs/features/auth/design.md`:

```markdown
# Architecture: Authentication System

## Components

1. **src/auth/register.js** - Registration logic
2. **src/auth/login.js** - Login + JWT generation
3. **src/auth/reset.js** - Password reset flow
4. **src/db/users.js** - User CRUD operations
5. **src/middleware/auth.js** - JWT verification middleware

## Data Flow

```
User → POST /register → Validate → Hash password → Store DB → 201 Created
User → POST /login → Verify password → Generate JWT → 200 OK + token
User → POST /reset → Send email → Click link → New password → 200 OK
```

## Tech Stack
- bcrypt (password hashing)
- jsonwebtoken (JWT)
- nodemailer (password reset emails)
- PostgreSQL (user storage)

## Security
- Passwords hashed with bcrypt (10 rounds)
- JWTs signed with HS256 + secret
- Rate limiting: 5 failed logins → 15min lockout
```

---

## 3. TASKS

Create `.specs/features/auth/tasks.md`:

```markdown
# Tasks: Authentication

## T1: Database Schema [REQUIRED]
**What:** Create users table with proper constraints
**Where:** migrations/001_create_users.sql
**Depends on:** None
**Done when:** Table exists, indexes created
**Tests:** Migration runs successfully
**Gate:** `npm run migrate`

## T2: Password Hashing [REQUIRED]
**What:** Implement bcrypt wrapper
**Where:** src/auth/hash.js
**Depends on:** None
**Reuses:** Standard bcrypt patterns
**Done when:** Hash + verify functions work
**Tests:** Unit tests for hash/verify
**Gate:** `npm test auth/hash`

## T3: User Registration [REQUIRED]
**What:** POST /register endpoint
**Where:** src/auth/register.js, src/api/routes/auth.js
**Depends on:** T1, T2
**Done when:** Users can register, passwords hashed
**Tests:** Integration tests with real DB
**Gate:** `npm test auth/register`

## T4: JWT Generation [REQUIRED] [P]
**What:** Create + verify JWT tokens
**Where:** src/auth/jwt.js
**Depends on:** None
**Reuses:** jsonwebtoken library
**Done when:** Tokens generated + verified correctly
**Tests:** Unit tests for JWT lifecycle
**Gate:** `npm test auth/jwt`
**Parallel:** [P] (can run with T3)

## T5: User Login [REQUIRED]
**What:** POST /login endpoint
**Where:** src/auth/login.js, src/api/routes/auth.js
**Depends on:** T1, T2, T4
**Done when:** Users can login, JWT returned
**Tests:** Integration tests
**Gate:** `npm test auth/login`

## T6: Password Reset Email [OPTIONAL] [P]
**What:** Send reset link via email
**Where:** src/auth/reset.js, src/email/templates/reset.html
**Depends on:** T1
**Done when:** Email sent with valid reset token
**Tests:** Integration tests (mock SMTP)
**Gate:** `npm test auth/reset-email`
**Parallel:** [P] (can run with T5)

## T7: Password Reset Handler [OPTIONAL]
**What:** POST /reset endpoint
**Where:** src/auth/reset.js, src/api/routes/auth.js
**Depends on:** T6
**Done when:** Password updated, old password invalidated
**Tests:** Integration tests
**Gate:** `npm test auth/reset-handler`

## T8: Auth Middleware [REQUIRED]
**What:** JWT verification middleware
**Where:** src/middleware/auth.js
**Depends on:** T4
**Done when:** Middleware blocks unauthenticated requests
**Tests:** Unit tests
**Gate:** `npm test middleware/auth`

## T9: Rate Limiting [REQUIRED]
**What:** Prevent brute force attacks
**Where:** src/middleware/rate-limit.js
**Depends on:** None
**Reuses:** express-rate-limit library
**Done when:** 5 failed logins → 15min lockout
**Tests:** Integration tests
**Gate:** `npm test middleware/rate-limit`

## T10: E2E Tests [REQUIRED]
**What:** Full authentication flow tests
**Where:** tests/e2e/auth.test.js
**Depends on:** T3, T5, T7, T8, T9
**Done when:** Register → Login → Reset flow works
**Tests:** E2E with Supertest
**Gate:** `npm run test:e2e`
```

**Execution order:**
```
Sequential: T1 → T3 → T5 → T7 → T10
Parallel batches:
  - T2, T4 (no dependencies)
  - T6, T8, T9 (after T4)
```

---

## 4. EXECUTE

### Sub-Agent Delegation

**Delegate to sub-agents:**
- T1 (database schema)
- T2, T4 (parallel, isolated)
- T3, T5, T7 (implementation tasks)
- T6, T8, T9 (parallel batch)

**Keep in main context:**
- Planning coordination
- T10 (E2E validation needs full context)

### Atomic Commits

```bash
# T1
git commit -m "TASK-AUTH-001: Create users table

- Implements REQ-AUTH-001, REQ-AUTH-002
- Migration: 001_create_users.sql
- Columns: id, email, password_hash, created_at
- Indexes: email (unique), created_at
- Gate: npm run migrate (✅ PASS)"

# T2
git commit -m "TASK-AUTH-002: Implement password hashing

- Implements REQ-AUTH-001
- Functions: hashPassword, verifyPassword
- bcrypt rounds: 10
- Gate: npm test auth/hash (✅ PASS 5/5)"

# ... continue for each task
```

---

## 5. VERIFY

### Gate Checks

```bash
npm test              # All unit tests pass
npm run test:e2e      # E2E flow works
npm run lint          # Code quality
npm run security      # Dependency audit
```

### Traceability

```
REQ-AUTH-001 (Registration)
  → DESIGN: Components 1, 4
    → TASK-AUTH-001 (database)
    → TASK-AUTH-002 (hashing)
    → TASK-AUTH-003 (endpoint)
      → IMPL: src/auth/register.js (182 lines)
        → TEST: tests/auth/register.test.js (87 lines)
          → COMMIT: abc1234 (verified ✓)

REQ-AUTH-002 (Login)
  → DESIGN: Components 2, 4
    → TASK-AUTH-004 (JWT)
    → TASK-AUTH-005 (endpoint)
      → IMPL: src/auth/login.js (124 lines)
        → TEST: tests/auth/login.test.js (65 lines)
          → COMMIT: def5678 (verified ✓)
```

---

## 6. UPDATE STATE.md

```markdown
## Decisions Made

### D3: bcrypt over argon2
**Date:** 2026-04-10
**Decision:** Use bcrypt for password hashing
**Rationale:** Better ecosystem support, sufficient security
**Impact:** 10 rounds = ~100ms hash time (acceptable)

### D4: JWT over sessions
**Date:** 2026-04-10
**Decision:** Stateless JWT tokens, no server-side sessions
**Rationale:** Scales better, simpler deployment
**Impact:** Cannot revoke tokens (workaround: blacklist if needed)

## Lessons Learned

### L2: Rate Limiting Critical
**Date:** 2026-04-10
**Lesson:** Initial design lacked rate limiting → brute force risk
**Solution:** Added T9 (rate limiting middleware)
**Applied:** Now standard for all auth systems
```

---

## Timeline

| Phase | Planned | Actual |
|-------|---------|--------|
| Specify | 2h | 1.5h ✓ |
| Design | 4h | 3h ✓ |
| Tasks | 2h | 2.5h |
| Execute | 3 days | 2.5 days ✓ |
| Verify | 4h | 5h |

**Total:** 3.5 days (estimate: 4 days) → **under budget** ✓

---

## Result

✅ **REQ-AUTH-001:** Registration working, passwords hashed  
✅ **REQ-AUTH-002:** Login working, JWT issued  
✅ **REQ-AUTH-003:** Password reset flow complete  
✅ **All gates passed:** 47/47 tests  
✅ **Traceability:** 100% (all REQs → COMMITs)  
✅ **Security audit:** No vulnerabilities

**Status:** Production-ready
