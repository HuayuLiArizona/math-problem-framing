# Prompt patterns from OpenAI's math project

Source: https://github.com/openai/math (Apache-2.0). The repository collects mathematical manuscripts produced by an internal OpenAI model on open research problems (372 result families, 719 manuscripts, about 42% of top-line results formalized in Lean). Ten reasoning summaries in `reasoning_traces/` include an **Original prompt (excerpts)** section — the most direct evidence of how problems were posed to the model. The patterns below are distilled from those prompts; original text is quoted only in short excerpts.

## Contents

1. State the deliverable and the success criterion up front
2. Fully self-contained definitions
3. Pin down quantifiers and "what is fixed, what varies"
4. Remove boundary ambiguities one by one
5. Acceptance criteria for both outcomes
6. Name what is NOT being asked
7. Block known shortcuts in advance
8. Research directions + milestones
9. Require self-checks: where each assumption is used, recovery of known cases
10. Annotated references
11. Say where the improvement lies (the frontier)
12. Give equivalent formulations and permitted reductions
Appendix: meta-lessons from the repository

---

## 1. State the deliverable and the success criterion up front

> "Please give a complete rigorous solution to the following problem. Even if the problem is "open", the intention is that you should resolve it…" (Mézard–Parisi)
>
> It then says a complete rigorous resolution or a "well-presented, rigorous refutation" are both acceptable, and that brute-force enumerations and computer-assisted proofs are discouraged in the final writeup.

**Why it matters**: the model knows what "done" means, and that disproving the claim also counts as success — removing pressure to force a proof.

**Practitioner version**: say in the first paragraph whether you want a derivation, proof, bound, algorithm or plan; accept "the conclusion fails under these assumptions" as a legitimate answer; accept "the problem is ill-posed" as a legitimate answer and ask for the minimal fix. Practitioners usually *welcome* numerical verification — the opposite of OpenAI's stance.

## 2. Fully self-contained definitions

The Kaplansky problem defines the group algebra $K[G]$ from scratch ("finite formal sums, convolution multiplication, identity element"); the Mézard–Parisi problem cites equation numbers from a paper but still writes "Here is a complete definition of its finite-level trial functional" and restates the whole construction.

**Why it matters**: the model's "default reading" of a notation may differ from yours, and conventions in different papers can conflict (normalization, signs, whether a constant term is included).

**Practitioner version**: the things "everyone in the field knows" are exactly what most need defining. Is "signal-to-noise ratio" a power ratio or an amplitude ratio? Which compartment does "half-life" refer to? Does "learning rate" include the warmup schedule? When citing a paper, copy the definitions you need.

## 3. Pin down quantifiers and "what is fixed, what varies"

> "The alphabet, all predicate truth tables, and k=max_f k_f are fixed independently of the input size" (Max-CSP)
>
> "there is a finite beta_0(d,S)>0 such that for every finite beta>=beta_0 …" (Heisenberg) — what a constant may depend on is written into its notation.
>
> "All model parameters and disorder laws are fixed as N→∞" (Mézard–Parisi)

**Why it matters**: the order of "for all / there exists", what constants depend on, and which quantities scale with size decide whether a statement is true. One of the most common silent substitutions by models is letting a constant depend on something it shouldn't.

**Practitioner version**: "when the sample is large" must become "fix the dimension $d$ and let $n\to\infty$" or "$d/n\to\gamma\in(0,1)$"; "for a small enough learning rate" must become "there exists $\eta_0>0$ (depending on $L$ and $\mu$, not on $T$) such that for all $\eta<\eta_0$…".

## 4. Remove boundary ambiguities one by one

Examples: zero-value instances are excluded from a ratio rather than given a 0/0 convention; the constant is set to $\alpha_\Gamma=1$ when a set is empty; indices are sampled "with replacement"; variables may repeat within a tuple; each unordered edge is counted once; $D$ is the nonnegative square root; $\mathbb N=\{1,2,\dots\}$.

**Why it matters**: these small points are the main source of "the solution looks right but solves a different problem".

**Practitioner version**: degenerate cases (zero dose, zero noise, a single sample), initial conditions, boundary conditions, handling of missing data, what to do when a ratio's denominator is zero, discretization step conventions.

## 5. Acceptance criteria for both outcomes

> "A disproof must provide a set with divergent reciprocal sum and some fixed length k>=3 for which no such progression exists." (arithmetic progressions)
>
> A counterexample must specify a prime, a field, a well-defined group and two finite sums, and certify $ab=1$; "A finitely presented group together with unproved word equalities or inequalities is not a certificate." (Kaplansky)

**Why it matters**: when the model knows what counts as a counterexample, it can't get away with something that merely looks like one — and it won't force a proof when the statement is actually false.

**Practitioner version**: "If you believe the method does not converge in this setting, give specific parameter values and an initial condition, and explain how to reproduce the divergence numerically." "If identification is not unique, construct two different parameter sets that produce exactly the same observations."

## 6. Name what is NOT being asked

> "The target is direct finiteness of group algebras, not the assertion that every group has no zero divisors…" (Kaplansky)
>
> "No attainment at a fixed level or by a particular trial law is asserted." (Mézard–Parisi)
>
> "It imposes no magnetization requirement on the rotation-invariant mixture…" (Heisenberg)

**Why it matters**: models drift easily toward a neighbouring problem that is easier or more famous. Ruling it out up front is much cheaper than correcting afterwards.

**Practitioner version**: "I am not asking what the optimal learning rate is; I am asking why linear scaling becomes unstable." "No survey of all causal inference methods is needed — only a plan for this confounding structure." "No numerical PDE solution is needed; I need qualitative conclusions about parameter dependence."

## 7. Block known shortcuts in advance

> Any proof valid only for sofic, residually finite, amenable or surjunctive groups "must identify its restriction instead of silently extending it to arbitrary groups." (Kaplansky)
>
> A theorem about complex operator algebras cannot be transferred to characteristic $p$ without argument; the cited paper's historical comments about nonsofic groups "must not be assumed current."

**Why it matters**: the problem setter knows what the most common wrong argument for this problem looks like, and writes it into the prompt.

**Practitioner version**: write your field's typical mistakes into the prompt — "do not assume samples are independent; the data contain repeated measurements", "do not extrapolate small-signal linearization to large perturbations unless you prove the range where linearization holds", "if it holds only under Gaussian noise, say so explicitly".

## 8. Research directions + milestones

> "Existing proofs for characteristic zero and for surjunctive groups are useful starting points."
>
> "A useful first milestone is a new class of groups not covered by the known sufficient hypotheses… These milestones do not replace the full quantifiers of the target." (Kaplansky)

**Why it matters**: starting points save a lot of exploration; saying "a milestone is not completion" stops the model from packaging a partial result as a full answer.

**Practitioner version**: "You may start with the one-dimensional / linear / two-compartment case and then generalize; if only the special case can be done, state which steps fail to generalize and why."

## 9. Require self-checks

> "Check explicitly where finite support, the field characteristic, torsion, and any approximation assumption enter. Verify the argument recovers the elementary finite-group case…" (Kaplansky)

The reasoning summaries show the model doing similar checks on its own: a normalization check with constant interactions, repeated audits of identities, attention to the order of limits.

**Practitioner version**: ask the model to mark where each assumption is used (so you can see what relaxing it would affect); require the result to reduce to the known answer in known special cases; dimensional checks; a small numerical example.

## 10. Annotated references

The Kaplansky problem lists two starting references, each annotated: one "Provides a known sufficient surjunctivity hypothesis, not an all-groups solution"; the other gives precise definitions and a proof under surjunctivity, but its historical comments are not to be taken as current.

**Why it matters**: prevents the model from reading "related literature" as "already solved", or citing outdated conclusions.

**Practitioner version**: give the key papers or textbook chapters you know, and say what you take from each ("use its model setup, not its approximation"). Also ask the model to give sources for theorems it cites so you can check them — LLMs sometimes fabricate citations.

## 11. Say where the improvement lies

> "The intended improvement is to obtain matching NP-hardness without the Unique Games Conjecture." (Max-CSP)
>
> "This is the sharpness conjecture immediately following Theorem 4" (Mézard–Parisi)

**Why it matters**: when the model knows where known results stop and where the novelty lies, it won't restate a known result as the answer.

**Practitioner version**: "I already know the standard result that SGD converges for small learning rates; what I want to know is what happens when batch size and learning rate change together…" "The formula in the literature assumes a uniform cross-section; my beam is tapered."

## 12. Give equivalent formulations and permitted reductions

> "equivalently the weak operator closure…"; "equivalently their Gram matrix is positive semidefinite"
>
> "It is enough to establish the assertion for every finitely generated G and then explain the reduction" (Kaplansky)

**Why it matters**: equivalent formulations help the model match a form it knows; explicitly permitted reductions let it simplify with confidence.

**Practitioner version**: "You may nondimensionalize first", "you may choose time units so that $k_{el}=1$", "either continuous- or discrete-time is fine, but state the correspondence between them".

---

## Appendix: meta-lessons from the repository

- **Scale and filtering**: about 4,000 problems, about 3 hours of reasoning per problem on average; after aggregation and a significance bar, 719 manuscripts. On hard problems most attempts do not succeed directly — for practitioners: expect several rounds on hard problems, and go for special cases and partial results first.
- **Verification is not optional**: the repository states that unformalized results may contain issues; one paper was withdrawn because a sign error invalidated its argument, taking two dependent papers with it. For practitioners: every LLM derivation needs a check you can run yourself.
- **What the reasoning looks like**: the summaries show the model first looking for counterexamples, using simple special cases as normalization checks, repeatedly auditing key steps, and minding the order of limits. Asking for these explicitly in the prompt writes good habits into the task.
- **The problem statement is the formalization target**: results with Lean formalizations come with scope notes stating exactly what the formalization covers and what it does not (e.g. "no explicit value of the constant is given"). For practitioners: when delivering an answer, also state the range in which it holds.
