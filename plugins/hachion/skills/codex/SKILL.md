---
name: codex
version: 5.0.0
description: Invoke Codex CLI for complex coding tasks requiring high reasoning capabilities. This skill enables Claude agents to delegate specific analysis tasks to Codex, leveraging its strengths in pattern recognition, algorithmic analysis, and batch processing. Supports delegation mode and A/B testing for cross-validation.
---

# Codex: High-Reasoning AI Assistant

## Overview

Codex is a powerful tool that Claude agents can use to enhance their capabilities. This skill defines **when** and **how** to delegate tasks to Codex for optimal results.

### Key Principle

> **Claude leads, Codex assists.** Claude maintains full context, tool access, and multi-turn reasoning. Codex is invoked for specific, well-defined analysis tasks where its strengths shine.

---

## Model Selection (2026 Latest)

### Recommended Models

| Model | Best For | Cost | Reasoning |
|-------|----------|------|-----------|
| `gpt-5.2-codex` | Complex analysis, real-world engineering | High | xhigh support |
| `gpt-5.1-codex-max` | Long-horizon tasks, deep debugging | Medium-High | xhigh support |
| `gpt-5.1-codex-mini` | Simple tasks, batch processing | Low | high support |
| `gpt-5.2` | General cross-domain tasks | Medium | xhigh support |

### Model Selection Guide

```
Task Complexity?
├── Complex (security audit, architecture review)
│   └── Use: gpt-5.2-codex + xhigh reasoning
├── Long-running (multi-file refactor, deep debug)
│   └── Use: gpt-5.1-codex-max + xhigh reasoning
├── Simple (code metrics, pattern scan)
│   └── Use: gpt-5.1-codex-mini + high reasoning
└── General (research, comparison)
    └── Use: gpt-5.2 + high reasoning
```

### Quick Reference

| Task Type | Model | Sandbox | Reasoning |
|-----------|-------|---------|-----------|
| Security audit | `gpt-5.2-codex` | read-only | xhigh |
| Performance analysis | `gpt-5.2-codex` | read-only | xhigh |
| Code editing | `gpt-5.2-codex` | workspace-write | high |
| Batch scanning | `gpt-5.1-codex-mini` | read-only | high |
| Research | `gpt-5.2` | read-only | high |

**Critical**: Always use `codex exec` (NOT plain `codex`)

---

## Delegation Modes

### Mode 1: Single Analysis (codex-analyze)

Delegate a specific, self-contained analysis task to Codex.

```bash
# For complex analysis (default)
codex exec -m gpt-5.2-codex -s read-only -c model_reasoning_effort=xhigh "
[ANALYSIS TASK]

CODE:
[code content]
"

# For simpler tasks (cost-efficient)
codex exec -m gpt-5.1-codex-mini -s read-only -c model_reasoning_effort=high "
[SIMPLE TASK]

CODE:
[code content]
"
```

**Best for:**
- Algorithmic complexity analysis (Big O)
- Security vulnerability scanning (OWASP patterns)
- Code smell detection
- Design pattern recognition
- Dependency analysis

### Mode 2: A/B Testing (codex-compare)

Run both Claude and Codex analysis, then compare and merge results.

**Protocol:**
```markdown
1. Claude analyzes first (uses full context, tools, multi-turn reasoning)
2. Codex analyzes independently (single-shot, fresh perspective)
3. Claude compares results:
   - Merge consistent findings (high confidence)
   - Investigate conflicts (arbitrate with reasoning)
   - Report unique findings from each
```

**Example workflow:**
```
Claude Analysis:
  Found: SQL injection at line 45, N+1 query at line 78

Codex Analysis:
  Found: SQL injection at line 45, XSS at line 92, hardcoded secret at line 15

Merged Results:
  - SQL injection at line 45 (both found - HIGH confidence)
  - N+1 query at line 78 (Claude only - MEDIUM confidence, context-dependent)
  - XSS at line 92 (Codex only - verify with context)
  - Hardcoded secret at line 15 (Codex only - verify with context)
```

### Mode 3: Batch Processing (codex-batch)

Process multiple files or large codebases efficiently using cost-effective model.

```bash
# Use mini model for batch processing (cost-efficient)
for file in src/*.ts; do
  codex exec -m gpt-5.1-codex-mini -s read-only -c model_reasoning_effort=high "
  Analyze this file for security issues:
  $(cat $file)
  " -o "reports/$(basename $file).md"
done
```

---

## Delegation Decision Guide

### When to Delegate to Codex

| Scenario | Delegate? | Model | Reasoning |
|----------|-----------|-------|-----------|
| Complex security audit | YES | gpt-5.2-codex | xhigh |
| Algorithm complexity analysis | YES | gpt-5.2-codex | xhigh |
| OWASP security scan | YES | gpt-5.2-codex | xhigh |
| Code smell detection | YES | gpt-5.1-codex-mini | high |
| Large file batch processing | YES | gpt-5.1-codex-mini | high |
| Design pattern identification | YES | gpt-5.2-codex | high |
| Long debugging session | YES | gpt-5.1-codex-max | xhigh |

### When Claude Should Handle Directly

| Scenario | Why Claude? |
|----------|-------------|
| Rails-specific best practices | Deep domain knowledge |
| Multi-file context analysis | Tool access, exploration |
| Business logic review | Requires understanding intent |
| Follow-up questions needed | Multi-turn interaction |
| Git history analysis | Tool access required |
| DHH/37signals style review | Specialized knowledge |

### Decision Flowchart

```
Is this a well-defined, single-shot analysis task?
├── YES → Does it require multi-file exploration?
│         ├── YES → Claude handles (uses tools)
│         └── NO → Can Codex answer without follow-up?
│                   ├── YES → Select model by complexity:
│                   │         ├── Complex → gpt-5.2-codex
│                   │         ├── Long-running → gpt-5.1-codex-max
│                   │         └── Simple → gpt-5.1-codex-mini
│                   └── NO → Claude handles
└── NO → Claude handles (needs interaction)
```

---

## Codex Strengths to Leverage

### 1. Complex Security Audit
```bash
codex exec -m gpt-5.2-codex -s read-only -c model_reasoning_effort=xhigh "
Perform comprehensive security audit:
1. OWASP Top 10 vulnerabilities
2. Hardcoded secrets detection
3. Authentication/authorization flaws
4. Input validation issues

For each vulnerability:
- Category and severity
- Location (file:line)
- Exploitation scenario
- Remediation

CODE:
$(cat src/controllers/*.rb)
"
```

### 2. Algorithmic Analysis
```bash
codex exec -m gpt-5.2-codex -s read-only -c model_reasoning_effort=xhigh "
Analyze time and space complexity:
- Big O for best, average, worst cases
- Memory allocation patterns
- Optimization opportunities
- Projected performance at 10x, 100x, 1000x scale

CODE:
$(cat algorithm.py)
"
```

### 3. Batch Code Quality Scan (Cost-Efficient)
```bash
codex exec -m gpt-5.1-codex-mini -s read-only -c model_reasoning_effort=high "
Calculate code quality metrics:
- Cyclomatic complexity per function
- Cognitive complexity
- Lines of code per function
- Nesting depth

Flag functions with:
- Cyclomatic complexity > 10
- More than 50 lines

CODE:
$(cat service.ts)
"
```

### 4. Long-Running Debug Session
```bash
codex exec -m gpt-5.1-codex-max -s read-only -c model_reasoning_effort=xhigh "
Deep debugging analysis:
- Trace execution flow
- Identify race conditions
- Memory leak detection
- State mutation tracking

CODE:
$(cat complex_module.py)
"
```

---

## A/B Testing Protocol

### Step 1: Claude First Pass
```markdown
I'll analyze this code for [specific concern].

[Claude's analysis using full context and tools]

Findings:
1. ...
2. ...
```

### Step 2: Codex Independent Analysis
```bash
codex exec -m gpt-5.2-codex -s read-only -c model_reasoning_effort=xhigh "
Analyze this code for [same concern]. Be thorough and systematic.

CODE:
[same code]
"
```

### Step 3: Comparison and Merge
```markdown
## A/B Testing Results

### Consistent Findings (High Confidence)
- [Finding both identified] - CONFIRMED

### Claude-Only Findings
- [Finding] - Reason: [why Codex might have missed, or false positive check]

### Codex-Only Findings
- [Finding] - Verification: [check with context/tools]

### Conflicts
- Claude said X, Codex said Y
- Arbitration: [reasoned decision]

### Final Merged Report
[Consolidated findings with confidence levels]
```

---

## Core Command Patterns

### Complex Analysis (Default)
```bash
codex exec -m gpt-5.2-codex -s read-only -c model_reasoning_effort=xhigh "prompt"
```

### Cost-Efficient Batch
```bash
codex exec -m gpt-5.1-codex-mini -s read-only -c model_reasoning_effort=high "prompt"
```

### Long-Running Tasks
```bash
codex exec -m gpt-5.1-codex-max -s read-only -c model_reasoning_effort=xhigh "prompt"
```

### With File Editing
```bash
codex exec -m gpt-5.2-codex -s workspace-write -c model_reasoning_effort=high "edit prompt"
```

### Resume Previous Session
```bash
codex exec resume --last
codex exec resume <session-id>
```

### Code Review
```bash
codex exec review --uncommitted
codex exec review --base main
codex exec review --commit abc123
```

### With Output File
```bash
codex exec -m gpt-5.2-codex -s read-only -c model_reasoning_effort=xhigh "prompt" -o output.md
```

---

## Reasoning Effort Guide

| Level | Use Case | Token Cost | Models |
|-------|----------|------------|--------|
| `xhigh` | Complex analysis, security audit | Highest | gpt-5.2-codex, gpt-5.1-codex-max |
| `high` | Standard analysis, batch tasks | High | All models |
| `medium` | Simple tasks | Medium | All models |
| `low` | Trivial operations | Low | All models |

**Recommendations:**
- Use `xhigh` for delegated analysis tasks (quality over cost)
- Use `high` for batch processing with mini model
- Avoid `medium`/`low` for analysis tasks

---

## Error Handling

### Common Issues

| Error | Solution |
|-------|----------|
| "stdout is not a terminal" | Use `codex exec`, not `codex` |
| Model unavailable | Try alternative model |
| Not authenticated | Run `codex login` |
| Timeout | Use `gpt-5.1-codex-max` for long tasks |

### Model Fallback Protocol
```bash
# Primary: gpt-5.2-codex
codex exec -m gpt-5.2-codex -s read-only -c model_reasoning_effort=xhigh "prompt"

# Fallback 1: gpt-5.1-codex-max (for long-running)
codex exec -m gpt-5.1-codex-max -s read-only -c model_reasoning_effort=xhigh "prompt"

# Fallback 2: gpt-5.2 (general)
codex exec -m gpt-5.2 -s read-only -c model_reasoning_effort=xhigh "prompt"
```

---

## Integration with Claude Agents

### For Agent Authors

Add this section to your agent's prompt to enable Codex delegation:

```markdown
## Codex Delegation

You can delegate specific analysis tasks to Codex:

### For Complex Analysis
```bash
codex exec -m gpt-5.2-codex -s read-only -c model_reasoning_effort=xhigh "
[task description]

CODE:
[code to analyze]
"
```

### For Simple/Batch Tasks
```bash
codex exec -m gpt-5.1-codex-mini -s read-only -c model_reasoning_effort=high "
[task description]

CODE:
[code to analyze]
"
```

### Delegate to Codex when:
- [List specific scenarios for this agent]

### Handle directly when:
- [List scenarios requiring your expertise]

### A/B Testing
For critical findings, run both your analysis and Codex analysis, then compare.
```

---

## Key Flags Reference

| Flag | Purpose |
|------|---------|
| `-m MODEL` | Model selection (`gpt-5.2-codex`, `gpt-5.1-codex-max`, `gpt-5.1-codex-mini`, `gpt-5.2`) |
| `-s SANDBOX` | `read-only`, `workspace-write`, `danger-full-access` |
| `-c key=value` | Config override (e.g., `model_reasoning_effort=xhigh`) |
| `--enable FEATURE` | Enable feature |
| `-o FILE` | Save output to file |
| `--add-dir DIR` | Additional writable directory |
| `--full-auto` | Workspace-write with auto-approval |
| `-C DIR` | Change working directory |
| `--json` | Output as JSONL |
| `--output-schema FILE` | Structured output schema |

---

## References

For detailed patterns, see `references/`:
- `codex-help.md` - Full CLI reference
- `codex-config.md` - Configuration options

Sources:
- [Codex Models](https://developers.openai.com/codex/models/)
- [Introducing GPT-5.2-Codex](https://openai.com/index/introducing-gpt-5-2-codex/)
- [GPT-5.1-Codex-Max](https://openai.com/index/gpt-5-1-codex-max/)
