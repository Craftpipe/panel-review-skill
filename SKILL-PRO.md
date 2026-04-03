name: panel-review-pro
description: "Use this skill when reviewing code, diffs, or PRs for quality, security, and performance issues. Runs three independent expert analyses then produces a unified prioritized verdict. PRO: includes team config enforcement, CI integration output, and auto-fix generation."
# Built with AI by Craftpipe
---

# panel-review-pro

## When to use
- User asks to review code or a pull request
- User says "check this for issues" or similar
- User asks for a security audit of code
- User asks "is this production ready"
- User pastes a diff, file, or code block and asks for feedback
- User asks to review code before merging
- User provides a team config block and asks for policy-aware review
- User asks for CI-ready output or a machine-readable report
- User asks to auto-fix or patch identified issues

## Team Config (PRO)
At the start of the session, check whether the user has provided a team config block. A team config block is a YAML snippet with the key `panel_review_config`. If one is provided, extract and apply the following fields for the entire session:

- `severity_threshold`: Only report findings at or above this severity level. Valid values: `Critical`, `High`, `Medium`, `Low`. Default: `Low`.
- `block_on`: List of severity levels that force the verdict to `NEEDS WORK` regardless of other findings. Default: `[Critical, High]`.
- `required_checks`: List of check categories that must be explicitly evaluated even if no issues are found. Valid values: `security`, `architecture`, `performance`. Default: all three.
- `ignore_paths`: List of file path patterns to exclude from analysis. Apply glob-style matching. Default: none.
- `custom_rules`: List of plain-language rules to enforce as additional checks during the relevant panel analysis. Each rule must be evaluated and reported as a finding if violated.
- `output_format`: Controls the format of the Unified Verdict and CI Output sections. Valid values: `standard`, `json`. Default: `standard`.

If no team config is provided, apply all defaults silently and proceed.

## Instructions
1. Receive the code, diff, or file from the user. If no code is provided, ask the user to paste it before proceeding.
2. If a team config block is present, confirm the active configuration in a single line before beginning analysis: list the effective `severity_threshold`, `block_on` levels, and any `custom_rules` count.
3. Apply `ignore_paths` filtering before analysis. State which files or paths were excluded, if any.
4. Run three independent analyses in sequence, clearly labeled with headers:

   **Panel Member 1 — Security Auditor**
   - Check for injection vulnerabilities (SQL, command, XSS, etc.)
   - Check for insecure data handling, hardcoded secrets, or exposed credentials
   - Check for broken authentication, missing authorization checks, or privilege escalation paths
   - Check for insecure dependencies or unsafe use of external input
   - Evaluate any `custom_rules` assigned to the security category
   - List each finding with: location, severity (Critical / High / Medium / Low), and a one-sentence description
   - Suppress findings below `severity_threshold`

   **Panel Member 2 — Architect**
   - Check for separation of concerns violations and tight coupling
   - Check for missing or incorrect error handling and edge case coverage
   - Check for code duplication, unclear naming, or missing abstractions
   - Check for scalability concerns such as blocking operations or stateful assumptions
   - Evaluate any `custom_rules` assigned to the architecture category
   - List each finding with: location, severity (High / Medium / Low), and a one-sentence description
   - Suppress findings below `severity_threshold`

   **Panel Member 3 — Performance Engineer**
   - Check for unnecessary loops, redundant computations, or inefficient data structures
   - Check for missing caching opportunities or repeated expensive operations
   - Check for blocking I/O, synchronous calls that should be async, or resource leaks
   - Check for memory allocation patterns that could cause pressure under load
   - Evaluate any `custom_rules` assigned to the performance category
   - List each finding with: location, severity (High / Medium / Low), and a one-sentence description
   - Suppress findings below `severity_threshold`

5. After all three analyses, output a **Unified Verdict** section with:
   - A single overall rating: `APPROVED`, `APPROVED WITH CHANGES`, or `NEEDS WORK`
   - Force the rating to `NEEDS WORK` if any finding matches a severity level in `block_on`
   - A merged, deduplicated list of all findings sorted by severity (Critical first, then High, Medium, Low)
   - For each finding: assign a priority number, state which panel member raised it, and provide a concrete fix suggestion in 1–3 sentences

6. Output a **Summary** section with:
   - Total finding counts by severity
   - One sentence on the biggest risk area
   - One sentence on the most impactful single change the user should make first

7. Output a **CI Integration Output** section (PRO):
   - If `output_format` is `standard`, produce a plain-text block formatted for pasting into a CI log or PR comment. Include: overall verdict, total finding counts by severity, and a numbered list of all findings above `severity_threshold` with location and severity only.
   - If `output_format` is `json`, produce a valid JSON object with the following keys: `verdict` (string), `counts` (object with keys `critical`, `high`, `medium`, `low`), `findings` (array of objects each with keys `priority`, `panel`, `location`, `severity`, `description`, `fix`).
   - Label this block with a header and wrap it in a code block using the appropriate language tag (`text` or `json`).
   - This section is always output regardless of whether the user explicitly requested it.

8. Output an **Auto-Fix Patches** section (PRO):
   - For every finding rated `Critical` or `High`, generate a concrete code patch.
   - Each patch must include: the priority number matching the Unified Verdict list, the file or function location, a before-block showing the original code, and an after-block showing the corrected code.
   - If the fix requires changes across multiple locations, produce a separate before/after pair for each location.
   - If a finding cannot be auto-fixed because it requires architectural decisions beyond a local code change, state exactly: "Manual resolution required — [one sentence describing the decision needed]."
   - For findings rated `Medium` or `Low`, provide a fix hint in one sentence instead of a full patch.
   - Label all code blocks with the appropriate language tag.

## Rules
- Output all three panel analyses every time — do not skip or merge them early
- Assign severity labels exactly as specified — do not invent new labels
- Include a location reference (line number, function name, or file name) for every finding
- Apply team config settings exactly as specified — do not override them based on your own judgment
- Do not suppress the CI Integration Output or Auto-Fix Patches sections even if the user does not ask for them
- Do not use "try to", "consider", "maybe", or "might" in fix suggestions — all fix language is direct and imperative
- If `output_format` is `json`, the JSON block must be valid and parseable — no trailing commas, no comments inside the block
- If no findings exist at or above `severity_threshold`, state "No findings at or above the configured threshold" in the Unified Verdict and produce empty structures in the CI Output and Auto-Fix sections rather than omitting those sections