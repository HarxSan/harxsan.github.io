---
title: "The RAG components that win alone add up to much less together"
date: 2026-10-24
description: "In a large study of RAG pipelines, the three retrieval techniques with the biggest separately measured gains predicted a +46.6% NDCG@10 lift over the default_standard baseline, which scores 0.412. The"
canonical_url: https://harxsan.github.io/2026/10/24/the-rag-components-that-win-alone-add-up-to-much-less-together/
image: /assets/the-rag-components-that-win-alone-add-up-to-much-less-together.png
---

In a large study of RAG pipelines, the three retrieval techniques with the biggest separately measured gains predicted a +46.6% NDCG@10 lift over the default_standard baseline, which scores 0.412. The balanced pipeline that combines them realizes only +17.2%, a 63% shortfall against that prediction.

The three techniques are cross-encoder reranking, disabling MMR, and document-awareness prefixes. Each one looks like a clear win in its own ablation. Together they deliver much less than their separate results add up to.

## Why it matters

Most teams decide what stays in a RAG pipeline one ablation at a time. If a stage wins its ablation, it goes in, and the expected gain of the full stack is the sum of the wins. Every stage adds latency and cost to every query, so when the sum does not hold, the team pays for gains it never sees.

HyDE is a small example from the same paper. It raises NDCG@10 by 3.1% but doubles first-hop latency, which reduces the latency-adjusted gain to 0.9%.

## The setup

The paper (arXiv 2610.29722) uses a framework called lasagna to study how RAG components compose. It runs 167 configurations chosen with a D-optimal design (Fedorov et al., 1972; Atkinson et al., 2007), across eight configurable pipeline stages. A D-optimal design picks a subset of the full grid of configurations that still lets the effect of each factor be estimated. The study covers eight benchmarks and corpus sizes from 5K to 200K documents. It adds 138 non-factorial ablation configurations across 24 targeted experiments.

Retrieval is scored with NDCG@10. Generation runs report faithfulness and answer relevance from RAGAS v0.4 (Es et al., 2024), judged by GPT-5.1, plus exact match, token-level F1, p50/p95 latency and cost per query. GPT-5.1 also generates the answers for every configuration. Faithfulness here measures whether the generated claims are supported by the retrieved context.

## The complication

The natural reading of a set of winning ablations is that the gains stack. The paper finds that they partly overlap. Its ablations suggest that reranking already provides some of the filtering benefit of disabling MMR, which leaves less room for the two to help when combined.

The balanced pipeline is not an outlier. Including all five separately positive retrieval techniques raises the shortfall to 72%. Across 17 tested combinations, the shortfall ranges from 7% to 72%, with a median of 41%.

The same kind of overlap shows up between the pipeline and the generator. A stronger generator helps the lightest pipeline far more than the heaviest, so a heavier stack buys less when the generator is already strong.

There is a second complication. Embedding, enrichment and reranking dominate the retrieval-quality metric, while query augmentation and agentic gating dominate answer faithfulness. Across matched configurations, the two metrics are essentially unrelated, with a pooled Pearson r of -0.01 (p = 0.91). So in this study, an NDCG@10 gain says almost nothing about whether the answers became better supported.

## The evidence

![Bar chart comparing the predicted and the realized NDCG@10 lift of the combined balanced pipeline over the default_standard baseline](/assets/the-rag-components-that-win-alone-add-up-to-much-less-together.png)

The chart puts the predicted lift next to the realized one, and the difference between the two bars is the 63% shortfall.

The factorial analysis shows the same split between the two metrics. It reports η² (the share of a metric's variance associated with one factor) for each stage. On retrieval, the largest marginal associations are embedding (η² = 0.164), enrichment (η² = 0.144) and reranking (η² = 0.090). On faithfulness, query augmentation (η² = 0.224) and agentic gating (η² = 0.217) lead, while embedding and enrichment are negligible (η² < 0.02 each). The authors note that these values are unadjusted, meant for exploratory screening, and not additive, because the design is unbalanced and the effects share variance.

Several single components also behave differently than you might expect. MMR reduces NDCG@10 by 3.6% relative on average, opposite its usual benefit on broad, heterogeneous corpora. The 2019 MS-MARCO MiniLM-L6 cross-encoder (Nogueira & Cho, 2019) ranks above all four recent rerankers tested. The authors speculate that its training aligns especially well with their focused domains. Chunker choice spans 0.126 NDCG@10 across 20 configurations, a 36% relative difference larger than most factorial main effects.

Corpus size changes which pipeline wins. The standard profile leads at 5K and 15K documents, and balanced leads from 25K onward. At 200K, balanced exceeds standard by 13.7% relative NDCG@10. Section 5 reports p = 0.003 for that gap, and the appendix reports p = 0.004 under Welch's t. The two values are consistent with a different test or reporting protocol, and the gap is significant under both. The 25K and 100K gaps are directionally consistent but underpowered.

Between the two agentic profiles, balanced has 4.5% higher NDCG@10 than max-reliable, with only a 0.005 faithfulness difference. Section 5 gives its response-time advantage as 13%, and the appendix gives 13.5% lower mean response time. The two figures are consistent with rounding.

The formal interaction test is more cautious than the headline numbers. The pre-declared test of the main interaction hypothesis, H1, was not met. A focused follow-up, pre-specified before it ran, confirms that the query augmentation × agentic gate interaction is real (p = 0.007), but far below that bar. Its partial η² is 0.033, and 10 of 20 combinations depart from additivity by at least two percentage points. Under the study's own variance rule, 0.033 sits on the additivity-holds side of the 5% floor. So the combined pipelines fall well short of the summed gains, while the one interaction tested formally stays below the bar the authors set in advance.

## Limits

The authors call their findings case-study evidence, and their derived profiles candidates for further validation rather than prescriptions for high-stakes deployments. The benchmarks sit in five focused domains: biomedical, financial, scientific, argumentative, and multi-hop QA over Wikipedia. The scaling distractors come from those same five domains, so broad, mixed corpora remain untested.

Faithfulness depends on one judge, GPT-5.1. A second judge agrees only moderately (Pearson r = 0.44, Cohen's κ = 0.35), so relative trends hold up but exact faithfulness scores depend on the judge. Raw faithfulness is also partly driven by answer length.

The hypotheses were pre-declared but not filed as a third-party pre-registration. The design matrix, per-query scores and analysis scripts are not in the public release, and are available from the authors on request. The corpora run from 5K to 200K documents, so larger indexes are outside what was measured.

## Takeaway

Before adding another stage to a RAG pipeline, measure the combined pipeline end to end on your own corpus. Track retrieval quality and answer faithfulness separately, because in this study one barely tracked the other.

If a stage only earned its place in a solo ablation, I would rerun the full pipeline without it before paying its latency on every query.