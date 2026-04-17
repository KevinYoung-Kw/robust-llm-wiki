# Quickstart

[中文](./README.zh-CN.md) | **English**

This quickstart is designed around two real entry paths:

1. you already have an LLM-Wiki and want to import the operating lessons from this repo,
2. you do not have an LLM-Wiki yet and want an agent to bootstrap one from this framework.

## Path A: You Already Have An LLM-Wiki

If you already maintain your own wiki, the goal is not to copy this repository wholesale. The goal is to let your agent absorb the operating rules in [schema/robust-llm-wiki-CLAUDE.md](../schema/robust-llm-wiki-CLAUDE.md) and adapt them to your existing implementation.

Recommended flow:

1. clone this repository or give your agent the repository URL,
2. tell the agent to read [schema/robust-llm-wiki-CLAUDE.md](../schema/robust-llm-wiki-CLAUDE.md),
3. ask it to compare that guide against your current wiki structure,
4. have it propose only the rules that fit your current system instead of forcing a full rewrite.

Use a prompt like this:

```text
Read robust-llm-wiki's schema/robust-llm-wiki-CLAUDE.md.
Compare it against my current LLM-Wiki implementation.
Do not force a rewrite.
Instead:
1. identify which rules already fit my system,
2. identify which rules need adaptation,
3. propose a minimal migration plan,
4. draft an agent operating file for my repo that preserves my current architecture where possible.
```

In this path, the right output is usually not "copy their files." The right output is "translate the robust operating rules into my repo's own language and folder structure."

## Path B: You Do Not Have An LLM-Wiki Yet

If you do not have an LLM-Wiki yet, the fastest path is even simpler: use this repository as the policy source and ask your coding agent to generate the right agent instruction file for its own ecosystem.

The important idea is:

1. this repo provides the operating pattern,
2. your agent converts that pattern into the control file it understands best.

Use a one-shot prompt like this:

```text
Read robust-llm-wiki's schema/robust-llm-wiki-CLAUDE.md.
Based on it, generate the agent operating file for my environment.
If I use Claude Code, generate CLAUDE.md.
If I use Codex or a generic coding agent workflow, generate AGENTS.md.
If I use Gemini, generate GEMINI.md.
Keep the core operating ideas, but adapt names, commands, and conventions to the target agent.
```

That is enough to get a usable first version.

## Read Next

1. [schema/README.md](../schema/README.md)
2. [schema/SPEC.md](../schema/SPEC.md)
3. [schema/robust-llm-wiki-CLAUDE.md](../schema/robust-llm-wiki-CLAUDE.md)
