---
title: "Most of the gain came before reinforcement learning started"
date: 2026-11-04
description: "The Axion 1.0 paper scored its model after each training step. Most of the climb came before the reinforcement learning step, but that step still moved some scores."
canonical_url: https://harxsan.github.io/2026/11/04/axion-1-0-much-of-the-gain-shows-up-before-reinforcement-learning-starts/
image: /assets/axion-1-0-much-of-the-gain-shows-up-before-reinforcement-learning-starts.png
---

Before you build a reinforcement learning step for your AI agent, look at what the earlier training steps already gave it. That is the practical lesson from Axion 1.0, an open recipe for an agent that searches the web and runs Python.

![Hand-drawn bar chart. The bigger Axion 1.0 model's score on 130 hard exam questions: 40.8% at the start, 68.5% after stage I, 68.5% after stage II, 74.6% after reinforcement learning. Most of the climb is the first step.](/assets/axion-1-0-much-of-the-gain-shows-up-before-reinforcement-learning-starts.png)

## What the recipe is

Axion 1.0 trains in three steps. The first two are rounds of fine-tuning, where the model studies worked examples. The third is reinforcement learning, where the model practises on tasks and is rewarded for good results. Stage I trains the model to reason before it has to retrieve outside information. Stage II starts from the Stage I result with the same settings.

The authors release the whole recipe, including data, prompts and training code. Their stated idea is that simplifying the training pipeline does not require simplifying the learning problem.

## What the scores show

The paper scored the model after each step on a 130-question subset of the HLE benchmark, a set of hard exam questions. These scores do not compare directly with the paper's headline results.

The bigger model, called 122B, went from 40.8% to 68.5% after the first step. It stayed at 68.5% after the second step but reached 74.6% after reinforcement learning. The smaller model, called 35B, moved in smaller steps: 47.7% to 59.2%, then 66.2%, then 70.0%. In both models, most of the climb came before the reinforcement learning step.

## Where reinforcement learning helped and where it did not

Reinforcement learning still moved some scores, but not all of them. On a benchmark called SEAL-0, it took the 35B model from 53.2% to 62.2%. On another, FrontierScience Research, it lowered the 35B model from 58.3% to 56.7% while raising the 122B model from 65.0% to 66.7%. So the last step helped on some tests and hurt on one.

## What this does and does not show

These are snapshots from one training run. The paper itself calls them "sequential checkpoint comparisons" that are "not component-removal ablations". They show when scores moved, not what caused the change.

Each score is also the best result from a single run, not an average. The 35B and 122B models start from different base models and both go through reinforcement learning, so the paper says not to read them as a clean test of model size. The small 4B and 9B versions only got the two fine-tuning steps, so they say nothing about reinforcement learning at that size.

## What it took

For the bigger model, the two fine-tuning steps together took 7 hours 30 minutes, or 240 GPU-hours. For the smaller model it was about 90 GPU-hours. All fine-tuning runs used 32 GPUs. The reinforcement learning setup for the bigger model used ten rollout engines on 40 GPUs and a 16-GPU trainer, plus a separate service node. The paper gives no run time for that step, so this post does not compare the two costs.

## If you want to copy it

Run the two fine-tuning steps first and measure them on your own tasks. Then decide whether the reinforcement learning step is worth building.

I would check each benchmark separately before keeping reinforcement learning, since here it helped on some and lowered one.

Source: the Axion 1.0 paper on arXiv (https://arxiv.org/html/2611.03233).
