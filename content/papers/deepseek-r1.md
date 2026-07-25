+++
title       = "DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning"
date        = 2025-01-22
draft       = false
type        = "blog"
slug        = "deepseek-r1"
description = "Notes on DeepSeek-R1, a paper on training reasoning capabilities in LLMs using reinforcement learning."
tags        = ["LLM", "reinforcement-learning", "reasoning"]
link        = "https://arxiv.org/abs/2501.12948"
+++

**Link:** [arxiv.org/abs/2501.12948](https://arxiv.org/abs/2501.12948)

---

## Abstract

DeepSeek-R1 shows that strong reasoning capabilities in large language models can emerge from pure reinforcement learning — no human-labeled reasoning traces required. The resulting model matches OpenAI's o1 on a range of benchmarks, and the learned reasoning patterns can be distilled into much smaller models that still punch well above their weight.

---

## Background

Getting LLMs to reason reliably has mostly meant one thing: more human supervision. Chain-of-thought prompting helps, but it requires annotated examples. RLHF aligns models to human preferences, but again depends on human feedback. OpenAI's o1 demonstrated that models could "think" before answering using extended reasoning chains — but the training details were never released.

The central question this paper addresses: *can reasoning emerge from RL alone, without human-curated reasoning trajectories?*

---

## Problem Statement

Supervised fine-tuning on human demonstrations scales poorly for reasoning. Humans are not great at writing out long, careful reasoning chains at the scale needed to train frontier models. The process is expensive, hard to verify, and may impose human reasoning patterns rather than letting better strategies emerge.

The authors want a training signal that is:

- **Verifiable**: right or wrong answers, not subjective quality
- **Scalable**: no human annotation per example
- **Expressive enough** to produce complex, multi-step reasoning

---

## Approach

### DeepSeek-R1-Zero: Pure RL from Scratch

The starting point is DeepSeek-V3-Base (a pre-trained model, no SFT). They apply **GRPO** (Group Relative Policy Optimization) — a variant of PPO that avoids training a separate critic by using within-group reward comparisons.

The reward signal is intentionally simple:

- **Accuracy reward**: does the final answer match the ground truth?
- **Format reward**: does the model use the expected `<think>...</think>` and `<answer>...</answer>` tags?

No process reward, no human preference labels.

What emerges is striking. The model spontaneously develops **self-reflection** (revisiting its own reasoning), **backtracking** (abandoning unproductive paths), and **verification** (checking its own answer before committing). These were not trained in — they arose from optimizing for correctness alone.

One observed behavior during training is what the authors call an "aha moment": the model learning to pause and reconsider mid-reasoning when something looks wrong. This appears to be a genuine emergent property.

### DeepSeek-R1: Fixing the Rough Edges

R1-Zero, while impressive, has practical problems: mixed-language outputs, poor readability, and unstable training early on. DeepSeek-R1 adds a **four-stage pipeline** to fix this:

1. **Cold start with long CoT data** — A small set of high-quality, long chain-of-thought examples bootstraps the model into a readable reasoning format before RL begins.
2. **Reasoning-focused RL** — Same GRPO-based RL as R1-Zero, but starting from a better baseline.
3. **Rejection sampling + supervised fine-tuning** — Sample many outputs from the RL checkpoint, keep only the good ones, and fine-tune to consolidate both reasoning and general instruction-following.
4. **Full RL** — A final RL stage across all task types, using a mix of accuracy rewards and human-preference signals for non-verifiable tasks.

This pipeline yields a model that is coherent, consistent in language, and competitive with o1.

### Distillation

One of the more practically useful results: the reasoning patterns from DeepSeek-R1 can be distilled into smaller models (1.5B to 70B parameters) via standard SFT on R1's outputs. The distilled 7B and 14B models outperform much larger models that weren't trained this way, and the 32B distilled model rivals GPT-4o on several benchmarks.

---

## Results

| Benchmark     | DeepSeek-R1 | OpenAI o1 |
|---------------|-------------|-----------|
| AIME 2024     | 79.8%       | 79.2%     |
| MATH-500      | 97.3%       | 96.4%     |
| LiveCodeBench | 65.9%       | 63.4%     |
| MMLU          | 90.8%       | 91.8%     |
| GPQA Diamond  | 71.5%       | 75.7%     |

DeepSeek-R1 matches or exceeds o1 on most math and code benchmarks. It falls slightly behind on knowledge-heavy tasks like GPQA Diamond.

The distilled models are also notable:

- **R1-Distill-Qwen-7B** outperforms QwQ-32B-Preview despite being 4.5x smaller
- **R1-Distill-Qwen-32B** matches o1-mini on several benchmarks

---

## Conclusion

The main takeaway is that reasoning is something that can be *incentivized*, not just *imitated*. Given a verifiable reward signal and enough compute, a model will develop sophisticated reasoning strategies that no human explicitly taught it.

A few things worth noting:

- **RL alone is not enough** for production quality — the cold start data and multi-stage pipeline matter
- **Distillation is remarkably effective** — small models trained on R1 outputs outperform models trained from scratch on standard data
- The **verifiability of the reward signal** is the load-bearing constraint. Math and code work because right/wrong is clear. Extending this to more open-ended tasks is still an open problem.

DeepSeek-R1 also matters as a statement about openness. The weights are released, the training methodology is described in detail, and the results are competitive with closed systems. That's a useful data point about where open research currently sits relative to frontier labs.
