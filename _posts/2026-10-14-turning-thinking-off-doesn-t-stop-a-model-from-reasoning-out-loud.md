---
title: "Turning thinking off doesn't stop a model from reasoning out loud"
date: 2026-10-14
description: "With thinking disabled, DeepSeek-V4-Flash (284B) still writes out visible reasoning in 99.9% of its open-ended responses. The paper behind that number draws a general conclusion from it: explicit no-t"
canonical_url: https://harxsan.github.io/2026/10/14/turning-thinking-off-doesn-t-stop-a-model-from-reasoning-out-loud/
image: /assets/turning-thinking-off-doesn-t-stop-a-model-from-reasoning-out-loud.png
---

With thinking disabled, DeepSeek-V4-Flash (284B) still writes out visible reasoning in 99.9% of its open-ended responses. The paper behind that number draws a general conclusion from it: explicit no-think controls cannot reliably remove visible reasoning from what a model writes.

## Why it matters

If you switch thinking off, you probably expect short answers at lower latency and token cost. But if reasoning text shows up anyway, the short-answer half of that plan has already failed. The cost half is shaky too. The authors warn that a response that looks answer-only should not be read as cheaper inference, because hidden reasoning may still be running.

## The setup

The authors split every response into a pre-answer trace and a final answer, and then score it at three levels.

The models tested include DeepSeek-V4-Flash and GPT-5.4. The study runs four models through APIs, plus Qwen3-4B and Qwen3-32B locally. For the four API models, each mode uses 1,000 sampled items from each of BoolQ, MMLU, MMLU-Pro, GSM8K and MATH, plus all 687 StrategyQA items. The local Qwen3 runs use the full benchmark splits.

Every mode gets the same 8,192-token response budget, so a shorter cap in no-think mode can't explain the differences in output. The "minimal" setting is treated as near-off, and not as fully off.

Whether a response shows visible explicit inference is decided by a blinded AI judge, GPT-5.5 at temperature 0. Humans labelled 600 outputs to check that judge. Their labels agree with GPT-5.5 on 98.8% of them, and with a second judge, Claude Opus 4.8, on 96.7%.

Two of the paper's measures come up below, EIR and ETR. Both are the paper's own metrics, so take their exact definitions from the paper itself.

## The complication

The obvious reading is that the switch decides whether a model reasons out loud. But the paper finds something else: visible inference is organized more by the task's answer space than by the switch itself. Answer space here means the kind of answer a task asks for, from yes/no and multiple choice through to open-ended answers the model has to build.

The authors call this "thinking inertia". They use the term for visible reasoning that persists under no-thinking controls and becomes more common as the answer space opens.

The open-ended end of that range is the clearest. There, answer-only responses collapse to nearly zero everywhere, while explicit inference stays high. The authors say this is consistent with open answers needing more intermediate computation than picking from narrow or visible options. They word it as a fit with what they observe, but stop short of proving the cause.

GPT-5.4 is the main exception to a pattern the paper reports on yes/no and multiple-choice tasks. On both, it stays answer-only most of the time, with correspondingly low explicit inference.

## The evidence

![A real reply from DeepSeek-V4-Flash with its thinking switch off: asked a multiple-choice question about US military spending in 2017, it still writes a sentence of reasoning, then answers B. On open-ended questions every model tested almost never gave just the answer; this one reasoned out loud in 99.9% of replies. An 'answer only' prompt made about 40% of replies just the answer, and accuracy fell about 15 percentage points.](/assets/turning-thinking-off-doesn-t-stop-a-model-from-reasoning-out-loud.png)

The picture walks through one real reply and then the paper's headline case: with thinking disabled, DeepSeek-V4-Flash still shows visible explicit inference in 99.9% of its open-ended responses.

The first fix most people would reach for is a stricter prompt. Across the five models with thinking fully disabled, strict answer-only prompting raises mean ETR from near zero to roughly 40%. But the same prompting also reduces mean accuracy by about 15 percentage points. Both are means across the five models, and the paper itself hedges them with "roughly" and "about".

The paper's worked example shows where that accuracy can go. Stronger direct-answer pressure can remove the computation needed to check a subtle constraint, in that case the word "smallest" in the question.

A prefilled answer template, the paper's M6 setting, does no better. It neither reliably produces answer-only responses nor consistently suppresses visible explicit inference. In the authors' words, it is "not a stronger version of Strict".

The paper also rewrites closed questions as open-ended ones. On that rewrite, accuracy falls to 52.6%, ETR drops to 0%, and EIR rises to 90.14%. The numbers for the original format aren't quoted here, so read these as levels after the rewrite and leave aside the size of the change.

That 0% can look like it clashes with the roughly 40% from strict prompting. But the two figures are consistent, because the two experiments change different things. Strict prompting keeps the tasks and changes the instruction, which pushes the model toward answer-only output. The rewrite changes the task itself into an open-ended one, which is exactly where answer-only output collapses.

The result also holds up with a different judge. Claude Opus 4.8 finds visible explicit inference in 61.02% of open-ended outputs under the paper's M5 condition, close to the 62.00% that GPT-5.5 finds.

## Limits

The biggest limit is what the paper measures. Its claims concern visible output only, and it does not treat provider signals as a description of internal reasoning. So a clean answer-only response does not show that the model did less work, or that the call cost less.

The authors leave multilingual, code, tool-use, multi-turn and stochastic settings open, along with how the results hold as proprietary endpoints change. If you build agents, treat this as a strong hint for your setup, since it was not tested there.

The API results come from 1,000-item samples per benchmark, and not from full test sets. Explicit inference is scored by an AI judge, but the human check on 600 outputs makes that less of a worry.

## Takeaway

If your product turns thinking off, read a sample of real responses for each type of task you serve, and start with the open-ended ones. Check whether reasoning text still shows up in them. Before you add a stricter answer-only prompt, measure accuracy on your own tasks, because the paper saw mean accuracy fall by about 15 points. And don't take answer-only output as proof that cost went down.

I'm wary of any answer-only prompt that makes output look tidy until someone measures what it does to accuracy.