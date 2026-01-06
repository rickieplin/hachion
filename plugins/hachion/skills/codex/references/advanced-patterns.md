# Advanced Configuration Examples

---

## ⚠️ CRITICAL: Always Use `codex exec`

**ALL commands in this document use `codex exec` - this is mandatory in Claude Code.**

❌ **NEVER**: `codex -m ...` or `codex --flag ...` (will fail with "stdout is not a terminal")
✅ **ALWAYS**: `codex exec -m ...` or `codex exec --flag ...` (correct non-interactive mode)

Claude Code's bash environment is non-terminal. Plain `codex` commands will NOT work.

---

## Custom Model Selection

### Example 1: General Reasoning Task

**User Request**: "Review this code for architecture issues"

**Skill Executes**:
```bash
codex exec -m gpt-5.2 -s read-only \
  -c model_reasoning_effort=xhigh \
  "Review this code for architecture issues"
```

**Why**: Architectural review is a reasoning task - use gpt-5.2 with read-only sandbox.

---

### Example 2: Code Editing Task

**User Request**: "Implement the authentication module"

**Skill Executes**:
```bash
codex exec -m gpt-5.2-codex -s workspace-write \
  -c model_reasoning_effort=xhigh \
  "Implement the authentication module"
```

**Why**: Implementation requires file writing and code generation - use gpt-5.2-codex (56.4% SWE-Bench Pro).

---

## Workspace Write Permission

### Example 3: Allow File Modifications

**User Request**: "Have Codex refactor this codebase (allow file writing)"

**Skill Executes**:
```bash
codex exec -m gpt-5.2-codex -s workspace-write \
  -c model_reasoning_effort=xhigh \
  "Refactor this codebase for better maintainability"
```

**Permission**: `workspace-write` allows Codex to modify files directly.

⚠️ **Warning**: Only use `workspace-write` when you trust the operation and want file modifications.

---

### Example 4: Read-Only Code Review

**User Request**: "Review this code for security vulnerabilities (read-only)"

**Skill Executes**:
```bash
codex exec -m gpt-5.2 -s read-only \
  -c model_reasoning_effort=xhigh \
  "Review this code for security vulnerabilities"
```

**Permission**: `read-only` prevents file modifications - safer for review tasks.

---

## Web Search Integration

### Example 5: Research Latest Patterns

**User Request**: "Research latest Python async patterns and implement them (enable web search)"

**Skill Executes**:
```bash
codex exec -m gpt-5.2-codex -s workspace-write \
  -c model_reasoning_effort=xhigh \
  --enable web_search_request \
  "Research latest Python async patterns and implement them"
```

**Feature**: `--enable web_search_request` enables web search for up-to-date information.

---

### Example 6: Security Best Practices Research

**User Request**: "Use web search to find latest JWT security best practices, then review this auth code"

**Skill Executes**:
```bash
codex exec -m gpt-5.2 -s read-only \
  -c model_reasoning_effort=xhigh \
  --enable web_search_request \
  "Find latest JWT security best practices and review this auth code"
```

---

## Reasoning Effort Control

### Example 7: Maximum Reasoning for Complex Algorithm

**User Request**: "Design an optimal algorithm for distributed consensus (maximum reasoning)"

**Skill Executes**:
```bash
codex exec -m gpt-5.2 -s read-only \
  -c model_reasoning_effort=xhigh \
  "Design an optimal algorithm for distributed consensus"
```

**Default**: Already uses `xhigh` reasoning effort.

---

### Example 8: Quick Code Review (Lower Reasoning)

**User Request**: "Quick syntax check on this code (low reasoning)"

**Skill Executes**:
```bash
codex exec -m gpt-5.2 -s read-only \
  -c model_reasoning_effort=low \
  "Quick syntax check on this code"
```

**Use Case**: Fast turnaround for simple tasks. Override xhigh when speed matters more than depth.

---

## Verbosity Control

### Example 9: Detailed Explanation

**User Request**: "Explain this algorithm in detail (high verbosity)"

**Skill Executes**:
```bash
codex exec -m gpt-5.2 -s read-only \
  -c model_reasoning_effort=xhigh \
  -c model_verbosity=high \
  "Explain this algorithm in detail"
```

**Output**: Comprehensive, detailed explanation.

---

### Example 10: Concise Summary

**User Request**: "Briefly review this code (low verbosity)"

**Skill Executes**:
```bash
codex exec -m gpt-5.2 -s read-only \
  -c model_reasoning_effort=xhigh \
  -c model_verbosity=low \
  "Review this code"
```

**Output**: Concise, focused feedback.

---

## Working Directory Control

### Example 11: Specific Project Directory

**User Request**: "Work in the backend directory and review the API code"

**Skill Executes**:
```bash
codex exec -m gpt-5.2 -s read-only \
  -c model_reasoning_effort=xhigh \
  -C ./backend \
  "Review the API code"
```

**Feature**: `-C` flag sets working directory for Codex.

---

## Approval Policy

### Example 12: Request Approval for Shell Commands

**User Request**: "Implement the build script (ask before running commands)"

**Skill Executes**:
```bash
codex exec -m gpt-5.2-codex -s workspace-write \
  -c model_reasoning_effort=xhigh \
  -a on-request \
  "Implement the build script"
```

**Safety**: `-a on-request` requires approval before executing shell commands.

---

## Combined Advanced Configuration

### Example 13: Full-Featured Request

**User Request**: "Use web search to find latest security practices, review my auth module in detail with high reasoning, allow file fixes if needed (ask for approval)"

**Skill Executes**:
```bash
codex exec -m gpt-5.2-codex -s workspace-write \
  -c model_reasoning_effort=xhigh \
  -c model_verbosity=high \
  -a on-request \
  --enable web_search_request \
  "Find latest security practices, review my auth module in detail, and fix issues"
```

**Features**:
- Web search enabled (`--enable web_search_request`)
- Maximum reasoning (`model_reasoning_effort=xhigh`)
- Detailed output (`model_verbosity=high`)
- File writing allowed (`workspace-write`)
- Requires approval for commands (`-a on-request`)

---

## Decision Tree: When to Use GPT-5.2 vs GPT-5.2-Codex

### Use GPT-5.2 (General Reasoning) For:

```
┌─────────────────────────────────────┐
│     Architecture & Design           │
│  - System architecture              │
│  - API design                       │
│  - Data structure design            │
│  - Algorithm analysis               │
├─────────────────────────────────────┤
│     Analysis & Review               │
│  - Code reviews                     │
│  - Security audits                  │
│  - Performance analysis             │
│  - Quality assessment               │
├─────────────────────────────────────┤
│     Explanation & Learning          │
│  - Concept explanations             │
│  - Documentation review             │
│  - Trade-off analysis               │
│  - Best practices guidance          │
└─────────────────────────────────────┘
```

### Use GPT-5.2-Codex (Agentic Coding) For:

```
┌─────────────────────────────────────┐
│     Code Editing                    │
│  - Modify existing files            │
│  - Implement features               │
│  - Refactoring                      │
│  - Bug fixes                        │
├─────────────────────────────────────┤
│     Code Generation                 │
│  - Write new code                   │
│  - Generate boilerplate             │
│  - Create test files                │
│  - Scaffold projects                │
├─────────────────────────────────────┤
│     Long-Horizon Tasks              │
│  - Multi-file changes               │
│  - Complex refactoring              │
│  - Migration scripts                │
│  - Architectural overhauls          │
└─────────────────────────────────────┘
```

**Note**: gpt-5.2-codex has native context compaction for long-horizon work (56.4% SWE-Bench Pro).

---

## Sandbox Mode Decision Matrix

| Task | Recommended Sandbox | Rationale |
|------|---------------------|-----------|
| Code review | `read-only` | No modifications needed |
| Architecture design | `read-only` | Planning phase only |
| Security audit | `read-only` | Analysis without changes |
| Implement feature | `workspace-write` | Requires file modifications |
| Refactor code | `workspace-write` | Must edit existing files |
| Generate new files | `workspace-write` | Creates new files |
| Bug fix | `workspace-write` | Edits source files |

---

## Configuration Profiles

### Create a Config Profile

You can create reusable configuration profiles in `~/.codex/config.toml`:

```toml
[profiles.review]
model = "gpt-5.2"
sandbox = "read-only"
model_reasoning_effort = "xhigh"
model_verbosity = "medium"

[profiles.implement]
model = "gpt-5.2-codex"
sandbox = "workspace-write"
model_reasoning_effort = "xhigh"
approval_policy = "on-request"
```

### Use Profile in Skill

**User Request**: "Use the review profile to analyze this code"

**Skill Executes**:
```bash
codex exec -p review "Analyze this code"
```

**Result**: Uses all settings from `[profiles.review]`.

---

## Best Practices

### 1. Match Model to Task Type

- **Thinking/Design** → GPT-5.2 (general reasoning)
- **Doing/Coding** → GPT-5.2-Codex (agentic coding)

### 2. Use Safe Defaults, Override Intentionally

- Default to `read-only` unless file writing is explicitly needed
- Default to `xhigh` reasoning for all tasks (maximum capability)
- Reduce reasoning effort only for simple, quick tasks

### 3. Combine Web Search with xhigh Reasoning

For best results researching current practices:
```bash
codex exec -m gpt-5.2 -s read-only \
  --enable web_search_request \
  -c model_reasoning_effort=xhigh \
  "Research latest distributed systems patterns"
```

### 4. Request Approval for Risky Operations

Use `-a on-request` when:
- Working with production code
- Running shell commands
- Making broad changes

---

## Output Format Control

### Example 14: Save to File

**User Request**: "Analyze the codebase and save the report"

**Skill Executes**:
```bash
codex exec -m gpt-5.2-codex -s read-only \
  -c model_reasoning_effort=xhigh \
  -o analysis-report.md \
  "Analyze this codebase for issues and improvements"
```

**Feature**: `-o` saves the final message to a file.

---

### Example 15: JSON Structured Output

**User Request**: "Analyze code and return structured findings"

**Skill Executes**:
```bash
# First create a schema file
cat > /tmp/analysis-schema.json << 'EOF'
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "summary": { "type": "string" },
    "issues": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "severity": { "enum": ["critical", "high", "medium", "low"] },
          "file": { "type": "string" },
          "description": { "type": "string" }
        }
      }
    }
  }
}
EOF

# Then run with schema
codex exec -m gpt-5.2-codex -s read-only \
  -c model_reasoning_effort=xhigh \
  --output-schema /tmp/analysis-schema.json \
  "Analyze this code and return structured findings"
```

**Feature**: `--output-schema` constrains output to JSON schema.

---

### Example 16: JSONL Event Stream

**User Request**: "Stream analysis events for processing"

**Skill Executes**:
```bash
codex exec -m gpt-5.2-codex -s read-only \
  -c model_reasoning_effort=high \
  --json \
  "Analyze this codebase" > analysis-events.jsonl
```

**Feature**: `--json` outputs events as JSONL for programmatic processing.

---

### Example 17: Reasoning Summary Control

**User Request**: "Analyze with detailed reasoning explanation"

**Skill Executes**:
```bash
codex exec -m gpt-5.2 -s read-only \
  -c model_reasoning_effort=xhigh \
  -c model_reasoning_summary=detailed \
  "Explain how this algorithm works"
```

**Options**:
- `auto`: Default behavior
- `concise`: Brief reasoning summaries
- `detailed`: Comprehensive reasoning explanation
- `none`: No reasoning summary

---

## Parallel Execution

### Example 18: Batch File Analysis

**User Request**: "Analyze all service files in parallel"

**Skill Executes**:
```bash
for file in src/services/*.py; do
  codex exec -m gpt-5.2-codex -s read-only \
    -c model_reasoning_effort=high \
    -o "reports/$(basename "$file" .py).md" \
    "Analyze $file" &
done
wait
echo "All analyses complete"
```

**Feature**: Parallel execution with background processes.

---

### Example 19: Multi-Perspective Parallel Analysis

**User Request**: "Analyze from security, performance, and maintainability perspectives"

**Skill Executes**:
```bash
file="src/main.py"

codex exec -m gpt-5.2-codex -s read-only \
  -o "security.md" "Security audit of $file" &

codex exec -m gpt-5.2-codex -s read-only \
  -o "performance.md" "Performance analysis of $file" &

codex exec -m gpt-5.2 -s read-only \
  -o "maintainability.md" "Maintainability review of $file" &

wait
```

**Feature**: Multiple analyses with different perspectives in parallel.

---

## Common Patterns

### Pattern 1: Research → Design → Implement

**Phase 1 - Research** (GPT-5.2 + web search):
```bash
codex exec -m gpt-5.2 -s read-only \
  --enable web_search_request \
  -c model_reasoning_effort=xhigh \
  "Research latest authentication patterns"
```

**Phase 2 - Design** (GPT-5.2 + xhigh reasoning):
```bash
codex exec resume --last
# "Design the authentication system based on research"
```

**Phase 3 - Implement** (GPT-5.2-Codex + workspace-write):
```bash
codex exec -m gpt-5.2-codex -s workspace-write \
  -c model_reasoning_effort=xhigh \
  "Implement the authentication system we designed"
```

---

### Pattern 2: Review → Fix → Verify

**Review** (GPT-5.2 + read-only):
```bash
codex exec -m gpt-5.2 -s read-only \
  -c model_reasoning_effort=xhigh \
  "Review this code for security issues"
```

**Fix** (GPT-5.2-Codex + workspace-write):
```bash
codex exec resume --last
# "Fix the security issues identified"
```

**Verify** (GPT-5.2 + read-only):
```bash
codex exec resume --last
# "Verify the fixes are correct"
```

---

---

## Claude + Codex Collaboration

### Pattern 1: Second Opinion
Claude completes analysis, then calls Codex for GPT perspective:
```bash
codex exec -m gpt-5.2 -s read-only \
  -c model_reasoning_effort=xhigh \
  "Review Claude's analysis: [Claude's findings]. Provide your perspective."
```

### Pattern 2: Divide and Conquer
- Claude: Frontend/UI tasks
- Codex: Algorithm/performance-intensive tasks

### Pattern 3: Validate and Implement
1. Claude designs approach
2. Codex validates feasibility
3. Claude or Codex implements

---

## Concurrency Limits

- **Max parallel tasks**: 5
- Use `high` instead of `xhigh` for parallel tasks to reduce resource consumption
- Monitor API rate limits
- 3 concurrent for large files, 5 for small files
