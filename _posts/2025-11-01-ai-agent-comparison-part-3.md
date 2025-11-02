---
layout: single
title: "How Asking the Model to Reason Changed Everything"
excerpt: "Part 3 of 3: How adding a reasoning step improved Claude's architecture by 19%."
series: "AI Agent Comparison"
series_order: 3
---

**TL;DR:** When I asked Claude's Product Owner persona to reason through
conflicting documentation, its architecture and code quality jumped by 19%.
Small prompt changes can have big architectural consequences.

## The Turning Point

During the first test, Claude stored extracted HTML directly in PostgreSQL. My
intended design stored it in **MinIO**, an object store used elsewhere in the
system. The discrepancy came from a conflict between the PRD and architecture
documents.

The Product Owner did notice the inconsistency but chose the simpler route:

> “Approve Option A for MVP consistency with AC 5 wording. Refactor to MinIO later.”

That seemed fine - until the decision cascaded through the codebase, causing
schema bloat and tighter coupling between services.

## The Experiment

To see if Claude could self‑correct, I ran the workflow again, watched the
product owner make the same mistake[^1] and asked it:

> "Can you reason carefully through trade‑offs of these ambiguitied
and present your preferred choice with pros and cons?"

That was it. Same task, same environment. The only change was *asking for
explicit reasoning*. Since Claude was pretty decent at scoring the differences
between the code given the more or less good agreement with my rubric and
Claude's, in this case I let Claude score it for me. The following results are
scores determined by Claude, but peer reviewed and accepted by me.

[^1]: Of course with slightly different wording and structure, given the stochastic nature of these models.

## The Results

| Rubric         | Claude v1             | Claude v2 | Δ   | Observation                           |
| -------------- | --------------------- | --------- | --- | ------------------------------------- |
| Documentation  | 8/10                  | 9/10      | +1  | Clearer examples and module docs      |
| Test Quality   | 8/10                  | 9/10      | +1  | Added security and fixture tests      |
| Readability    | 7/10                  | 8/10      | +1  | Better separation via Pydantic models |
| Sustainability | 6/10                  | 8/10      | +2  | Cleaner API‑DB boundaries             |
| Architecture   | 7/10                  | 9/10      | +2  | Correct MinIO integration             |
| **Overall**    | **7.2 → 8.6 (+19 %)** |           |     | Improved reasoning clarity            |

### Structural Metrics

| Metric         | v1    | v2    | Δ    | Interpretation           |
| -------------- | ----- | ----- | ---- | ------------------------ |
| Lines Added    | 4 138 | 3 682 | −456 | More concise code        |
| Files Modified | 34    | 29    | −5   | Tighter focus            |
| Test Files     | 8     | 11    | +3   | Broader coverage         |
| QA Fixes       | 8     | 5     | −3   | Fewer corrections needed |
| Total Commits  | 21    | 12    | −9   | More efficient iteration |

---

## What Changed in Practice

Claude v2 reasoned through the conflict explicitly:

> “Option A: Store protobuf in MinIO - aligns with Story 2.3, scalable,
consistent schema. Option B: Add BYTEA column - simpler but heavier DB.
**Recommendation: Option A.**”

This small deliberation step unlocked better architectural alignment *without
any new context*. It simply took time to think.

## Takeaways

1. **Ask for reasoning, not just answers.** Explicit reflection yields better structure.
2. **Document the decision process.** When models articulate trade‑offs, it's easier to audit or adjust them later.
3. **Keep humans in the loop.** BMAD's layered agent roles (PO -> Architect -> Dev) make these checks natural.

> Claude's improvement didn't come from a model upgrade. It came from giving it permission to think.

## Closing Thoughts

It's tempting to chase the most powerful model of the moment. But this
experiment shows that learning to get the most out of any model delivers far
greater returns, and that advantage compounds as models improve.

In the end, progress came not from switching tools, but from refining how I
worked with them.

> “Always assume today's AI is the worst you'll ever use.”  - Ethan Mollick [^cointelligence]

This concludes the **AI Agent Comparison series**:

- [**Part 1**](/2025/11/01/ai-agent-comparison-part-1.html): Setup and methodology
- [**Part 2**](/2025/11/01/ai-agent-comparison-part-2.html): Results and analysis
- [**Part 3 (this post)**](/2025/11/01/ai-agent-comparison-part-3.html): Why asking the model to *reason* changed everything


[^cointelligence]: Mollick, E. (2024). *Co-intelligence: living and working with AI.* Portfolio/Penguin. [link](https://www.goodreads.com/book/show/198678736-co-intelligence)
