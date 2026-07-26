# Contribution Journal — pathreview

## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/154

**Issue title:** Health check DB probe passes a raw SQL string, which fails under SQLAlchemy 2.x

**Tier:** [x] Tier 1  [ ] Tier 2  [ ] Tier 3

**Problem summary:**
The database health check in `api/routes/health.py` runs a connectivity probe by executing the literal string `"SELECT 1"` directly against the database session. SQLAlchemy 2.x removed support for passing raw strings to `execute()` and now requires textual SQL to be wrapped in `sqlalchemy.text()`. As a result, the probe raises an `ArgumentError` and the health check reports the database as unreachable even when it is fully operational. The fix is a one-line change: import `text` from `sqlalchemy` and change the probe to `text("SELECT 1")`.

**Is this right for me? — scope reasoning:**
The fix is a single-line change in one file (`api/routes/health.py`) with a clear root cause documented in the issue. The SQLAlchemy 2.x migration requirement is well-documented and the error message itself points directly to the solution. The scope is narrow enough that it won't require understanding the full codebase, and the existing health check tests provide a clear target for verifying the fix. This is appropriate for a first open-source contribution.

**Branch name:** fix/154-health-check-raw-sql

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger

---

## Week 8 — Reproduction & solution planning

**Reproduction commit link:** [link to commit — update after pushing]

**Reproduction summary:**
In `api/routes/health.py` line 22, `await db.execute("SELECT 1")` passes a bare Python string to SQLAlchemy 2.x's `execute()` method. SQLAlchemy 2.x requires all textual SQL to be wrapped in `sqlalchemy.text()` — passing a raw string raises `ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')`. This is caught by the `except` block, which sets `health_status["dependencies"]["postgres"] = "unhealthy"` and causes the endpoint to return `503` even when the database is reachable.

**PLAN.md link:** [link to PLAN.md — update after pushing]

**Walkthrough video (recommended):** N/A

**Blockers or open questions:**
Need to inspect `tests/unit/api/test_health.py` to confirm whether the existing tests mock the DB session or use a live connection — this determines whether the fix will be automatically validated by the test suite or whether a new/updated test is needed.