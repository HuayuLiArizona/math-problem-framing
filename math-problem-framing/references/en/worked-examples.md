# Worked examples: from vague description to final prompt

Three examples from clinical pharmacology, synthetic biology and machine learning. Focus on *what choice each step made and why*, not on copying the content. In real use the prompt is tailored more closely to the user's situation. Chinese versions are in `references/zh/worked-examples.md`.

---

## Example 1: How long to recover after a missed dose (clinical / pharmacokinetics)

**User's words**: "A patient takes a drug once a day and occasionally forgets a dose. I want to know how long it takes for the blood level to get back to normal after a missed dose, and whether they should make it up. I'd like GPT to work it out but don't know how to ask."

**Stage 0**: entry B (vaguely knows it's "pharmacokinetics"); answer shape = derivation + comparison of strategies.

**Stage 1 key questions** (each changes the math):
1. Roughly what is the drug's half-life? (Determines $k\tau$, the only dimensionless number that really matters.)
2. Is oral absorption fast (peak within 1–2 hours) or slow? (Decides whether absorption can be treated as instantaneous.)
3. Does "normal level" mean the trough stays above an efficacy threshold, or the peak stays below a toxicity threshold?
4. Which make-up strategies do you want to compare? (skip / take it when remembered / double the next dose)

Suppose the user answers: half-life about 12 h, fast absorption, cares about troughs, compare all three.

**Stage 2 formalization card (compact)**:
- One compartment, first-order elimination, instantaneous absorption (simplification, flagged); dose $D$, volume of distribution $V$, elimination rate $k=\ln 2/t_{1/2}$, dosing interval $\tau=24\,\text{h}$.
- Linear pharmacokinetics → superposition holds (key assumption, flagged).
- Goal: (a) the trough deficit at the end of the $n$-th interval after the dose at time 0 is missed at steady state, relative to the steady-state trough, as a function of $n$ and $k\tau$; (b) the smallest $n$ with deficit $\le\varepsilon$; (c) trough and peak trajectories under three strategies, with late-dose delay $\delta\in(0,\tau)$.

**Back-translation highlights**: "I'm assuming this drug is metabolized linearly over the usual dose range — double the dose, double the concentration. If its metabolism saturates (phenytoin, for example), the conclusions below don't apply. I'm also assuming the drug enters the blood immediately when taken; that's a reasonable approximation for fast-absorbed drugs but overstates the peak after a late dose."

**Final prompt**:

```markdown
# Task
Derive closed-form expressions for the effect of one missed dose on plasma concentration under the one-compartment model below, and compare three recovery strategies. If a quantity has no closed form, give an exact implicit expression and how to evaluate it.
Before deriving, restate the problem and list any ambiguities.

# Context
Clinical question: a once-daily oral drug (half-life ≈ 12 h, rapid absorption). Efficacy depends on the trough concentration staying near its steady-state value. The formal statement below is authoritative.

# Setup and notation
- Dose $D>0$ (mg), volume of distribution $V>0$ (L), elimination rate constant $k>0$ (1/h), dosing interval $\tau>0$ (h). Write $r=e^{-k\tau}\in(0,1)$.
- Doses are given at times $t_j=j\tau$, $j\in\mathbb Z$ (an infinite past, so the system is at steady state before the missed dose).
- Concentration after a single bolus at time $s$: $C(t)=\frac{D}{V}e^{-k(t-s)}$ for $t\ge s$, and 0 before.

# Assumptions
A1. Linear pharmacokinetics, so concentrations from different doses superpose. (Essential.)
A2. Instantaneous absorption (IV-bolus approximation). (Simplification — state how conclusions change with first-order absorption rate $k_a$.)
A3. Parameters constant over time.

# Problem
1. The dose at $t_0=0$ is missed; all others are taken. Let $C_{\min,ss}$ be the steady-state trough (just before a dose). For $n\ge1$, find the trough just before the dose at $t=n\tau$, and the relative deficit $\Delta_n=1-C(n\tau^-)/C_{\min,ss}$ as a function of $n$ and $r$ only.
2. For a tolerance $\varepsilon\in(0,1)$, find the smallest integer $n$ with $\Delta_n\le\varepsilon$.
3. Compare strategies: (S1) skip the missed dose; (S2) take it late at $t=\delta$, $0<\delta<\tau$; (S3) take $2D$ at $t=\tau$. For each, give the trough sequence and the maximum peak concentration over $t\ge0$, relative to the steady-state peak $C_{\max,ss}$.

# Scope
No population variability, no nonlinear kinetics, no specific drug recommendations — only the mathematics of this model.

# Deliverable
- Derivations using superposition; mark where A1 and A2 are used.
- Final formulas for $\Delta_n$, the minimal $n$, and peak ratios for S1–S3, each on a separate line.
- A table of $\Delta_1,\Delta_2,\Delta_3$ and peak ratios for $t_{1/2}\in\{6, 12, 24, 48\}$ h with $\tau=24$ h.

# Self-checks
- Limit $k\tau\to\infty$ (no accumulation): the missed dose should only affect the interval $[0,\tau)$.
- Units: confirm all exponents are dimensionless.
- Provide a Python script that simulates the dosing train by direct summation over 200 past doses and compares with your formulas for S1–S3.
```

**Verification plan**: run the attached script to compare the formulas with direct summation; check that at $t_{1/2}=6$ h (almost no accumulation) the effect of a missed dose has essentially vanished within a day; give the prompt and answer to another model with the reviewer template to check the S3 peak calculation.

**Search keywords**: one-compartment model, superposition principle, accumulation ratio, steady-state trough, missed dose simulation.

---

## Example 2: When does a self-activating gene circuit act as a "switch"? (synthetic biology)

**User's words**: "I built a circuit where a transcription factor activates its own expression. Some cells are high, some low. I want to know, in theory, under what parameter conditions two such populations appear, so I can design modifications."

**Stage 0**: entry A (doesn't know which math). Stage 3 judgment: two populations → could be bistability (deterministic), stochastic switching, or extrinsic cell-to-cell differences. Name all three in the delivery; the user chooses to test bistability first. Answer shape = derive a parameter region.

**Stage 1 key questions**:
1. Is activation cooperative (does the TF act as a dimer or multimer)? — bistability usually needs Hill coefficient $n>1$
2. Is there basal leaky expression?
3. Is the protein removed mainly by degradation or by dilution through division? (Determines $\gamma$.)
4. Which parameter can you tune (promoter strength, RBS, degradation tag)? — this decides which parameter the bifurcation diagram should be drawn along.

**Stage 2 card highlights**: $\dot x=\alpha_0+\alpha\frac{x^n}{K^n+x^n}-\gamma x$, $x\ge0$ the protein concentration; after nondimensionalizing $u=x/K$, $\tau=\gamma t$ three parameters remain: $a_0=\alpha_0/(\gamma K)$, $a=\alpha/(\gamma K)$, $n$. Goal: for each $n$, the region of the $(a,a_0)$ plane with exactly two stable equilibria, bounded by saddle-node bifurcation curves.

**Back-translation highlights**: "This model treats all cells as identical, with continuously varying concentrations. It can answer 'do two stable states exist', but not 'how often does a cell switch at random' — if that's what you care about more, you need a stochastic model, which is a different prompt."

**Final prompt** (core excerpt):

```markdown
# Task
Determine exactly when the ODE below has two stable equilibria (bistability), and describe the boundary of that region. If no closed form exists for the boundary, give a parametric representation.

# Setup
$\dfrac{du}{d\tau}=f(u)=a_0+a\dfrac{u^n}{1+u^n}-u$, $u\ge0$, with $a>0$, $a_0\ge0$, and Hill coefficient $n\ge1$ (real).

# Problem
1. For which $(n,a,a_0)$ does $f$ have three nonnegative zeros, two of them stable? (Note $u=0$ is a zero when $a_0=0$.) Prove that $n>1$ is necessary.
2. Give the saddle-node bifurcation curve in the $(a,a_0)$ plane for fixed $n$, parametrized by the equilibrium value $u^*$ (use $f(u^*)=0$, $f'(u^*)=0$).
3. Find the cusp point where the bistable region ends, as a function of $n$.

# Scope
Deterministic model only; no stochastic switching rates; no spatial effects.

# Deliverable
Derivations; final region description; a description of the hysteresis loop as $a$ varies with $a_0$ fixed.

# Self-checks
- Confirm monostability for $n=1$ for all $a, a_0$.
- Provide Python code that, for $n=2$ and a grid of $(a,a_0)$, counts zeros of $f$ numerically and overlays your analytic boundary.
```

**Search keywords**: positive autoregulation, bistability, saddle-node bifurcation, cusp catastrophe, hysteresis, Hill function, (stochastic direction) chemical master equation, mean first passage time.

---

## Example 3: REINFORCE gradient variance (machine learning)

**User's words**: "I'm training a discrete latent-variable model with a score-function gradient estimator and the variance is huge. I've heard a baseline helps, but I don't know how to choose it or how much it can reduce variance in theory."

**Stage 0**: entry B; answer shape = derivation (variance expression and optimal baseline).

**Stage 1 key questions**:
1. Is the baseline a single constant, one constant per parameter coordinate, or may it depend on the input $x$? (Decides the form of the optimum.)
2. Single-sample or $K$-sample estimator? (Decides whether a leave-one-out baseline is available.)
3. Do you care about total variance (the trace) or each coordinate?

**Stage 2 card highlights**: $z\sim p_\theta(z)$ (discrete), $f(z)$ independent of $\theta$; estimator $\hat g_b=(f(z)-b)\,\nabla_\theta\log p_\theta(z)$; goal: minimize $\mathrm{tr}\,\mathrm{Cov}(\hat g_b)$ over $b$; distinguish the scalar-$b$ and vector (per-coordinate) $b$ versions.

**Final prompt** (core excerpt):

```markdown
# Task
Derive the variance of the score-function (REINFORCE) gradient estimator with a baseline, and find the variance-minimizing baseline. Prove unbiasedness conditions.

# Setup
$z\sim p_\theta$ on a finite set $\mathcal Z$, $\theta\in\mathbb R^m$, $p_\theta(z)>0$ and differentiable in $\theta$ for all $z$. $f:\mathcal Z\to\mathbb R$ does not depend on $\theta$. Let $s(z)=\nabla_\theta\log p_\theta(z)\in\mathbb R^m$. Estimator: $\hat g_b=(f(z)-b)\,s(z)$.

# Problem
1. Show $\mathbb E[\hat g_b]=\nabla_\theta\mathbb E[f(z)]$ for any $b$ not depending on $z$. Give an example showing bias when $b$ depends on $z$.
2. (Scalar $b$.) Find $b^\star$ minimizing $\mathrm{tr}\,\mathrm{Cov}(\hat g_b)$ and the minimal value.
3. (Per-coordinate $b\in\mathbb R^m$, estimator $(f(z)-b_i)s_i(z)$.) Find the optimal $b_i^\star$.
4. Compare $b^\star$ with the common choice $b=\mathbb E[f(z)]$: when are they equal, and give an example where they differ substantially.

# Scope
Single-sample estimator only; no reparameterization or continuous relaxations.

# Self-checks
Python Monte Carlo check for $z\sim\mathrm{Bernoulli}(\sigma(\theta))$, $m=1$, a chosen $f$: compare empirical variance for $b\in\{0,\mathbb E f, b^\star\}$ with your formulas.
```

**Search keywords**: score function estimator, REINFORCE, control variates, optimal baseline, Rao–Blackwellization, RLOO (leave-one-out baseline).
