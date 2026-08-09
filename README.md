> **This skill now lives in [AgentGameLab/skills](https://github.com/AgentGameLab/skills)**, together with our other agent skills, as one installable Claude Code plugin — `/plugin marketplace add AgentGameLab/skills`. This repo stays up and still works standalone, but new versions land there.
> **本 skill 已迁入 [AgentGameLab/skills](https://github.com/AgentGameLab/skills)** 单仓，与其余 skill 合并为一个可安装的 Claude Code plugin。本仓保留可用，但后续更新只发新仓。

# harness-evolve

> An Agent Skill (SKILL.md format) that lets an Agent workspace improve itself in one pass: track the frontier, read it, audit the system, act within risk tiers.
> 一个让 Agent workspace 自我进化的 Agent Skill（SKILL.md 格式）——一次运行走完"追踪前沿 → 精读 → 系统自检 → 分级执行"整条闭环。跑在 Claude Code 里，但不是 Claude Code 专属——任何实现了 SKILL.md 加载约定的 harness 都能用。

`harness-evolve` turns your agent's harness (its config files, routing rules, memory layout, persona docs — whatever decides its runtime behavior) from something that only gets touched when it breaks, into something that gets reviewed and improved on a schedule.

Each run does four things, in order:

1. **Outward** — search recent (≤3 day) AI agent / harness / memory-orchestration research, read the ones that clear the bar, map each to a real problem in your workspace.
2. **Inward** — self-audit the current config structure across 8 dimensions (rule redundancy, drift, backlog, regression, complexity debt, extractable skills, etc.).
3. **Risk-tiered action** — every candidate change, whether it came from research or the self-audit, goes through the same three tiers before anything touches a file:
   - **L1 auto-ship**: safe files only (logs, docs, indexes) — just do it.
   - **L2 auto-decide**: needs reversibility + scope minimization + expected value to all check out. Non-destructive changes push directly; changes to core behavior files open a draft PR for owner review instead.
   - **L3 observe-only**: DB migrations, auth/RLS, CI/CD, deleting existing rules — never touched automatically, only reported.
4. **Log** — everything lands in one append-only run log, so the next run knows what's already been tried, shipped, or shelved.

## Why merged into one skill

Earlier iterations split this into two skills — one that only researched, one that only acted — to keep research intuition from leaking into execution decisions. In practice that just added bookkeeping overhead (which skill to run, keeping two logs in sync) without adding safety, because the real safety mechanism was always the risk tiering in step 3, not the file boundary between two skills. v3.0 merges them into one flow and moves that boundary inside the pipeline: nothing skips the L1/L2/L3 gate, no matter which step it came from.

## Install

For Claude Code:

```bash
git clone https://github.com/AgentGameLab/harness-evolve.git ~/.claude/skills/harness-evolve
```

Claude Code auto-discovers the skill from its `SKILL.md` front-matter. Any other harness that reads the same SKILL.md convention (OpenClaw included) can load it from its own skills directory the same way.

## Use

First run reads your project's config files to figure out what "safe to touch directly" vs "needs review" vs "hands off" means for your workspace — if it can't tell, it asks instead of guessing.

```
运行一次 harness evolve
跑一次自进化，检查一下系统有没有能优化的地方
```

## Fits any framework with config + logs

OpenClaw · Claude Code Agent · LangGraph · AutoGPT · your own agent framework — anything with a notion of "files that decide behavior" and "a place runs get logged" works.

## Layout

```
harness-evolve/
├── SKILL.md                              # the skill: full 8-step pipeline + risk tiers
└── references/
    └── cross-discipline-lattice.md       # opt-in cross-discipline lookup table, loaded on demand
```

## License

MIT — see [LICENSE](LICENSE).
