# Prompt templates (by answer shape)

Fill in the `[...]` placeholders; delete sections you don't need rather than leaving empty shells. Chinese versions of these templates are in `references/zh/prompt-templates.md`.

## Contents

- T1 Method map (stage one of a two-stage prompt: classify, don't solve)
- T2 Derivation / closed form
- T3 Prove or disprove a property
- T4 Bound / asymptotic order / scaling
- T5 Model choice
- T6 Algorithm / numerical scheme
- T7 Statistical analysis plan
- T8 Reviewer: check an existing derivation
- T9 Follow-up templates

---

## T1 Method map

Use for entry A (the user doesn't know what kind of math this is). The aim is to let the LLM show the user which mathematical problems this could be formalized as and what each can answer, so the user can choose. Don't let it solve at this stage.

```markdown
# Task
I am a [field] researcher, not a mathematician. I will describe a problem from my work. Do NOT solve it yet. Instead, map out how it could be formalized mathematically, so I can choose a formulation.

# The situation (in domain terms)
[3–10 lines: what the system is, what is observed, what puzzles me, what decision depends on the answer.]
[A concrete toy instance with numbers, if available.]

# What I already know / have tried
[Known results, papers, failed attempts.]

# What I need from you
1. Restate the problem in your own words and list the ambiguities that would change the mathematics.
2. Propose 2–4 candidate mathematical formulations. For each:
   - the mathematical objects (variables, spaces, random variables) and the precise question;
   - the key assumptions it makes and which of them are most questionable for my setting;
   - the branch of mathematics and the standard method names;
   - what kind of answer it can deliver (closed form, qualitative condition, bound, numerical procedure) and roughly how hard it is;
   - 1–3 standard references (textbook chapter or classic paper). Only cite works you are confident exist.
3. Recommend which formulation to try first and why, and list the questions I should answer to decide between them.
```

## T2 Derivation / closed form

```markdown
# Task
Derive [the quantity / expression] under the model and assumptions below. If a closed form does not exist, say so and give the best available alternative (series, asymptotic approximation with its regime of validity, or an implicit equation plus how to solve it numerically).
Before deriving, restate the problem and list any ambiguities; if an ambiguity changes the answer, state which interpretation you adopt.

# Context
[≤5 lines of domain background. The formal statement below is authoritative.]

# Setup and notation
[Each symbol: type, range, units, meaning.]

# Model
[Equations / data-generating process.]

# Assumptions
A1. … (essential)
A2. … (simplification for tractability — tell me how the result changes if it is relaxed)

# Problem
[Precise target, e.g. "Find $C_{ss}(t)$ for $t\ge 0$ in closed form, as a function of $D,\tau,k,V$."]

# Scope
[What is not needed, e.g. "No numerical simulation needed; no discussion of nonlinear PK."]

# Deliverable
- Step-by-step derivation; state which assumption is used at each step.
- Final result on a separate line, with its regime of validity.
- If the result holds only in a special case, state the restriction explicitly.

# Self-checks
- Reduce the result to [known special case] and confirm it matches [known answer].
- Check units / dimensions.
- Check limits: [parameter → 0, parameter → ∞].
- Provide a short Python (numpy/sympy/scipy) script that numerically verifies the formula on [toy instance].
```

## T3 Prove or disprove a property

```markdown
# Task
Prove or disprove the statement below. A complete proof or a rigorous refutation both count as success. If the statement is false as written but true under a natural extra assumption, give the counterexample first, then state and prove the corrected version.
Before starting, restate the statement and list any ambiguities.

# Setup and notation
[Complete definitions; quantifiers explicit; what is fixed and what varies.]

# Assumptions
A1. …

# Statement
[For all … there exists … such that … . Constants may depend on [list] but not on [list].]

# Scope
[Near-miss statements that are NOT being asked, e.g. "not asking for convergence of a subsequence".]

# What counts as a counterexample
[E.g. "explicit parameter values and initial conditions, with a computation or numerical demonstration that the property fails".]

# Deliverable
- If the argument only works under an extra condition ([linear / Gaussian / small step size / d=1 …]), state that restriction explicitly instead of silently extending it.
- Mark every step where each assumption is used.
- Cite any theorem you use by name and standard source; do not rely on results you are not sure exist.

# Directions (optional)
[Possible starting points; known partial results and what they do NOT cover.]
[Acceptable milestone: the case … . A milestone does not replace the full statement.]

# Self-checks
- Verify the argument recovers [known simple case].
- Identify the weakest step and how confident you are in it.
```

## T4 Bound / asymptotic order / scaling

```markdown
# Task
Determine how [quantity Q] scales with [parameters] in the regime specified below. Give upper and lower bounds if possible; say whether the bound is tight.

# Setup and notation
[...]

# Regime
[Which parameters go to which limits, at what relative rates, what is held fixed. Order of limits if more than one.]

# Problem
[E.g. "Find exponents a, b such that $Q = \Theta(n^{a} d^{b})$ as $n\to\infty$ with $d/n\to\gamma\in(0,1)$ fixed." State whether constants matter.]

# Deliverable
- The scaling law, with what the hidden constants depend on.
- The mechanism in one paragraph (why this scaling).
- Where the bound breaks down.

# Self-checks
- A heuristic or dimensional-analysis cross-check.
- A simulation design (sizes, number of repeats) that would test the predicted exponents, with code.
```

## T5 Model choice

```markdown
# Task
Help me choose a mathematical model for [phenomenon]. Compare the candidate models below (add others if clearly better) against the criteria listed, then recommend one.

# Situation and data
[What is measured, how often, noise level, sample size, what must be predicted or explained.]

# Candidate models
M1. [equations]
M2. [equations]

# Criteria
[E.g. ability to reproduce [feature], identifiability of parameters from the available data, interpretability, computational cost.]

# Deliverable
- A comparison table: model × criterion, with a one-line justification per cell.
- Which parameters are identifiable from my data under each model.
- A recommended model, plus a concrete test (data pattern or experiment) that would falsify it.
```

## T6 Algorithm / numerical scheme

```markdown
# Task
Design an algorithm/numerical method to [compute/solve …] and argue that it is correct.

# Input / output
[Exact input format, sizes, accuracy required, time/memory budget.]

# Problem
[Precise mathematical problem the algorithm must solve.]

# Requirements
- Correctness argument (or convergence proof with rate) under stated assumptions.
- Complexity in terms of [n, d, ε].
- Failure modes: when it is unstable or slow (stiffness, ill-conditioning, nonconvexity).

# Deliverable
- Pseudocode and a reference implementation in [language].
- A test on [toy instance] whose answer is known: [expected answer].
```

## T7 Statistical analysis plan

Use where data answer the question (medicine, biology, psychology…). The core is to state the **estimand** and the **identification assumptions** first, then talk about methods.

```markdown
# Task
Propose a statistical analysis plan to estimate [effect] from the data described below, and state the assumptions under which the estimate has a causal / unbiased interpretation.

# Data
[Design (RCT / cohort / case–control / repeated measures), sample size, variables and their types, missingness, how treatment/exposure was assigned.]

# Estimand
[E.g. "average treatment effect on [outcome] at 30 days, in the population of [who]" — absolute or relative scale.]

# Assumed causal structure
[Known/suspected confounders, mediators, colliders; a DAG in text form if possible.]

# Deliverable
- Primary analysis method, with the exact model specification.
- Identification assumptions (e.g. no unmeasured confounding, positivity, consistency) and how to probe each.
- Sensitivity analyses (e.g. E-value, alternative adjustment sets).
- Sample size / power calculation with all inputs stated explicitly; flag inputs I must supply.
- What this design CANNOT answer.
```

## T8 Reviewer: check an existing derivation

Use for entry D. Best given to another model or a fresh conversation, so the same context isn't "grading itself".

```markdown
# Task
You are a careful referee. Below is a problem statement and a proposed solution produced by another AI. Your job is to find errors, not to be agreeable.

# Problem statement
[Paste the formal problem.]

# Proposed solution
[Paste the solution.]

# Instructions
1. Check that the solution answers the stated problem, not a nearby easier one (e.g. a special case, a weaker statement, different quantifiers).
2. Go step by step. For each step, mark it: correct / gap (needs justification) / error. Quote the step briefly.
3. List every assumption the solution used that is NOT in the problem statement.
4. Check cited theorems: does each exist, and are its hypotheses actually satisfied here?
5. Test the final result on [special case / numeric instance] and report the outcome.
6. Verdict: correct / correct with gaps / incorrect. If incorrect, the first fatal error.
```

## T9 Follow-up templates

- **Probe the weakest step**: "You said step [k] is the weakest. Expand it into a fully detailed argument, with every inequality justified. If you cannot, say exactly what is missing."
- **Relax an assumption**: "Which conclusions survive if assumption A[j] is dropped? Give a counterexample for each conclusion that fails."
- **Ask for verification code**: "Write a self-contained Python script that numerically checks [result] on [instance] and prints the discrepancy."
- **Another angle**: "Solve the same problem by a different method ([alternative]) and compare with the previous answer."
- **Explain in domain terms**: "Explain the result and its assumptions to a [field] researcher with undergraduate math, in under 200 words, including when it should NOT be used."
