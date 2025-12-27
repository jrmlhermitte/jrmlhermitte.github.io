## When Are Agents Worth It?

**TL;DR** Agents are only worth it if the probability of their success, $P_s$,
is greater than the time it takes to verify the task over the total time it
would have taken to complete the task without an agent, $\frac{\mathrm{Verification Time}}{\mathrm{Actual Task Time}}$ , so $P_s >
\frac{\mathrm{Verification Time}}{\mathrm{Actual Task Time}}$.

### Introduction

Agents are so prevalent today that they hardly need any introduction.
Numerous success stories exist. A notable example is this recent one
[here](https://cameronwestland.com/it-s-not-10x-it-s-36x-this-is-what-it-looks-like-to-kill-a-usd30k-meeting-with-ai)
involving oncall triage.

Following their wide spread use, agents have been used with varying degrees of success. At
times, they were a time saver, and at times, a time sink. This document aims to
naively explore when it might be worth considering an agent. Although today
most uses of agents are quite complex involving multiple agents, it will assume
a very simple one agent setup.

Future analysis may elaborate on a more complex setup.

<figure class="image">
  <img src="/images/2025-08-12-agent-and-verifier.png" alt="Agent And Verifier">
  <figcaption>Gemini prompt: Draw a picture of an agent as a robot alongside
  a human verifying a document the agent handed it. The agent also has as
  wristwatch and is checking the time.
  </figcaption>
</figure>

### Formalizing The Problem

Consider an agent $A$ that is tasked to solve problems. When is it useful, and
when is a normal approach preferable? This question can be reduced down to a few
simple variables:

- $T$ $\rightarrow$ The total time a task takes
- $P_s$ $\rightarrow$ The probability of an agent being successful
- $V_r := \frac{\mathrm{Verification Time}}{\mathrm{Actual Task Time}}$
  $\rightarrow$ The fraction of the time it takes to verify the task over the total
  time it would have taken to complete the task without an agent. For simplicity,
  this will include the time spent fixing/tweaking the result if it is almost
  correct.

Now, in reality, longer tasks tend to be harder for an agent as they're
generally more complex problems, so $P_s$ should not be constant over that. The
verification ratio can sometimes change as well. Therefore assume $P_s
\rightarrow P_s(T)$ and $V_r \rightarrow V_r(T)$. Effectively, this assumes that
tasks of the same length have the same difficulty. This isn't quite right (task
time is not one-to-one with its complexity), but this serves as a starting point
to estimate the limits of agents. It is assumed that the tasks here have the
same complexity (example complexity categories: doc writing, code migrations
etc).

### Fire And Forget

One way agents could be used is a fire and forget type approach. Consider 100
tasks (100 bugs), and they're all about the same complexity.
There is a $P_s$ chance of succeeding, and thus $(1 - P_s)$ of failing.
Upon success, the verification time spent is, $V_r T$ on the problem.
Upon failure, the cost is verification plus total time. This is due to
having to manually work on the problem: $T(1 + V_r)$.

Assume the time taken for one task is $\tau$, which is a random variable. The
expectation is the sum of these two cases:

$$
\begin{aligned}
\langle \tau \rangle &= P_s V_r T + (1 - P_s) T (1 + V_r) \cr
&= T \left ( P_s V_r + (1 - P_s) (1 + V_r) \right )\cr
&= T \left ( P_s V_r + 1 - P_s + V_r - P_s V_r \right )\cr
&= T \left ( 1 + V_r - P_s \right )
\end{aligned}
$$

Subtracting the manual task time gives:

$$
\begin{aligned}
\frac{\Delta \langle \tau \rangle}{T} &= V_r - P_s
\end{aligned}
$$

This $\Delta \langle \tau \rangle$ is the additional time gained/lost. This
must be negative to **reduce** the total time spent on tasks. It is evident that
to reduce this, the probability of success simply needs to be greater than the
verification ratio.

### Try Until Success

Eventually, an abundance of models with automated retries and randomization will
be used to solve tasks. What happens in the best case?

The cases are simple:

- $P_s$ chance of success, spent $V_rT$ time
- $(1-P_s)$ chance to fail first time, $P_s$ after to succeed, spent $2V_rT$ time
- etc

The expected time is the sum of the time spent in each of these cases times
their probability:

$$
\begin{aligned}
\langle \tau \rangle &= \sum \limits_{i=1}^{\infty} P_s (1 - P_s)^{i-1} i V_r T \cr
&= V_r T \sum \limits_{i=1}^{\infty} P_s (1 - P_s)^{i-1} i
\end{aligned}
$$

This is a well known formula which can be derived by simply taking
$f = (1 - P_s)$, the sum $S = \sum \limits_{i=1}^{\infty} f^{i-1} i$ and
subtracting these two quantities: $S - f S$. The derivation is omitted and left
as an exercise.

The result is then:

$$
\begin{aligned}
\langle \tau \rangle &= V_r T \sum \limits_{i=1}^{\infty} P_s (1 - P_s)^{i-1} i \cr
&= V_r T \frac{1}{P_s} = T \frac{V_r}{P_s}
\end{aligned}
$$

or, in terms of $\frac{\langle \Delta \tau \rangle}{T}$:

$$
\begin{aligned}
\frac{\langle \Delta \tau \rangle}{T} &= \frac{V_r}{P_s} - 1 \cr
&= \frac{1}{P_s} \left ( V_r - P_s \right )
\end{aligned}
$$

In this case, if attempts continue, assuming the model will be right $P_s$ (so
the prob of success doesn't change knowing the model previously failed for
example), time is saved if $P_s > V_r$. This is the same result as for the fire
and forget case.

### Which Is Better?

This raises an interesting point. As agent development progresses, which case is
better to optimize for?

Taking the ratio:

$$
\begin{aligned}
\frac{\mathrm{FireAndForget}}{\mathrm{TryForever}} &= \frac{V_r - P_s}{\frac{1}{P_s} \left ( V_r - P_s \right) } \cr
&= P_s
\end{aligned}
$$

it depends solely on $P_s$. Given that $0 \le P_s \le 1$, this ratio is less than 1, suggesting that the try forever approach has a larger gain $\langle \Delta \tau \rangle$.

This brings in an interesting question. This trend is moving towards the
direction of having a multitude of agents with different strengths. At some
point, the diversity of agents will likely make the try forever approach
eventually succeed in most cases and be worth the effort. This is an interesting
avenue of innovation and possibility.

### Some Examples

When are some cases where this might be useful? Here are some tips from
practical examples. Note that this is not quantitative (future posts may provide
quantitative results) and feedback is welcome.

#### Fixing Unit Tests

These types of problems are very easy to verify. Tests can be verified in a
semi-automated way by simply running them. They still need to be manually
reviewed for correctness and sometimes tweaked. Variety can be introduced
by randomly picking some sample code changes with test cases, making the try
forever case also more favorable.

#### Oncall Troubleshooting

Oncall analysis/troubleshooting tends to involve needle in a haystack type
problems. The culprit is sometimes hard to find, but easy to verify. Since this
is a retrieval task, likely increasing model temperatures or randomizing inputs
would produce enough variety.

#### Document Writing

The verification overhead $V_r$ for these problems tends to be pretty high.
Manual writing is likely preferable unless the writing is a very basic
information retrieval task (ex: take this bug and add it to the troubleshooting
page or FAQ).

Here are some estimates (again, not quantitative!):

| Task Type              | Verification Ratio | Probability of Success | Fire And Forget % Time saved | Try Until Success % Time Saved |
| ---------------------- | ------------------ | ---------------------- | ---------------------------- | ------------------------------ |
| Bug Fixing             | 5%                 | 80%                    | 75%                          | 93.75%                         |
| Oncall Troubleshooting | 5%                 | 60%                    | 55%                          | 91.7%                          |
| Document Writing       | 60%                | 75%                    | 15%                          | 20%                            |

### Conclusion

How does this align with general experiences using agents? What strategies have
yielded the most time savings? Any interesting links to share?

## Citation

```
@misc{jlhermitte2025agentsverifiers,
  title={When Are Agents Worth It?},
  author={Julien R. Lhermitte},
  year={2025},
  howpublished={\url{https://jrmlhermitte.github.io/2025/08/12/agents-and-verification.html}}
}
```
