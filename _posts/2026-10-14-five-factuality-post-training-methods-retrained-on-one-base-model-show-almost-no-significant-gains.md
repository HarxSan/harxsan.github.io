---
title: "Five factuality post-training methods, retrained on one base model, show almost no significant gains"
date: 2026-10-14
description: "Five published factuality post-training methods were retrained on one shared base model, Llama-3.1-8B-Instruct, and scored on one common benchmark. None of them significantly outperformed the untraine"
canonical_url: https://harxsan.github.io/2026/10/14/five-factuality-post-training-methods-retrained-on-one-base-model-show-almost-no-significant-gains/
image: /assets/five-factuality-post-training-methods-retrained-on-one-base-model-show-almost-no-significant-gains.png
---

Five published factuality post-training methods were retrained on one shared base model, Llama-3.1-8B-Instruct, and scored on one common benchmark. None of them significantly outperformed the untrained base model, apart from two long-form cases. One of those exceptions is an artifact of very brief answers, and the other is a small gain after relevance filtering.

The study is a reproduction of five factuality methods that include Faith, FactGRPO and Flame. Its authors also found that none of the methods reproduces its published results in full.

## Why it matters

If you choose a factuality method from the tables in its paper, you may end up with a model that answers more briefly, pads answers with off-topic claims, or gets worse on short questions. Faith and FactGRPO lose about 25 and 18 points of TruthfulQA accuracy against the base model. Of FactGRPO's biography responses, 36% contain claims that are supported by evidence but irrelevant to the prompt.

## The setup

The original papers are hard to compare with each other. None of them shares a model or dataset with all the others, three report precision-like metrics, one credits refusals and one excludes them.

So the authors trained every method on Llama-3.1-8B-Instruct (Grattafiori et al., 2024), with the training regime and data that its own paper specifies. Each alignment stage is a LoRA adapter of rank 32 (Hu et al., 2022), placed on top of the method's own supervised stage and trained with its paper's hyperparameters.

The common benchmark has two long-form generation tasks scored with VeriScore (Song et al., 2024) and two short-form question answering tasks graded by an LLM judge. VeriScore is a claim-level scorer. It splits a long answer into short factual claims, checks each claim against retrieved evidence, and counts how many are supported. Precision is the share of claims that are supported. F1@K combines that precision with how many supported claims the answer contains, measured against a target count K. A relevance-filtered score counts only the claims that address the prompt.

Each method is compared with the untrained base model on the same four datasets. The authors apply Holm's correction for multiple comparisons. Holm's correction raises the bar for significance when many tests run at once, so that a few lucky differences are not counted as wins.

## The complication

Each method arrives with published results. Under each method's own protocol, all methods except Faith and FactGRPO follow a similar trend to their original implementations, but none reproduces its published results in full. Faith and FactGRPO do not follow a similar trend.

On the common benchmark, the published gains mostly vanish. On closer inspection, the authors find that the rewards optimise for format, length or brevity rather than factuality and relevance. The same analysis finds that the choice between on-policy and offline training matters little.

## The evidence

Start with the short-form tasks. Faith and FactGRPO lose about 25 and 18 points of TruthfulQA accuracy. The other three methods stay within two points of the base model, which is not statistically significant. The judge shapes the absolute numbers here, since regrading with gpt-oss-120b changes up to a third of the TruthfulQA labels, but the large drops of FactGRPO and Faith persist.

Accuracy on its own can hide how differently these models behave. On SimpleQA, Flame raises its accuracy over attempted questions by declining more often, but its overall accuracy stays unchanged.

Neither of the two long-form exceptions holds up well. Faith has the highest precision on the biography task. The authors show that this is an artifact of 0.4 supported claims in a one-sentence, very brief response, which is why its relevance-filtered precision is significantly above the base model. A model that says very little has little room to be wrong.

FactGRPO is the other exception. Its relevance-filtered biography F1@K is 0.342, against 0.325 for the base model, and the difference is marked significant. But its reward counts a claim as correct whenever evidence supports it, even if the claim is irrelevant to the prompt. Of its biography responses, 36% contain such claims.

![Five factuality methods retrained the same way on one open model. On TruthfulQA, a test of common misconceptions, Faith and FactGRPO got about 25 and 18 fewer answers right per 100 questions than the starting model; the other three moved 2 or fewer. The training rewarded how answers looked and how long they were more than whether they were true; Faith's best score came from one-sentence answers.](/assets/five-factuality-post-training-methods-retrained-on-one-base-model-show-almost-no-significant-gains.png)

The chart shows that FactGRPO's one significant long-form gain is small.

The verifier matters as much as the judge. Re-verifying biography claims against the subject's Wikipedia article lowers precision by about twenty points for methods that produce long responses.

A separate sensitivity check leaves the order of the methods almost unchanged, but FactGRPO's one significant gain no longer survives the paired test.

Two further results are worth knowing. The authors report refusal rates of 0.000 on three datasets and 0.0003 on the fourth, even though 17% to 56% of questions are classified as unknown. In one reproduction, 400 steps of Dr. GRPO left the reward flat, and precision rose slightly while the supported-claim count fell, against the published gains in both.

## Limits

The common evaluation uses one 8B model with LoRA adapters, four English datasets, and smaller training budgets than the original papers. Results may differ with larger models, longer training or full fine-tuning. Each method was trained once, so variance across random seeds was not measured.

The Dr. GRPO run used a tenth of the published optimisation, so the authors say it bounds a tenth of the budget rather than the method. The short-form judge decides the absolute numbers. The authors also note that the lack of improvement on these metrics does not rule out factuality gains measured in other ways.

## Takeaway

Before adopting a factuality method, check its answer length, claims per answer, refusal rate and claim relevance. Then check whether its gain over the untrained base model survives a different verifier or judge.

The more useful lesson here is about evaluation: a factuality score means little until a different verifier or judge agrees with it.