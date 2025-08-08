<h1 id="introduction">Introduction</h1><a href="/pdfs/kernel_machines.pdf">(pdf version)</a>
<p>Domingos et. al demonstrates a relation between the similarity of
training data samples and the inference point over the history of the
changes of parameters for any model learned by gradient descent<span
class="citation"
data-cites="domingos2020modellearnedgradientdescent"><a href="#ref-domingos2020modellearnedgradientdescent">[1]</a></span>.</p>
<p>Assuming a model <span class="math inline">\(f(x; \omega)\)</span>,
parametrized by weights <span class="math inline">\(\omega\)</span> and
taking input <span class="math inline">\(x\)</span>, they arrive at the
following interesting result: <span
class="math display">\[\lim_{\epsilon \rightarrow 0} y = y_0 -
\int_{c(t)}\sum\limits_{i=1}^{m}L&#39;(y_i^*, y_i)
    \vec{\nabla}_\omega f(\vec{x}; \vec{\omega}(t)) \cdot
\vec{\nabla}_\omega f(\vec{x}_i; \vec{\omega}(t)) dt
    \tag{1}\]</span></p>
<figure id="pathcontinuous">
<div class="center">
<img src="/images/2025-08-08-path.png" style="width:5in" />
</div>
<figcaption>Original visual figure of the path kernel and the similarity
from <span class="citation"
data-cites="domingos2020modellearnedgradientdescent"><a href="#ref-domingos2020modellearnedgradientdescent">[1]</a></span>.</figcaption>
</figure>
<p>What this suggests is that no matter where our model starts (<span
class="math inline">\(y_0\)</span>), the movement towards the trained
model can be approximated as a sum of terms weighted by the similarity
between the gradient of the inference point <span
class="math inline">\(\vec{x}\)</span> and the training data point. This
suggests that for a smooth enough parameter space, that the strongest
contributing terms in the sum are the ones where the vector <span
class="math inline">\(\vec{x}\)</span> are closest. Intuitively, this is
because we can assume some correlation of gradients for neighboring
points but no correlation (i.e. random) for other points.</p>
<h1 id="the-open-question">The Open Question</h1>
<p>The current scheme uses gradient flows <span class="citation"
data-cites="ambrosioGradientFlowsMetric2008"><a href="#ref-ambrosioGradientFlowsMetric2008">[2]</a></span>
and requires that the learning rate <span
class="math inline">\(\epsilon\)</span> be very small. When training, I
believe it may be an open question whether this approximation holds. In
particular, it assumes we are not in a local minimum. Being in a local
minimum would require us to jump over a max (and reach a point where
<span class="math inline">\(\epsilon=0\)</span>).</p>
<p>They mention<span class="citation"
data-cites="domingos2020modellearnedgradientdescent"><a href="#ref-domingos2020modellearnedgradientdescent">[1]</a></span>:</p>
<blockquote>
<p>This is standard in the analysis of gradient descent, and is also
generally a good approximation in practice, since the learning rate has
to be quite small in order to avoid divergence (e.g., <span
class="math inline">\(\epsilon = 10^{-3}\)</span>) <span
class="citation"
data-cites="Goodfellow-et-al-2016"><a href="#ref-Goodfellow-et-al-2016">[3]</a></span>.
Nevertheless, it remains an open question to what extent models learned
by gradient descent can still be approximated by kernel machines outside
of this regime.</p>
</blockquote>
<p>Intuitively this makes a bit of sense. Practically, we observe this
with basic LLMs where when giving a very simple prompt asking solve a
math specific question with the prefix “You are an MIT student” leads to
a higher likelihood better answer than without.</p>
<p>This document aims to discuss what happens when we are outside of
this regime. How is this approximation affected? To what extent does it
still hold?</p>
<h1 id="discrete-proof">Discrete Proof</h1>
<p>Before we may discuss the limits, we must derive a discrete version
of the original proof <span class="citation"
data-cites="domingos2020modellearnedgradientdescent"><a href="#ref-domingos2020modellearnedgradientdescent">[1]</a></span>.
We will also find that the key insight that is needed to prove this is
that we assume the model can be approximated by a hyperplane (i.e. can
be Taylor expanded keeping up to first order derviative terms).</p>
<p>When we undergo gradient descent, we are more or less updating our
weights according to a rule: <span
class="math display">\[\vec{\omega}_{n+1} = \vec{\omega}_{n} -
\alpha_{n+1} \vec{\nabla}L(\vec{\omega}_n)\]</span></p>
<p>where <span class="math inline">\(\alpha_{n+1}\)</span> is the
learning rate, <span class="math inline">\(\vec{\omega}_n\)</span> the
weights at timestep <span class="math inline">\(n\)</span> and <span
class="math inline">\(L\)</span> the loss. Note that the loss is
implicity dependent upon the training samples <span
class="math inline">\((x_i, y_i^*)\)</span>.</p>
<p>We can as in <span class="citation"
data-cites="domingos2020modellearnedgradientdescent"><a href="#ref-domingos2020modellearnedgradientdescent">[1]</a></span>
rewrite the gradient of the total loss in terms of a sum over individual
gradient losses with respect to the input:</p>
<p><span class="math display">\[\begin{aligned}
    \vec{\nabla}L(\vec{\omega}_n) &amp; = &amp; \sum \limits_j
\frac{\partial L}{\partial \omega_j}(\vec{\omega}_n) \hat{\omega_j} \\
    &amp; = &amp;  \sum\limits_{j, m} \frac{\partial L}{\partial y_m}
\frac{\partial y_m}{\partial \omega_j} (\vec{\omega}_n) \hat{\omega_j}\\
    &amp; = &amp;  \sum\limits_m \frac{\partial L}{\partial y_m}
\vec{\nabla}_{\omega} f(\vec{x}_m; \vec{\omega}_n)
\end{aligned}\]</span></p>
<p>where we sum over <span class="math inline">\(m\)</span> training
data points with <span class="math inline">\(y_m = f(\vec{x}_m;
\vec{\omega}_n)\)</span>. Finally, we have:</p>
<p><span class="math display">\[\vec{\omega}_{n+1} - \vec{\omega}_{n} =
\sum\limits_m \frac{\partial L}{\partial y_m} \vec{\nabla}_{\omega}
f(\vec{x}_m; \vec{\omega}_n) \tag{2}\]</span></p>
<p>The change of the model at each timestep is: <span
class="math display">\[\Delta f = f(\vec{x}; \vec{\omega}_{n+1}) -
f(\vec{x}; \vec{\omega}_n)\]</span></p>
<p>We can rewrite <span class="math inline">\(f\)</span> as a Tayor
series, centered at <span class="math inline">\(\vec{\omega}_n\)</span>:
<span class="math display">\[f(\vec{x}; \vec{\omega}_{n+1}) = f(\vec{x};
\vec{\omega}_n) +
    \vec{\nabla}_\omega f (\vec{\omega}_n) \cdot \left
(\vec{\omega}_{n+1} - \vec{\omega}_n
    \right ) + O(|\omega|^2)\]</span></p>
<p>The key step here is to assume that our function can be approximated
by a hyperplane. We can then ignore the <span
class="math inline">\(O(|\omega|^2)\)</span> terms.</p>
<p>If this is true, then we can write the delta as:</p>
<p><span class="math display">\[\begin{aligned}
    \Delta f &amp; = &amp; f(\vec{x}; \vec{\omega}_{n+1}) - f(\vec{x};
\vec{\omega}_n) \\
    &amp; = &amp; f(\vec{x}; \vec{\omega}_{n}) -  \alpha_{n+1}
\vec{\nabla}_\omega f \cdot \left
    (\vec{\omega}_{n+1} - \vec{\omega}_n \right ) - f(\vec{x};
\vec{\omega}_n) \\
    &amp; = &amp; -  \alpha_{n+1} \vec{\nabla}_\omega f \cdot \left
    (\vec{\omega}_{n+1} - \vec{\omega}_n \right )
\end{aligned}\]</span></p>
<p>If we then replace <span class="math inline">\(\vec{\omega}_{n+1} -
\vec{\omega}_n\)</span> with Equation 2, we get:</p>
<p><span class="math display">\[\begin{aligned}
    \Delta f &amp; = &amp; -  \alpha_{n+1} \vec{\nabla}_\omega
f(\vec{x}) \cdot
     \sum\limits_m \frac{\partial L}{\partial y_m} \vec{\nabla}_{\omega}
f(\vec{x}_m; \vec{\omega}_n)\\
      &amp; = &amp; - \alpha_{n+1} \sum \limits_m  \frac{\partial
L}{\partial y_m}
      \vec{\nabla}_\omega f(\vec{x}; \vec{\omega}_n) \cdot
\vec{\nabla}_{\omega} f(\vec{x}_m;
      \vec{\omega}_n)
\end{aligned}\]</span></p>
<p>Finally, the final model will just be the sum of the <span
class="math inline">\(\Delta\)</span>s plus the initial model:</p>
<p><span class="math display">\[f(\vec{x}; \vec{\omega_N}) = f(\vec{x};
\vec{\omega_0}) + \sum \limits_{k=0}^{N-1} -\alpha_{k+1} \sum
\limits_m  \frac{\partial L}{\partial y_m}
      \vec{\nabla}_\omega f(\vec{x}; \vec{\omega}_k) \cdot
\vec{\nabla}_{\omega} f(\vec{x}_m;
      \vec{\omega}_k)\]</span></p>
<p>and we end with: <span class="math display">\[\begin{array}{rcl}
f(\vec{x}; \vec{\omega_N}) &amp; = &amp; f(\vec{x}; \vec{\omega_0}) + \\
&amp; &amp; \sum \limits_{k=0}^{N-1} -\alpha_{k+1} \sum \limits_m
L&#39;(y_i^*, y_i)
      \vec{\nabla}_\omega f(\vec{x}; \vec{\omega}_k) \cdot
\vec{\nabla}_{\omega} f(\vec{x}_m;
      \vec{\omega}_k) \tag{3}
\end{array}\]</span></p>
<p>which is the discrete version of Equation 1. Note that this is a
similar result, except that we find that the learning rate can be
variable so long as the function can locally be approximated by a
hyperplane.</p>
<h1 id="discussion">Discussion</h1>
<p><strong>Not Quite Kernel Machine With Local Minima (But Still
Approximate?)</strong></p>
<p>Equation 3 demonstrates to us that we need simply assume the method
is locally approximated by a hyperplane at each time step.</p>
<p>We can quickly see this breaks down when this is not true. When can
this happen? This can happen where the slope of the approximate
hyperplane is negligible and we must keep higher order terms. This could
happen if we happen to be in a local minima or saddle point. In one
dimension, this is easy to visualize.</p>
<figure id="gdexample">
<div class="center">
<img src="/images/2025-08-08-gd-example.png" style="width:3in" />
</div>
<figcaption>Example of one iteration of gradient descent where the local
minima is not the global minima. The only way to get past this minimal
is to move to a regime where we hit zero slope, and thus the higher
order terms must be included.</figcaption>
</figure>
<p>See Figure <a href="#gdexample" data-reference-type="ref"
data-reference="gdexample">2</a>. The only way to get to the global
minima is to move a <span class="math inline">\(\Delta
\vec{\omega}\)</span> where <span class="math inline">\(f\)</span>
cannot be approximated by the first order terms in <span
class="math inline">\(\omega\)</span>. In this case, it means we must
account for additional terms. Roughly, we can simply not ignore the
Taylor approximation. Let us group the complex timesteps into <span
class="math inline">\(T_c\)</span>. We see we have a sum like:</p>
<p><span class="math display">\[\begin{aligned}
    f(\vec{x}; \vec{\omega_N}) &amp; = &amp; f(\vec{x}; \vec{\omega_0})
+ \nonumber \\
&amp; &amp; \sum \limits_{k=0}^{N-1} -\alpha_{k+1} \sum \limits_m
L&#39;(y_i^*, y_i)
      \vec{\nabla}_\omega f(\vec{x}; \vec{\omega}_k) \cdot
\vec{\nabla}_{\omega} f(\vec{x}_m;
      \vec{\omega}_k) \nonumber \\
&amp; &amp; + \sum \limits_{\omega_k \in T_c} - \alpha_{k+1}
O(|\omega_{k+1} - \omega_k|^2)
\end{aligned}\]</span></p>
<p>Whether this terms contributes or not is an open question, and
something that will have to be empirically determined. Roughly, I think
it’s likely related to ratio of local minima encountered to total steps.
Training usually involves some sort of regularization which attempts to
smooth the space. I would expect this ratio to be small, and likely
there to be little contribution.</p>
<p><strong>Kernel Machine When Initialized</strong></p>
<p>Note that a simple solution to this problem would be to simply
initialize parameters close to the global minimum. However, this weakens
the kernel machines argument. Gradient descent is only a kernel machine
if we started near the solution.</p>
<h1 id="follow-up">Follow Up</h1>
<p><a
href="https://nbviewer.org/github/Janko-dev/path_kernel_machine/blob/main/path_kernel_machine.ipynb">Janko-dev’s
notebook</a> demonstrates this empirically. It is possible by tweaking
the paths and ensuring we’re in a local minimum, we may see if we have
agreement or not, and how big the <span
class="math inline">\(O(|\omega|^2)\)</span> terms are.</p>
<div id="refs" class="references csl-bib-body" role="list">
<div id="ref-domingos2020modellearnedgradientdescent" class="csl-entry"
role="listitem">
<div class="csl-left-margin">[1] </div><div class="csl-right-inline"><a
href="https://arxiv.org/abs/2012.00152">P. Domingos, (2020)</a>.</div>
</div>
<div id="ref-ambrosioGradientFlowsMetric2008" class="csl-entry"
role="listitem">
<div class="csl-left-margin">[2] </div><div class="csl-right-inline">L.
Ambrosio, N. Gigli, and G. Savaré, <em>Gradient Flows in Metric Spaces
and in the Space of Probability Measures</em>, 2. ed
(<span>Birkhäuser</span>, <span>Basel</span>, 2008).</div>
</div>
<div id="ref-Goodfellow-et-al-2016" class="csl-entry" role="listitem">
<div class="csl-left-margin">[3] </div><div class="csl-right-inline">I.
Goodfellow, Y. Bengio, and A. Courville, <em><a
href="http://www.deeplearningbook.org">Deep Learning</a></em> (MIT
Press, 2016).</div>
</div>
</div>
