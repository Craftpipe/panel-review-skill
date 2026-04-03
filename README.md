# panel-review

Multi-lens code review using three expert personas (Security Auditor, Architect, Performance Engineer) that analyze code independently and produce a unified verdict with prioritized findings.

## Installation

### Claude Code
```bash
# Option 1: Manual installation
cp SKILL.md ~/.claude/skills/panel-review/SKILL.md

# Option 2: Plugin installation
claude plugin install https://github.com/craftpipe/panel-review
```

### Cursor
```bash
# Option 1: Manual installation
cp SKILL.md ~/.cursor/skills/panel-review/SKILL.md

# Option 2: Via Cursor marketplace
# Search for "panel-review" in Cursor's plugin marketplace and install
```

### ClawHub/OpenClaw
```bash
# Option 1: ClawHub marketplace
# Visit ClawHub marketplace and install panel-review

# Option 2: Git clone
git clone https://github.com/craftpipe/panel-review.git
cp -r panel-review ~/.claude/skills/
```

### Codex CLI
```bash
cp SKILL.md ~/.codex/skills/panel-review/SKILL.md
```

## Usage

The skill activates when you request a code review or paste code for analysis. It automatically:

1. **Spawns three independent expert reviewers** — each analyzes the code through their specialized lens
2. **Generates individual reports** — Security Auditor flags vulnerabilities, Architect reviews design patterns, Performance Engineer identifies optimization opportunities
3. **Produces unified verdict** — consolidates findings into a prioritized list with severity levels and actionable fix suggestions

## Examples

### Example 1: API Endpoint Review
```
Request: "Review this authentication endpoint for security and performance"

Output:
- Security Auditor: Identifies missing input validation, SQL injection risk
- Architect: Flags tight coupling, suggests dependency injection
- Performance Engineer: Detects N+1 query problem, recommends caching strategy
- Unified Verdict: 3 critical, 2 high, 1 medium priority issues with fixes
```

### Example 2: Database Query Review
```
Request: "Review this user lookup query"

Output:
- Security Auditor: Checks for injection vulnerabilities
- Architect: Evaluates schema design and relationships
- Performance Engineer: Analyzes query execution plan, suggests indexing
- Unified Verdict: Consolidated recommendations ranked by impact
```

## Features

- **Parallel Analysis** — Three expert perspectives simultaneously
- **Prioritized Findings** — Issues ranked by severity and business impact
- **Actionable Fixes** — Concrete code suggestions for each finding
- **Free & Open Source** — No licensing restrictions

---

Built with AI by Craftpipe

Support: support@heijnesdigital.com
## Pro Features

The Pro version includes:
- team config
- ci integration
- auto fix

Get it at: https://craftpipe.gumroad.com
