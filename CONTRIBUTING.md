# Contributing Guide

[中文](./CONTRIBUTING.zh-CN.md) | **English**

Thank you for contributing to Robust LLM-Wiki.

This project is a practice framework repository. The goal is to preserve methods, standards, and reusable workflows, not personal content pages.

## 1. Contribution Scope

Welcome contributions to:

1. `research/`: verifiable data, survey findings, case summaries
2. `schema/`: standards, red lines, execution guidance
3. cross-document consistency and workflow governance clarifications

Not recommended:

1. personal content pages unrelated to project goals
2. large-scale style rewrites without explanation
3. scripted "hard templates" introduced without discussion

## 2. Contribution Order (Important)

Default order:

1. update `schema/details/*` first
2. then sync `schema/SPEC.md`
3. finally run terminology and boundary consistency checks

## 3. Pre-PR Checklist

1. Keep Karpathy's kernel: Wiki form, double-link, ingest/query/lint loop
2. Use plain language: explain "why" before "how"
3. Apply strict anonymization (no real names, handles, accounts, websites, or identifiable clues; use labels like Entity-A/B)
4. Provide sources and evaluation scope (especially for model benchmark data)
5. Avoid turning recommendations into one-size-fits-all hard rules

## 4. Suggested PR Structure

In your PR description, include:

1. motivation (Why)
2. key changed files
3. scope impact (which standards/workflows are affected)
4. remaining risks or TODOs

## 5. Discussion and Proposals

For major changes (new standard sections, SPEC restructuring, or key definition rewrites), open an issue/discussion first, then submit a PR.
