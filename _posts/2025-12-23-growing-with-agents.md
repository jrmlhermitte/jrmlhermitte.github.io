---
layout: single
title: "Beyond Automation: Using Agents to Level Up Your Mind"
---

<figure>
<img src="/images/2025-12-23-agentic-ladder.png" alt="A digital human climbing a glowing ladder towards a brain icon, representing AI-assisted growth">
<figcaption>The Agentic Ladder: Using scale, stage, and verification to reach higher levels of complexity.</figcaption>
</figure>

I’ve been playing with a lot of agentic frameworks at home and at work. There
are lots of pages with feedback on agents (example: [learnings from the
sourcegraph team](https://www.nibzard.com/ampcode)) and plenty of tried and true
[agentic patterns](https://agentic-patterns.com/) out there.

What is an agent? More or less, it’s defined as [^1]:

> A system designed to perceive its environment, reason about how to achieve a
> specific goal, and then take actions to complete it, often with little to no
> human intervention.

As we all know, this isn't just an LLM that responds with text. It is a model
with access to tools like web search or code execution. However, as I’ve
experimented with these frameworks, I've realized we are facing
**The Operator’s Dilemma**: the more efficient our agents become at "doing," the
more passive we become as observers. If we aren't careful, we risk becoming mere
passive observers and losing the very skills that made us capable of directing
the agents in the first place.

I found myself asking one constant question:

> **_How can I use agents so that I, as the operator, also grow?_**

These are some tips I’ve learned to put the emphasis on self-growth rather than
just raw productivity. Our brain has an impressive capacity to learn. By
offloading the "thinking" without a strategy, we waste that capacity. Learning
provides benefits beyond just your career, including improved mental health
[^2].

To keep this engaging, I've added suggestions you can try yourself. You can
follow along by just opening a few free [Gemini](https://gemini.google.com/) or
[ChatGPT](https://chatgpt.com/) sessions.

> **NOTE**: I have decided to stick to one set of prompts throughout this post
> from the [BMAD method](https://github.com/bmad-code-org/BMAD-METHOD), named
> after its creator Brian Madison. I use BMAD because it mirrors a professional
> software development lifecycle (SDLC) more closely than any other framework I’ve
> found. If you use these prompts, please attribute them to the author, Brian
> Madison.

---

## Separate By Scale And Stage, Not Expertise

I’ve found that trying to separate agents by expertise (e.g., "The Python
Expert") tends to limit them. If you think about it, it doesn’t make sense. If
you ask an astrobiologist a question, asking them to "pretend they only know
astronomy" doesn't help you. It just restricts the resources they can draw upon.

Conversely, allowing an agent to traverse _scale_ is counter to how we think. We
don't try to understand how a car works by thinking about the molecular
structure of rubber tires at the same time we're thinking about the steering
rack. We want the agent to be as knowledgeable as possible, but focused on the
right level of abstraction.

These two concepts have specific names in the literature:

- **Scale:** [Hierarchical Task Decomposition](https://www.emergentmind.com/topics/hierarchical-task-decomposition)
- **Stage:** [Discrete Phase Separation](https://agentic-patterns.com/patterns/discrete-phase-separation/)

### Scale Example: Hierarchical Task Decomposition

In software, we handle scale by moving down a hierarchy. This prevents the
"Developer" agent from getting bogged down in "Product" questions.

```mermaid!
graph LR
    A[Analyst] --> B[Product Manager]
    B --> C[Architect]
    C --> D[Scrum Master]
    D --> E[Developer]
    style A fill:#f9f,stroke:#333
    style E fill:#00ff00,stroke:#333

```

In the BMAD method, this looks like this:

- **Goal:** The [Product Manager](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/pm.txt) writes a PRD.
- **System:** The [Architect](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/architect.txt) defines the high-level constraints.
- **Task:** The [Scrum Master](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/sm.txt) breaks it down.
- **Execution:** The [Developer](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/dev.txt) writes the code.

### Stage Example: Discrete Phase Separation

Interleaved with Scale is the concept of operating in **Stages**. Early on, we aren't sure what we want, so we follow a pattern of **Research → Plan → Execute**.

In BMAD, this separates the work into distinct phases so you don't start coding
before you know what you are building:

- **Research:** The [Analyst](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/analyst.txt) helps you create a brief.
- **Plan:** The [Product Manager](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/pm.txt),
  [Architect](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/architect.txt)
  and [Scrum Master](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/sm.txt)
  help you break it down into tasks.
- **Execute:** The [Developer](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/dev.txt)
  executes the work.

Agents don't "remember" well across massive contexts [^3], so writing things down at each scale and stage—and passing those documents to the next agent—is critical.

> **Try this**: Copy and paste these prompts in different sessions in order. At the end of each session, have the previous agent write a "handover doc" for the next one.
> [Analyst](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/analyst.txt) → [Product Manager](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/pm.txt) → [Architect](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/architect.txt)

---

## Always Use a Verifier Agent

<figure>
<img src="/images/2025-12-23-verifier-agent.jpg" alt="Two agents, a primary
agent (left) and verifier agent (right) separated by a context wall.">
<figcaption>Two agents, a primary agent (left) and a verifier agent (right),
separated by a context wall.</figcaption>
</figure>

For every agent performing a task, use a **Verifier Agent** to check the work. To avoid "sunk cost bias" or the agent simply agreeing with itself, the Verifier must follow three rules:

1. Must start from a **fresh context** (a new chat).
2. Must not see the private "thought process" of the first agent.
3. Must have a specific prompt focused on finding flaws.

This mimics the real-world "fresh pair of eyes" effect.

**Examples from BMAD:**

- [Scrum Master](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/sm.txt) verified by [Product Owner](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/po.txt).
- [Developer](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/dev.txt) verified by [QA Agent](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/qa.txt).

---

## Automate Only The Scale You’re Comfortable With

When starting, it’s tempting to let agents run wild. This is almost always a mistake. You will quickly lose understanding of the product being built. Instead, think of the agent as a **facilitator**.

The line of automation should meet your level of expertise. If you let an agent write code you don't understand, you aren't growing. You are just accumulating technical debt you can't pay off. I categorize agents into three levels:

### 1. Q&A Helper Agent

An agent you ask at least 10 questions a day. Even if you think you know the answer, ask. This is about discovering "what you don't know you don't know."

> **Try this**: Ask an agent "How does transmission fluid work?" or "Why do stars twinkle but planets don't?" Then (and this is the important part) ask **two follow-up questions** about the mechanics.

### 2. Facilitator Agent

These agents brainstorm and ask _you_ questions. They help pull the best ideas out of your head.

> **Try this**: Use the [Analyst Agent](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/analyst.txt) and tell it you want to build a new app. See how it challenges your assumptions.

### 3. Autonomous Execution Agent

The agent that does the work. Junior developers should use this sparingly, mostly for code completion. Senior developers can offload larger chunks of routine tasks.

> **Try this**: Ask an agent to write a Python script for a unicorn maze. **Crucially:** Ask the agent to explain the logic behind the pathfinding algorithm it used so you understand the "why" behind the code.

---

## Keep It Simple

Unless you’re shipping a product, cap your "workflow improvement" time to 10%. The [Bitter Lesson](http://www.incompleteideas.net/IncIdeas/BitterLesson.html) and the [Wait Calculation](https://www.oneusefulthing.org/p/the-lazy-tyranny-of-the-wait-calculation) have taught me that general methods usually win over complex, hand-tuned workflows.

If you spend all your time developing a grand workflow, you risk:

1. Being superseded by a simpler, native LLM update.
2. Creating a system so complex you never actually use it for real work.

---

## Closing Thoughts

As we develop more and more complex agentic systems, I believe we must remember
what drove us here: the desire to learn. Learning and thinking are such integral
parts of human behavior that they will be here to stay while humans exist, even
if it means we eventually merge with machines [^4].

We are at a pivotal moment. We have tools that can think on scales that handle
the mundane, enabling us to maximize our learning. But if used incorrectly,
simply to "finish the work," our cognitive skills will decline.

A [recent
study](https://www.media.mit.edu/projects/your-brain-on-chatgpt/overview/) found
that users who used ChatGPT to write essays became "metacognitively lazy."
However, the group that practiced **without** AI first, and then used AI,
performed significantly better.

This leads to what I call **The Agentic Ladder**:

1. **Master the skill manually** so you have a solid footing.
2. **Use the agent to launch you** to the next rung of complexity.
3. **Repeat.**

If you start on a rung you haven't mastered, you have no footing, and the agent
is carrying you. The goal isn't to build a better agent: it's to use the agent
to build a better version of yourself.

Don't let the agent be your replacement. Let it be the floor you stand on while
you reach for something higher.

---

## Interesting Reading

- [The BMAD Method](https://github.com/bmad-code-org/BMAD-METHOD) by Brian Madison: The core system used for the prompts in this post.
- [Agentic Patterns](https://agentic-patterns.com/): A comprehensive catalog of common agent behaviors.
- [The MIT ChatGPT Study](https://www.media.mit.edu/projects/your-brain-on-chatgpt/overview/): On the metacognitive effects of AI assistance.
- [The Bitter Lesson](http://www.incompleteideas.net/IncIdeas/BitterLesson.html) by Rich Sutton: A fundamental read on why general methods eventually win in AI.
- [The Lazy Tyranny of the Wait Calculation](https://www.oneusefulthing.org/p/the-lazy-tyranny-of-the-wait-calculation) by Ethan Mollick: Why waiting for better AI is often smarter than building complex tools now.
- [Context Window Anxiety](https://cognition.ai/blog/devin-sonnet-4-5-lessons-and-challenges#the-model-is-aware-of-its-context-window): Observations on model performance and context awareness.
- [Sourcegraph Team Learnings](https://www.nibzard.com/ampcode): Real-world insights from engineering teams building with agents.

[^1]: This was taken from [GeeksforGeeks](https://www.geeksforgeeks.org/artificial-intelligence/agents-artificial-intelligence/).
[^2]: See [The Mental Health Benefits of Learning a New Skill](https://www.grandrisingbehavioralhealth.com/blog/the-mental-health-benefits-of-learning-a-new-skill)
[^3]: Newer models are sometimes suspected of being [aware of their own context window](https://cognition.ai/blog/devin-sonnet-4-5-lessons-and-challenges#the-model-is-aware-of-its-context-window), which can hurt their performance. This is a phenomenon currently called "[context anxiety](https://agentic-patterns.com/patterns/context-window-anxiety-management/)". This is another reason why separating by scale is vital: smaller, focused contexts keep the agent (and the human) from feeling overwhelmed.
[^4]: A reference to this [intriguing prediction](https://www.youtube.com/shorts/n4nOOXC7yg0)
