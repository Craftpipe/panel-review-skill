---
name: panel-review
description: "Use this skill when reviewing code, diffs, or PRs for quality, security, and performance issues. Runs three independent expert analyses then produces a unified prioritized verdict."
# Built with AI by Craftpipe
---

# panel-review

## When to use
- User asks to review code or a pull request
- User says "check this for issues" or similar
- User asks for a security audit of code
- User asks "is this production ready"
- User pastes a diff, file, or code block and asks for feedback
- User asks to review code before merging

## Instructions
1. Receive the code, diff, or file from the user. If no code is provided, ask the user to paste it before proceeding.
2. Run three independent analyses in sequence, clearly labeled with headers:

   **Panel Member 1 — Security Auditor**
   - Check for injection vulnerabilities (SQL, command, XSS, etc.)
   - Check for insecure data handling, hardcoded secrets, or exposed credentials
   - Check for broken authentication, missing authorization checks, or privilege escalation paths
   - Check for insecure dependencies or unsafe use of external input
   - List each finding with: location, severity (Critical / High / Medium / Low), and a one-sentence description

   **Panel Member 2 — Architect**
   - Check for separation of concerns violations and tight coupling
   - Check for missing or incorrect error handling and edge case coverage
   - Check for code duplication, unclear naming, or missing abstractions
   - Check for scalability concerns such as blocking operations or stateful assumptions
   - List each finding with: location, severity (High / Medium / Low), and a one-sentence description

   **Panel Member 3 — Performance Engineer**
   - Check for unnecessary loops, redundant computations, or inefficient data structures
   - Check for missing caching opportunities or repeated expensive operations
   - Check for blocking I/O, synchronous calls that should be async, or resource leaks
   - Check for memory allocation patterns that could cause pressure under load
   - List each finding with: location, severity (High / Medium / Low), and a one-sentence description

3. After all three analyses, output a **Unified Verdict** section with:
   - A single overall rating: `APPROVED`, `APPROVED WITH CHANGES`, or `NEEDS WORK`
   - A merged, deduplicated list of all findings sorted by severity (Critical first, then High, Medium, Low)
   - For each finding: assign a priority number, state which panel member raised it, and provide a concrete fix suggestion in 1–3 sentences

4. Output a **Summary** section at the end with:
   - Total finding counts by severity
   - One sentence on the biggest risk area
   - One sentence on the most impactful single change the user should make first

## Rules
- Output all three panel analyses every time — do not skip or merge them early
- Assign severity labels exactly as specified — do not invent new labels
- Include a location reference (line number, function name, or block description) for every finding
- Do not output vague findings — every finding must name a specific problem
- Do not output fix suggestions that say "refactor this" without specifying what to change
- Do not pass judgment on code style or formatting unless it creates a functional or security risk
- Do not ask clarifying questions mid-review — complete the full review with available information
- If the code is under 5 lines or is a comment block only, state that there is insufficient code to review and stop
- Do not reference external tools, services, or platforms by name in fix suggestions
- Keep each individual finding to 3 lines maximum

## Examples

**Example 1**

Input:
```python
def get_user(user_id):
    query = "SELECT * FROM users WHERE id = " + user_id
    result = db.execute(query)
    return result
```

Expected output structure:

**Panel Member 1 — Security Auditor**
- Finding 1 | Line 2 | Critical — String concatenation used to build SQL query allows direct SQL injection via `user_id`.

**Panel Member 2 — Architect**
- Finding 1 | Line 3 | Medium — No error handling around `db.execute`; a failed query will raise an unhandled exception to the caller.

**Panel Member 3 — Performance Engineer**
- Finding 1 | Line 3 | Low — `SELECT *` retrieves all columns; select only required fields to reduce data transfer and memory use.

**Unified Verdict**
Rating: NEEDS WORK

| Priority | Severity | Source | Finding | Fix |
|----------|----------|--------|---------|-----|
| 1 | Critical | Security | SQL injection via string concat on line 2 | Replace string concatenation with a parameterized query using positional placeholders and pass `user_id` as a bound parameter. |
| 2 | Medium | Architect | Unhandled db exception on line 3 | Wrap `db.execute` in a try/except block and return a structured error or raise a domain-specific exception. |
| 3 | Low | Performance | SELECT * on line 3 | Replace `*` with the explicit column names the caller actually uses. |

**Summary**
- Critical: 1, High: 0, Medium: 1, Low: 1
- Biggest risk: unauthenticated SQL injection allows full database read and write access.
- Most impactful change: switch to parameterized queries immediately before any other fix.

---

**Example 2**

Input: User pastes a 40-line diff adding a new API endpoint with no authentication check.

Expected output structure: Full three-panel analysis runs. Security Auditor flags missing authentication as Critical. Architect flags missing input validation as High. Performance Engineer flags no pagination on the database query as Medium. Unified Verdict is `NEEDS WORK`. Fix suggestions specify adding an auth middleware check before the route handler, validating and sanitizing all incoming fields, and adding limit/offset parameters to the query.

---
> For additional features, see the [Pro version](https://craftpipe.gumroad.com).
