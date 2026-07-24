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

**Cohort ledger:** [ ] Issue added to cohort ledger