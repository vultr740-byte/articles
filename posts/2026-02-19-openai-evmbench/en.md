---
title: "Introducing EVMbench (summary + notes)"
---

OpenAI introduced **EVMbench**, a benchmark for evaluating how well AI agents can work on **high-severity EVM smart contract vulnerabilities**.

---

## What is EVMbench?

EVMbench is designed to measure agent capability in an **economically meaningful** environment: smart contracts that secure large amounts of value.

It evaluates three distinct modes:

1) **Detect** — audit a smart contract codebase and identify known vulnerabilities (scored against ground-truth findings and rewards).
2) **Patch** — fix the vulnerabilities while preserving intended functionality (verified by tests and exploit checks).
3) **Exploit** — execute an end-to-end attack (e.g., draining funds) against deployed contracts in a sandboxed chain environment, graded programmatically.

---

## Dataset / task construction (high level)

- **120 curated vulnerabilities** drawn from **40 audits**, mostly from open audit competitions.
- Includes scenarios inspired by the security auditing process for **Tempo**, a payments-oriented L1.
- Environments use adapted or hand-written exploit PoCs and deployment scripts.
- For exploit mode, the harness replays transactions deterministically and restricts unsafe RPC methods.

---

## Results (as stated in the post)

OpenAI reports that frontier agents improve significantly in **exploit** mode.

- In **exploit**, *GPT‑5.3‑Codex via Codex CLI* scores **72.2%**.
- A previous model (*GPT‑5*) scores **31.9%** (released ~6 months earlier).

The post also notes that **detect recall** and **patch success** remain meaningfully below full coverage.

---

## Interpreting the behavior gap

A key takeaway is that agents do best when the objective is concrete and easily verifiable:

- **Exploit** tasks have a crisp terminal condition (“funds drained”), which encourages iterative search.
- **Detect** can fail due to premature stopping (“found one issue, done”).
- **Patch** remains hard because it demands both security and functional correctness.

---

## Limitations (important)

EVMbench is not “the full real world”:

- Ground-truth detection is based on what human auditors found; additional findings aren’t easily scored as true vs false positives.
- Exploit grading replays transactions sequentially; timing-dependent behaviors are out of scope.
- The chain is a clean local environment (Anvil), not a mainnet fork; some scenarios rely on mocks.

---

## Why it matters

The post frames smart contract security as a **dual-use** domain: stronger agents can help defenders, but also enable attackers.

EVMbench is positioned both as:

- a measurement tool for tracking capability/risk, and
- a prompt for the ecosystem to adopt AI-assisted auditing defensively.

---

## My notes / implications

- If the benchmark reward structure makes “exploit” easiest, it suggests we should expect **attack capabilities to outpace defensive auditing** unless “detect/patch” loops are engineered to be exhaustive.
- For product design: defensive tools likely need strong scaffolding around **coverage, stopping criteria, and regression tests**.

---

Reference: [OpenAI — Introducing EVMbench](https://openai.com/index/introducing-evmbench/)
