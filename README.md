# Compound Engineering Plugin (Personal Fork)

A Claude Code plugin that makes each unit of engineering work easier than the last.

**This is a personal fork** with Codex (GPT-5.2) integration embedded into existing workflows.

## Install

This is a private repository. Clone directly:

```bash
git clone https://github.com/rickieplin/compound-engineering-plugin.git ~/.claude/plugins/marketplaces/every-marketplace
```

## What's Different from Upstream

All review and research agents are enhanced with **Codex delegation**:
- Agents intelligently decide when to handle directly vs delegate to Codex
- Codex handles: algorithmic complexity, OWASP scanning, pattern detection, code metrics
- Claude handles: context-dependent analysis, Rails-specific patterns, multi-file exploration
- **A/B Testing**: Critical findings run both Claude and Codex for cross-validation

## Workflow

```
Plan → Work → Review → Compound → Repeat
```

| Command | Purpose |
|---------|---------|
| `/workflows:plan` | Turn feature ideas into detailed implementation plans |
| `/workflows:work` | Execute plans with worktrees and task tracking |
| `/workflows:review` | Multi-agent code review (with Codex delegation) |
| `/workflows:compound` | Document learnings to make future work easier |

## Syncing with Upstream

To pull updates from the original repository:

```bash
cd ~/.claude/plugins/marketplaces/every-marketplace
git fetch upstream
git merge upstream/main
git push origin main
```

## Philosophy

**Each unit of engineering work should make subsequent units easier—not harder.**

Traditional development accumulates technical debt. Every feature adds complexity. The codebase becomes harder to work with over time.

Compound engineering inverts this. 80% is in planning and review, 20% is in execution:
- Plan thoroughly before writing code
- Review to catch issues and capture learnings
- Codify knowledge so it's reusable
- Keep quality high so future changes are easy

## Learn More

- [Full component reference](plugins/compound-engineering/README.md) - all agents, commands, skills
- [Compound engineering: how Every codes with agents](https://every.to/chain-of-thought/compound-engineering-how-every-codes-with-agents)
- [The story behind compounding engineering](https://every.to/source-code/my-ai-had-already-fixed-the-code-before-i-saw-it)

---

*Forked from [EveryInc/compound-engineering-plugin](https://github.com/EveryInc/compound-engineering-plugin)*
