# Verification methods non-mathematicians can run

Premise: users usually can't check a proof line by line, but they can run checks. A good verification plan turns "trust the model" into "run a check and look at the result". Pick the 2–4 most effective checks for the problem and write them as concrete steps (with specific parameter values and expected results), not just method names.

## Contents

1. Special-case reduction
2. Limits and asymptotic behaviour
3. Units / dimensions
4. Numerical comparison (simulation, Monte Carlo, brute force)
5. Symbolic computation
6. Adversarial review
7. Consistency and robustness checks
8. Red flags

---

## 1. Special-case reduction

Set the parameters to a case whose answer you already know and see whether the result reduces to it.

- A multi-compartment model's result should reduce to the one-compartment formula when only one compartment remains.
- A damped oscillator's frequency formula should give $\omega_0=\sqrt{k/m}$ when damping is 0.
- For a claim about any dimension $d$, plug in $d=1$ and work it out by hand.

Phrasing: "With [parameter values] the formula must give [known value]; if it doesn't, the derivation is wrong."

## 2. Limits and asymptotic behaviour

As parameters go to 0 or ∞, the result should behave as intuition or known physical/biological laws dictate.

- As dose → 0, concentration → 0; as the dosing interval → ∞, there should be no accumulation.
- As noise → 0, the stochastic model's result should converge to the deterministic model's.
- As sample size → ∞, the estimator's variance should go to 0 (if consistency is claimed).

## 3. Units / dimensions

The cheapest and most often skipped check. Both sides of a formula must have the same units; the arguments of $\exp(\cdot)$, $\log(\cdot)$, $\sin(\cdot)$ must be dimensionless. Many derivation errors show up directly as inconsistent units.

## 4. Numerical comparison

The most convincing check: compute the same quantity in a way **independent** of the derivation and compare.

- **Direct simulation**: solve the ODE/PDE numerically (e.g. `scipy.integrate.solve_ivp`) and compare with the closed form at several time points.
- **Monte Carlo**: for expectations, probabilities and variances, estimate by sampling with a confidence interval and check that the theoretical value falls inside.
- **Brute force**: for combinatorial or discrete problems, enumerate at small scale.
- **Parameter sweeps**: compare over a grid, not at a single point — many errors appear only in part of the parameter space.

When asking the model for code, ask it to: use only common libraries (numpy / scipy / sympy / matplotlib), fix the random seed, and print the theoretical value, the numerical value and the relative error. The user runs it — don't rely on run results the model reports.

## 5. Symbolic computation

Use sympy (or Mathematica / Maple) to verify key algebraic steps: derivatives, integrals, simplification, equation solving, series expansion.

```python
import sympy as sp
t, k, D, V = sp.symbols('t k D V', positive=True)
C = D/V*sp.exp(-k*t)
assert sp.simplify(sp.diff(C, t) + k*C) == 0   # check that C satisfies dC/dt = -kC
```

Well suited to "does this solution actually satisfy the equation?" — verifying a solution is often much easier than finding it.

## 6. Adversarial review

Give the problem statement and the answer to **another model or a fresh conversation**, using the T8 reviewer template in `prompt-templates.md`. Key points:

- The role is "find errors", not "confirm";
- Require a step-by-step marking of "correct / gap / error";
- Require a list of assumptions the answer used that are not in the problem — the most common source of errors;
- Require checking that cited theorems exist and that their conditions hold.

Two independent models both judging an answer correct is far more credible than one model confidently saying "correct" — but it is still not a proof.

## 7. Consistency and robustness checks

- **Solve another way**: solve independently by another method (e.g. an energy method, or numerically) and see if the results agree.
- **Rephrase the problem**: ask again with an equivalent but different formulation and see if the conclusion is stable.
- **Sample several times**: ask the same prompt a few times; a conclusion that flips back and forth signals uncertainty.
- **Probe the boundary**: ask "when does this conclusion fail?" — be wary if it can't say.

## 8. Red flags

Be more cautious when you see:

- A conclusion much "better" than expected, or one that overturns common knowledge in the field, without explaining why nobody noticed before;
- Cited theorems you can't find, or names that are almost-but-not-quite right;
- "Obviously", "it is easy to show", "without loss of generality" at key steps;
- Assumptions quietly changing mid-derivation (e.g. a general distribution at the start, Gaussian properties used later);
- A proof of a special case stated as a general conclusion;
- Exchanging limits, swapping sums and integrals, or differentiating unknown functions without any justification;
- Numerical verification done only at one carefully chosen point.
