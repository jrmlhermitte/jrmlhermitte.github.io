---
layout: single
title: "Growing With Agents"
permalink: /preview/4f8b2c91-a7e5-4d33-b912-680f76e5341c.html
---


I’ve been playing with a lot of agentic frameworks at home and at work. There
are lots of pages with feedback on agents (example: [learnings from the
sourcegraph team](https://www.nibzard.com/ampcode)) and tons of tried and true
[agentic patterns](https://agentic-patterns.com/) out there.
What is an agent? More or less, it’s defined as [^1]:

> A system designed to perceive its environment, reason about how to achieve a
specific goal, and then take actions to complete it—often with little to no
human intervention.

So, this is not just an LLM that responds with text, but one with access to some tools such as searching the web etc. I have tried many different frameworks of varying complexity and productivity and found there remains one constant question among them:

> ***How can I use them so that I, as the operator, also grow?***

These are some tips that I’ve learned over time that help put emphasis on
self-growth, and not just productivity. Our brain has an impressive capacity to
learn. By missing learning opportunities, we’re wasting that capacity away.
Learning is deeply embedded in our DNA, providing many benefits beyond
furthering our career, such as for example,
[improving your mental health](https://www.grandrisingbehavioralhealth.com/blog/the-mental-health-benefits-of-learning-a-new-skill).
These tips should not only help you make the most of agents, but ensure that
you're exercising your capacity to learn, and not throwing it away in the process.

To keep this engaging, I've added suggestions you can try
yourself. You can follow this by just opening a bunch of free
[Gemini](https://gemini.google.com/) / [ChatGPT](https://chatgpt.com/) sessions.

> **NOTE**: I have also decided to stick to one set of prompts throughout this post
from the [BMAD method](https://github.com/bmad-code-org/BMAD-METHOD), mainly
because in my opinion they’re so good! :) If you use any of these prompts and
end up using them in your software, please do read the license and attribute it
properly (and please thank this amazing author, Brian Madison). Thank you Brian!

[^1]: This was taken from [geeks2geeks](https://www.geeksforgeeks.org/artificial-intelligence/agents-artificial-intelligence/).

## Separate By Scale And Stage, Not Expertise

I’ve found that trying to separate agents by expertise tends to limit them. If
you think about it, it doesn’t make sense. For example, imagine asking a an
astrobiologist to pretend they only know about astronomy when answering
your questions. Whether they understand biology or not should not affect the
helpfulness of their responses. It can only limit them.

Conversely, allowing an agent to traverse scale is counter to how we think. We
never try to think about how a car works, by thinking about the components:
wheels, axle, motor, and then drill down to what makes rubber tires so
impressively durable. So asking them to highlight the general concepts behind
things and avoid drilling down does make sense. We want them to be as
knowledgeable as possible, but focused on the right scale level.

These specific two ideas have a name in the literature:

- Scale: [Hierarchical Task Decomposition](https://www.emergentmind.com/topics/hierarchical-task-decomposition)
- Stage: [Discrete Phase Separation](https://agentic-patterns.com/patterns/discrete-phase-separation/)

### BMAD Scale Example

We see this in software engineering. I'll use the BMAD method as an example
here. When working on a product, we generally think through what the overall
goal is we are trying to achieve. This is equivalent to a [product manager
agent](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/pm.txt)
writing a **product requirements doc**. When we have the goal in mind, we start
thinking about how we would implement it high level: what are the constraints
(requirements)? What kind of system can we put together that would optimize on
these constraints? This would be equivalent to a senior engineer drafting a
design (the [architect
agent](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/architect.txt)).
Here, technologies and high level structure are documented, but the nitty gritty
details, unless relevant to this scale is avoided. Finally, when we have this
high level picture, we start funneling down into creating specific tasks. In
BMAD, the associated agent it the [scrum master
agent](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/sm.txt).
Finally, you have the [developer
agent](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/dev.txt)
which executes the tasks themselves.

Often for small projects, this is sometimes skipped or short-circuited. With
agents, where writing things down is fairly cheap, it is critical this is not
skipped. Agents don't remember things, and so everything they need to remember
needs to be written down.

### BMAD Stage Example

Interleaved with this is the concept of operating in stages. Early on, we're not
quite sure what we want, and so a common tendency is to research → plan → execute
which is a [very common pattern](https://agentic-patterns.com/patterns/discrete-phase-separation/). In BMAD, this is basically:

- Research: The
[analyst](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/analyst.txt),
helps you create a brief
- Plan: The [product manager
agent](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/pm.txt),
[architect](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/architect.txt)
and [scrum master
agent](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/sm.txt)
help you break it down into tasks.
- Execute: The [developer](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/sm.txt) executes the work.

> **Try this (software engineering example)**: Copy paste each of these prompts
below in different chatGPT/Gemini/etc sessions and have them work with you on a
project idea in order. At the end of each session, have the earlier one write
you a doc to give the next one. Agents:
>
>[Analyst](https://raw.githubusercontent.com/bmad-code-org/BMAD-METHOD/refs/tags/v4.44.3/dist/agents/analyst.txt)
→ [Product
Manager](https://raw.githubusercontent.com/bmad-code-org/BMAD-METHOD/refs/tags/v4.44.3/dist/agents/pm.txt)
→
[Architect](https://raw.githubusercontent.com/bmad-code-org/BMAD-METHOD/refs/tags/v4.44.3/dist/agents/architect.txt)
→
[Scrum Master](https://raw.githubusercontent.com/bmad-code-org/BMAD-METHOD/refs/tags/v4.44.3/dist/agents/sm.txt)
→
[Developer](https://raw.githubusercontent.com/bmad-code-org/BMAD-METHOD/refs/tags/v4.44.3/dist/agents/dev.txt)

## Always Use a Verifier Agent

For each agent, on top of giving it tools to verify its work, always use a
verifier agent to verify your tasks. This is similar to when one creates work
(post, story, code etc). One will proof read it first.

Critical properties of this complement agent:

- Must start from a fresh context
- Must not have access to any private artifacts for the other agent
- Must have a prompt with focus on verification

Conceptually, you want the agent to proof-read what was done with a fresh pair of eyes as you would do with your own work. This will often save a lot of time.

Examples from the [BMAD framework](https://github.com/bmad-code-org/BMAD-METHOD): 
- [Story scrum master](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/sm.txt)
→ verified by [product
owner](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/po.txt),
- [developer](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/dev.txt)
→ verified by the [QA
agent](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/qa.txt)

> **Try this**: Wherever you have run an agent, be it CLI, or UI, ask it to
summarize its persona, tasks and work done (it can refer to files it has written
to if your agent has access to them).  Open a new agent and type “you task is to
verify the work below:” and copy paste this summary.

## Automate Only The Scale You’re Comfortable With

When starting, it’s tempting to just let agents mostly run wild. This is almost
always a bad idea. You will quickly lose understanding of the product being
built. Agents are not at a level today where you can trust them like a human,
and you absolutely need to be familiar with their work. Rather, an agent should
be a facilitator or force multiplier. It should:

- Encourage you and help you brainstorm new ideas.
- Teach you about new concepts you may not have known.
- Execute tasks that are more or less routine.

Over time, as you gain experience, where this line is drawn will change for you. It is critical that this line meets your level of expertise, or you will find yourself quickly lost with the work your agents provide.

The best way I found is have three categories of agents:

### 1. Q&A Helper Agent

This is an agent you ask whenever you have questions who does the work to search
for information and aggregate it for you (most prompts do this today by
default). Even if you think you know the answer to a question, open a new
session and fire away. In my opinion, you should be asking at the very least 10
questions a day. Sometimes you won’t need the answer. Sometimes it will be
helpful. Sometimes, it will spark more questions, seeding more ideas and more
innovation (the ones that allow you to discover what you don’t know that you
don’t know are the best). This agent must be extremely simple to spin up and
type/dictate your question (fraction of a second). Get the gemini chrome
extension agent, or anything else that suits your needs.

> **Try this**: Open 5 prompts and ask questions you have been curious about
today. Some starters: is there something you’ve used you would like to
understand deeper? (random examples: how a database works, how transmission
fluid works, why stars twinkle and planets don’t etc). When you receive and
answer, try to come up with a few follow up questions (the

### 2. Facilitator Agent

Agents that will brainstorm and ask you questions iteratively. Most modern
prompts (chatGPT, Gemini) already do this for you by asking follow up questions.
If you want something more sophisticated, the BMAD method has a good set of
agent prompts in this category [here](https://github.com/bmad-code-org/BMAD-METHOD).

> **Try this**: Try copy pasting one of [this analyst agent](https://github.com/bmad-code-org/BMAD-METHOD/blob/v4.44.3/dist/agents/analyst.txt)
into Gemini/ChatGPT and see what you come up with.

### 3. Autonomous Execution Agent

This is the agent that does the work. What this is depends on the nature of work
and your expertise. For example, a junior software developer might benefit from
no agents and conservative code completion. More experienced developers will
benefit from agents of different scales. The line will also be drawn depending
on expertise (backend/fullstack etc). Although it can be the most fun type of
agent, I have found that I spend a very small fraction of my time with it.

> **Try this**: Ask gemini to draw you a unicorn maze in python.

## Keep It Simple

Unless you plan on building a product that you’re shipping to customers, keep
your workflow as dumb and simple as possible and cap any exploratory workflow
improvement work to at most 10% of your time. The [bitter
lesson](http://www.incompleteideas.net/IncIdeas/BitterLesson.html) and the [wait
calculation](https://www.oneusefulthing.org/p/the-lazy-tyranny-of-the-wait-calculation)
has taught me that often making do with the tools that are easiest to use are
better time spent than the cognitive overhead of developing a tool and
remembering how to use it for your workflow. If you spend all your time
developing a grand workflow that will solve all your woes, there is always the
risk that:

1. It will be superseded by something else and simpler.
2. It may be so complicated to setup that you’ll never use it.

 So conservatively hedge your bets. Say you get something working for 1-2 weeks
 only to find a better tool supersedes it. Would you prefer to have spent 1-2
 weeks of work on it heads down, or 1 hour a week and still made progress on
 your main work?

Sometimes though, you will come up with innovations that are worthwhile. If so,
you can use this litmus test. Is it easy to use? Show it to a friend or
colleague. If they start using it, or come to you with some feedback/discussion
about it, chances are it’s worthwhile. If it’s hard to use, or you see no
interest, it’s probably best to keep it capped at 10% of your time.

## Closing Thoughts

This brings us to the end of this post. As we develop more and more complex
agentic systems to solve hard problems, I believe we must remember what drove us
here is the desire to learn. Learning and thinking is such an integral part of
human behavior that it will be here to stay while humans exist, even if it means
to eventually [merge with machines](https://www.youtube.com/shorts/n4nOOXC7yg0).

To learn has been a luxury in the past. However today, we're in a pivotal moment
in humanity where this is no longer true. We have tools that can think on scales
that can perform most repetitive tasks today, enabling us to maximize our
learning. Not so familiar with rust? Get an AI tutor to make you exercises. Want
to practice with a project but don't have any ideas? Ask the AI. Moved on and
want to plan and build your very first large scale design? Have an AI help you.

The key here is the AI is **here to help you**. We are all still growing in
difference ways at different stages of our lives, and we now have a tailored
coach to help us with it. On the flip side, it could be used incorrectly. You
could use AI to finish your homework. You could use AI to write up your
entry-level engineering project. Use it to write a larger scale design for a
project, review someone else's code. However, use it that way, and you'll
quickly find that your cognitive skills will decline. Remember that [chat GPT
study](https://www.media.mit.edu/projects/your-brain-on-chatgpt/overview/) where
they found users tasked to write an essay with the aid of ChatGPT were found to
be more "metacognitively lazy?" This is a pretty obvious finding and wasn't the
key finding of this paper. What was more important, is they found the group that did not use chatGPT to write essays actually performed **a lot better** when allowed to use chatGPT to write essays after having **practiced 4 in sessions without it**.
In other words, if you want to optimize your learning, you have three choices:

1. Don't use AI: You will have in shape critical thinking skills.
2. Use AI: Declined critical thinking
3. Don't use AI until you've mastered a skill, then use it to launch you into
more skills: The study shows you'll perform better.

I conjecture here that this is because you're maximizing your learning. You've
presumably mastered essay writing and it has become a mundane skill (you're no
longer learning), and now you're leveraging AI to help find more for you and
push your limits. So if there is anything I would like you to take away from
this is that the best way to leverage agents is to keep evaluating whether they
are maxmimizing your ability to learn. Things are going way above your head?
Step back and ask the agent to explain things and suggest a plan for how to
learn what's going on with exercises. Things are a little too simple? Empower the
agent to do more.

Remember, you are the driver and where you're headed is within your control!
