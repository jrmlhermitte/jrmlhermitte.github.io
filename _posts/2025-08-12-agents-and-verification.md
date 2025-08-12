## When Are Agents Worth It?

### Introduction

Agents are so prevalent today that I don't believe they need any introduction.
You can find numerous success stories. My favorite one so far is this recent one
[here](https://cameronwestland.com/it-s-not-10x-it-s-36x-this-is-what-it-looks-like-to-kill-a-usd30k-meeting-with-ai)
involving oncall triage.

As everyone else, I have been using agents successfully and unsuccessfully. At
times, they were a time saver, and at times, a time sink.  This document aims to
naively explore when it might be worth considering an agent.  Although today
most uses of agents are quite complex involving multiple agents, it will assume
a very simple one agent setup.

I hope to in the future elaborate on a more complex setup.

### Formalizing The Problem

Let's say we have an agent $$A$$ that is tasked to solve problems. When is it useful, and when might we be better off taking a manual approach? We can reduce this question down to a few simple variables:

- $$T$$ $$\rightarrow$$ The total time a task takes
- $$P_s$$ $$\rightarrow$$ The probability of an agent being successful
- $$V_r$$ $$\rightarrow$$ The percentage of the total task time it takes to verify the result of an agent. For simplicity, this will include the time spent fixing/tweaking the result if it is almost correct.

Now, in reality, longer tasks tend to be harder for an agent as they're
generally more complex problems, so $$P_s$$ should not be constant over that. The verification ratio can sometimes change as well. So let's assume $$P_s \rightarrow P_s(T)$$ and $$V_r \rightarrow V_r(T)$$. Effectively, what we're assuming here is that tasks of the same length have the same difficulty. This isn't quite right (task time is not one-to-one with its complexity), but let's just start with this to get an idea of the limits of our agents. From now on, we'll just assume that the tasks here have the same complexity (example complexity categories: doc writing, code migrations etc).

### Fire And Forget

One way agents could be used is a fire and forget type approach. Say you have 100 tasks (100 bugs), and they're all about the same complexity.
You have a $$P_s$$ chance of succeeding, and thus $$(1 - P_s)$$ of failing.
When you succeed, you spend the verification time, $$V_r T$$ on the problem.
When they fail, you suffer the verification plus total time (since you actually
have to work on the problem yourself: $$T(1 + V_r)$$.

Let's say the time you take for one task is $$\tau$$, which is a random variable.
The expectation is the the sum of these two cases:

$$
\begin{align*}
\langle \tau \rangle & = & P_s V_r T + (1 - P_s) T (1 + V_r) \\
& = & T \left ( P_s V_r + (1 - P_s) (1 + V_r) \right )\\
& = & T \left ( P_s V_r + 1 - P_s + V_r - P_s V_r \right )\\
& = & T \left ( 1 + V_r - P_s \right )\\
\end{align*}
$$

Subtracting the time we would have taken for the task, we get:

$$
\begin{align*}
\frac{\Delta \langle \tau \rangle}{T} & = & V_r - P_s
\end{align*}
$$

This $$\Delta \langle \tau \rangle$$ is the additional time gained/lost.
We want this to be negative so that we **reduce** the total time we spend
on tasks. We quickly see that to reduce this, we just need the probability of success to be less than the verification ratio.

### Try Until You Give Up

One day, we'll have an abundance of models, and we may even be able to have them
retry and randomize until they succeed. What happens in the best case?

The cases are simple:

- $$P_s$$ chance of success, spent $$V_rT$$ time
- $$(1-P_s)$$ chance to fail first time, $$P_s$$ after to succeed, spent $$2V_rT$$ time
- etc

The expectated time is the sum of the time spent in each of these cases times
their probability:

$$
\begin{align*}
\langle \tau \rangle & = & \sum \limits_{i=0}^{\infty} P_s (1 - P_s)^{i-1} i V_r T \\
& = & V_r T \sum \limits_{i=0}^{\infty} P_s (1 - P_s)^{i-1} i
\end{align*}
$$

This is a well known formula which can be derived by simply taking
$$f = (1 - P_s)$$, the sum $$S = \sum \limits_{i=0}^{\infty} f^{i-1} i$$ and
subtracing these two quantities: $$S - f S$$. I will not go through the
derivation and leave it as an exercise.

The result is then:

$$
\begin{align*}
\langle \tau \rangle & = & V_r T \sum \limits_{i=0}^{\infty} P_s (1 - P_s)^{i-1} i \\
& = & V_r T \frac{1}{P_s} = T \frac{V_r}{P_s}
\end{align*}
$$

or, in terms of $$\frac{\langle \Delta \tau \rangle}{T}$$:

$$
\begin{align*}
\frac{\langle \Delta \tau \rangle}{T} & = & \frac{V_r}{P_s} - 1
& = & \frac{1}{P_s} \left ( V_r - P_s \right )
\end{align*}
$$

We can see in this case, if we keep trying, assuming the model will be right
$$P_s$$ (so the prob of success doesn't change knowing the model previously failed
for example), we will have saved time if $$P_s > V_r$$. This is the same result as
for the fire and forget case.

### Which Is Better?

This brings an interesting question. As we develop more agents, which case is better to optimize for?

If we take the ratio:

$$
\begin{align*}
\frac{\mathrm{FireAndForget}}{\mathrm{TryForever}} & = & \frac{V_r - P_s}{\frac{1}{P_s} \left ( V_r - P_s \right) } \\
& = & P_s
\end{align*}
$$

we see that it just depends on $$P_s$$. Since we know that $$0 \le P_s \le 1$$, we quickly see that this ratio is less than 1, suggesting that the try forever approach has a larger gain $$\langle \Delta \tau \rangle$$.

This brings in an interesting question. We are trending towards the direction of having a multitude of agents with different strengths. At some point, the diversity of agents will likely make the try forever approach eventually succeed in most cases and be worth the effort. This is an interesting avenue of innovation and possibility.

### Some Examples

When are some cases where this might be useful? Here are some tips from my personal experience. Please note this is no way quantitative (hope to try providing some quantitative results in a future post) and I'd love to hear opinions about these.

#### Fixing Unit Tests

These types of problems are very easy to verify. Tests can be verified in a
semi-automated way by simply running them. They still need to be manually
reviewed for correctness and sometimes tweaked. You can introduce variety
by randomly picking some sample code changes with test cases, making the try
forever case also more favorable.

#### Oncall Troubleshooting

Oncall analysis/troubleshooting tends to involve needle in a haystack type
problems. The culprit is sometimes hard to find, but easy to verify. Since this
is a retrieval task, likely increasing model temperatures or randomizing inputs
would produce enough variety.

#### Document Writing

The verification overhead $$V_r$$ for these problems tends to be pretty high.
In my opintion, I feel likely better off writing the documents myself unless
the writing is a very basic information retrieval task (ex: take this bug and
add it to the troubleshooting page or FAQ).

Here are some estimates (again, not quantitative!):

| Task Type              | Verification Ratio | Probability of Success | Fire And Forget % Time saved | Try Until You Succeed % Time Saved |
| ---------------------- | ------------------ | ---------------------- | ---------------------------- | ---------------------------------- |
| Bug Fixing             | 5%                 | 80%                    | 75%                          | 93.75%                             |
| Oncall Troubleshooting | 5%                 | 60%                    | 55%                          | 91.7%                              |
| Document Writing       | 60%                | 75%                    | 15%                          | 20%                                |

### What do you think?

What do you think? What is your experience with agents?
What has saved you the most time? Any interesting links to share?