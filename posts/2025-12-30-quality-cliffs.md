# Quality Cliffs

**Date:** 2025-12-30

**Standfirst:** What gets lost when you optimize for cost. A finding from building memory infrastructure.

---

We're building a system that extracts learnings from past conversations — what was built, what was discovered, what patterns emerged. The extractions feed a search index so future sessions can find relevant history.

Extraction is expensive. Each conversation gets summarized by an LLM. We have 1,800+ sessions. So we tested whether Haiku could do the job instead of Sonnet.

## The numbers

Same conversations. Same prompt. Different model.

| Metric | Sonnet | Haiku |
|--------|--------|-------|
| Learnings captured | 4.2 avg | 2.5 avg |
| `why_it_matters` length | 45 words | 28 words |
| Specificity | "This solves the core problem of X by Y" | "This is useful for development" |

40% fewer learnings. And the ones that survived were blander.

## The cliff

This wasn't linear degradation. It wasn't "80% as good for 20% of the cost." It was a quality cliff — the extractions crossed from useful to noise.

The difference between "Haiku extraction quality has steep cliff vs Sonnet" and "Model choice affects extraction quality" is the difference between a memory system that helps and one that clutters.

Haiku didn't miss obvious things. It missed the *interesting* things. The non-obvious connections. The insights that required holding multiple threads at once.

## The shape of cost optimization

When you optimize for cost in LLM systems, the degradation isn't smooth. There are plateaus and cliffs. You can often go surprisingly far down the capability ladder without losing much. Then you fall off an edge.

The edge isn't always where you expect. Haiku handles many tasks beautifully. But "extract what matters from a complex technical conversation" apparently requires more than it can bring.

The only way to find the cliff is to test. Side-by-side, same inputs, measuring what actually matters to you. "It runs" isn't the same as "it works."

> **Sameer writes:** Haikus are great — we've all seen that end screen where you find they were doing terrific work, so all credit to them — but sometimes you don't want to delegate something to an agent with a simpler worldview.
