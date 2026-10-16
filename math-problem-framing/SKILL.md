---
name: math-problem-framing
description: Bilingual (中文/English) skill that helps non-mathematician researchers (ML researchers, engineers, clinicians, life scientists, etc.) turn a domain research problem into a precise math problem, find suitable methods, and build an LLM math prompt (structure distilled from OpenAI's math project) plus a verification plan. Run ONLY when explicitly invoked, i.e. the user types /math-problem-framing or asks by name to use the math-problem-framing skill. Do not start it on your own for ordinary math, modeling, statistics or prompt-writing requests; answer those normally. 仅在用户输入 /math-problem-framing 或点名要求使用 math-problem-framing 时运行；普通的数学、建模、统计或写 prompt 的请求不要自动启用，正常回答即可。
---

# Math Problem Framing for Non-Mathematician Researchers / 面向非数学专业研究者的数学问题定义

## Invocation and language

**Explicit invocation only.** This skill runs only when the user calls it (`/math-problem-framing`, or asks by name). Once invoked, keep applying it to follow-ups about the same problem in this conversation — answering your confirmation questions, revising the prompt, reviewing the answer an LLM gave them. A new, unrelated problem asked without invoking the skill gets a normal answer.

**Invoked with no problem description.** Ask the user to describe the problem, in their language, and say what helps: their field; the phenomenon or question; what is known (data, equations, constraints); what they want (derivation, proof, bound, model choice, analysis plan, a check of an LLM's derivation); and a small example with real numbers if they have one. If there is no language signal at all, write this short request in both Chinese and English.

**Language policy.**
- Reply in the user's language: Chinese (including Chinese mixed with English terms) → Chinese; English → English; any other language → that language.
- Read reference files from `references/zh/` for Chinese replies and from `references/en/` otherwise. The two folders hold the same content, so load only one.
- Write the prompt you build in the user's language by default. In a Chinese prompt, give key mathematical terms with their canonical English name in parentheses, e.g. 一致收敛 (uniform convergence): method and theorem names are how both people and LLMs index the literature. Formulas use LaTeX in either language.
- End with a one-line offer of the prompt in the other language ("需要英文版 prompt 吗？" / "Want a Chinese version of the prompt?"). If the user asks for both, give two separate code blocks with identical content rather than interleaving lines, which makes copying error-prone.
- Use the section labels from the delivery table at the end, in the user's language.

## What this skill solves

LLMs most often fail at math not for lack of reasoning power but because **the problem was never defined**: objects are undefined, quantifiers are missing, "optimal" has no criterion, hidden assumptions are unstated, the form of the answer is unclear. The model quietly fills the gap with an interpretation of its own and then cleanly solves a problem that *is not the user's problem* — and a non-mathematician has a hard time noticing.

When OpenAI posed research-level open problems to its internal model, the prompts followed a very consistent pattern: fully self-contained definitions, pinned-down quantifiers, acceptance criteria for both a proof and a refutation, an explicit statement of what is *not* being asked, pre-emptively blocked shortcuts, required self-checks (see `openai-math-prompt-patterns.md`). But those prompts were written by mathematicians who already knew the precise statement.

The users of this skill are stuck further upstream: **getting from a domain phenomenon to a mathematical statement**. So your main job is translate → back-translate for confirmation → locate methods; the prompt is just the final product. A formalization the user can confirm ("yes, that is my problem") is worth far more than an impressive prompt template.

## Workflow

```
Stage 0  Identify the entry point and the shape of the answer
Stage 1  Domain interview (only the questions that matter)
Stage 2  Formalization card + back-translation in domain language   ← most important
Stage 3  Locate the mathematical area and candidate methods
Stage 4  Assemble the prompt
Stage 5  Ambiguity audit
Stage 6  Verification plan
Deliver
```

Skip or compress stages depending on the entry point, but the Stage 2 back-translation and Stage 5 audit are almost always worth doing.

---

### Stage 0: Entry point and answer shape

**Entry point** (where the user is stuck):

| Entry | Typical phrasing | Focus |
|---|---|---|
| A. Doesn't know what kind of math this is | "What math should I use to analyze this?" | 1→2→3, often producing a **two-stage prompt** (method map first, then solve) |
| B. Roughly knows, can't state it precisely | "I know it's about stability but can't write it down" | 2, 5 |
| C. Already has a math statement or prompt | pastes a prompt | straight to 4→5→6: audit and rewrite |
| D. An LLM already gave a derivation; is it right? | pastes the LLM's answer | 6: reviewer prompt + checks the user can run |

**Answer shape** (what the user ultimately needs) — it decides which prompt sections are needed:

- **Derivation / closed form**: assumption register, regime of validity, final result stated separately
- **Prove or disprove a property** (convergence, stability, unbiasedness, identifiability, a bound holds): a prove-or-disprove clause and acceptance criteria for a counterexample
- **Bound / estimate / asymptotic order / scaling law**: which quantity tends to what, what constants may depend on
- **Model choice**: candidate models and comparison criteria
- **Algorithm / numerical scheme**: correctness criterion, complexity/accuracy requirements, input size
- **Optimization / design**: objective, constraints, decision variables
- **Statistical analysis plan**: data-generating process, estimand, identification assumptions

### Stage 1: Domain interview

The goal is the minimum information needed to formalize. A mathematician would need these six dimensions:

1. **Objects**: what quantities exist (variables, signals, populations, parameters)? Which are random, which fixed? Which observed, which unknown?
2. **Relations**: what links them (physical laws, mechanisms, empirical relations, data-generating process)?
3. **Goal**: what exactly is wanted — compute, prove, bound, optimize, choose among options? In what form?
4. **Scale and regime**: orders of magnitude? Which ratios are large or small? Time scales? How large is "N is large"?
5. **Assumptions**: what is the user willing to assume? What *cannot* be assumed (e.g. the data clearly are not normal)?
6. **Use and acceptance**: what decision depends on the result? What answer would convince them? How much precision is enough?

Interaction rules:

- **Ask at most 3–5 questions per round**, choosing only those that would *change the formalization*, ordered by impact. Ask in domain language, not math jargon, and offer options where possible ("Does the noise come mainly from the instrument, or does the system itself fluctuate randomly?").
- **Ask for a toy instance**: a minimal example with concrete numbers. It is the fastest way to make a vague description precise, and it doubles as a test case later.
- **Don't stall when the user can't say**: give 2–3 reasonable readings, adopt one as a labelled assumption, keep going, and list it under "please confirm" at delivery.
- **One-shot requests** (the user says "just give me the prompt", or back-and-forth isn't possible): don't stop to ask; run the whole workflow with explicit assumptions and put the assumptions that most affect the conclusion first.

### Stage 2: Formalization card + back-translation

Write the interview results as a compact **formalization card**:

- **Notation table**: symbol | mathematical type (scalar / $\mathbb R^d$ vector / matrix / function / random variable / distribution / graph …) | domain meaning | units or range
- **Known vs unknown**
- **Model / relations**: equations, constraints, data-generating process
- **Assumption list**: each tagged with its source — "confirmed by user" / "my inference" / "simplification for tractability"
- **Goal statement**: with full quantifiers (for all… / there exists… / as $N\to\infty$…), stating what is fixed, what varies, and what constants may depend on
- **Answer form**

Then **back-translate in domain language**: explain the formalization to the user and point out where it *made a choice on their behalf*. For example: "I read 'noise' as independent, identically distributed, zero-mean Gaussian noise. That means errors in successive measurements are uncorrelated — if your sensor drifts, the conclusion may change."

Back-translation is the single most important step: the user can't judge whether a mathematical statement is right, but can judge "yes, that's what I mean / no, it isn't".

Hidden assumptions worth probing (domain-independent):
independence / identical distribution; stationarity; linearity; Gaussianity; smoothness / differentiability; convexity; boundedness or finite variance; steady state / equilibrium; homogeneous population; deterministic vs stochastic; continuous vs discrete time; **order of limits** ($N\to\infty$ or $t\to\infty$ first?); in what sense "converges"; "optimal" with respect to which objective and under which constraints; which kind of "stable" (Lyapunov, BIBO, numerical stability, statistical robustness).

### Stage 3: Locate the mathematical area and candidate methods

- Use `domain-math-map.md` to map domain phrasing to mathematical structures and candidate methods (sections for ML / engineering / medicine & public health / biology / physics-chemistry-materials; read only what's relevant).
- **When the choice is genuinely open, offer 2–3 candidate formulations** and explain the trade-offs in domain language (tractability vs realism), e.g. "A deterministic ODE model gives a closed-form steady state but ignores patient-to-patient variability; a mixed-effects model is more realistic but only solvable numerically."
- **Give canonical English terms as search keywords** (with the user's-language name alongside, for Chinese users). Replacing the user's description with the right technical terms often solves half the problem.
- **When the method is uncertain, use a two-stage prompt**: stage A asks the LLM only for a method map (classify the problem, list candidate frameworks, their assumptions and what each can conclude — no solving); after the user picks, stage B solves the chosen formulation. Templates in `prompt-templates.md`.
- **Set expectations honestly**: if the problem looks like a known open problem or genuinely research-level, say so. In OpenAI's project the model spent about 3 hours of reasoning per problem on average, roughly 4,000 problems yielded a few hundred publishable results, and some papers were still withdrawn over a sign error. For research-level problems, special cases, partial results or heuristic derivations are usually the realistic target.

### Stage 4: Assemble the prompt

The skeleton below follows the structure of the OpenAI math prompts, adapted for practitioners. Use only the heading in the prompt's language (e.g. `# Task` or `# 任务`). **Every section must carry information; drop sections that would be empty** — about a page is usually enough for a moderately hard derivation; only research-level problems need more.

```markdown
# Task | 任务
[One sentence on what is wanted + the success criterion.]
[Proof/judgment problems: a proof OR a rigorous refutation with a counterexample both count.]
[If the problem itself is flawed (ill-posed, contradictory assumptions, goal false under the stated assumptions), say so explicitly and propose the minimal fix — do not silently change the problem.]
[Before solving, restate the problem in your own words and list any ambiguities; if an ambiguity changes the answer, say which interpretation you adopt or treat each case.]

# Context (optional, ≤5 lines) | 背景（可选，≤5 行）
[Domain background — only to help the model judge which assumptions are physically/biologically/practically reasonable. State that the formal statement below is authoritative.]

# Setup and notation | 记号与定义
[Define every object from scratch: type, range, units; distribution and independence structure of random objects; when citing literature, copy the needed definitions in full.]

# Assumptions | 假设
A1. …  A2. …
[Mark which are essential, which are simplifications for tractability, which should be relaxed if possible.]

# Problem | 问题
[Precise statement with quantifiers and dependencies: what is fixed, what varies, what constants may depend on; order of limits where relevant.]
[Equivalent formulations, if any.]

# Scope (what is NOT being asked) | 范围（不在问什么）
[Name nearby-but-different questions, especially the easier version the model might substitute; say what is not needed.]

# Deliverable | 交付要求
[Answer form; step-by-step derivation; final result stated separately; mark where each assumption is used.]
[If a conclusion holds only in a special case (linear, Gaussian, small parameter, a given dimension…), state the restriction explicitly; do not present a special case as the general result.]

# Directions and milestones (optional) | 方向与阶段目标（可选）
[Possibly useful methods or references, noting what each provides and what it does not.]
[Acceptable partial results, e.g. d=1 / linearized / Gaussian case first — but a partial result is not a full answer.]

# Self-checks | 自检
[Reduce to known special cases; units/dimensions consistent; sensible limiting behaviour; verify on a small numerical example (Python/sympy code welcome); name the weakest step and your confidence in it.]
```

Writing guidance:

- **Formulas in LaTeX** (`$...$`), which every major LLM reads; **one symbol per concept**, used consistently.
- **Replace vague words with precise ones**: "large/small" → an order of magnitude or an asymptotic relation; "converges" → in what sense; "optimal" → objective and constraints; "stable" → which stability; "significant" → which test and level.
- **Don't presuppose or hint at the conclusion you expect**; the model will play along. If you have a conjecture, write it as "prove or disprove".
- **Keep context and formalization separate**: context aids understanding, but the formal statement must stand on its own.
- **Don't prescribe a method when the method is uncertain**; when it is settled, you can write "prefer X; if X is not workable, explain why before switching".
- **Welcome numerical checks**: unlike OpenAI's discouragement of computer-assisted proofs, practitioners usually very much want runnable verification code.
- **For reasoning models** (o-series, GPT thinking modes, Claude extended thinking, Gemini Deep Think, etc.), put the effort into the problem definition; incantations like "let's think step by step" are unnecessary.

Ready-made templates by answer shape (method map, derivation, prove/disprove, bound, model choice, algorithm, statistical analysis plan, reviewer, follow-ups) are in `prompt-templates.md`.

### Stage 5: Ambiguity audit

Go through this before delivering; fix what you can, and list what you can't under "please confirm":

- [ ] Every symbol defined, with type and range?
- [ ] Quantifiers complete? ("for all" or "there exists"? what do constants depend on?)
- [ ] Randomness: what is random, with what distribution, what independence? What are expectations/probabilities taken over?
- [ ] Limits: which quantity tends to what? Are the others fixed? Order of limits?
- [ ] Vague words replaced? (large, small, approximately, optimal, converge, stable, significant, robust, effective)
- [ ] Boundary and degenerate cases: division by zero, empty sets, initial values, boundary conditions, parameters at endpoints?
- [ ] Answer form explicit?
- [ ] Proof/judgment problems: both outcomes allowed, with acceptance criteria?
- [ ] "Not being asked" stated — especially where the model could swap in an easier problem?
- [ ] Known shortcuts blocked? (proving only the linear case, holding only under Gaussianity, numbers without derivation, citing nonexistent theorems)
- [ ] Units and dimensions consistent?
- [ ] **Self-containment test**: could a mathematician who knows nothing about this field read only the prompt and know exactly what to solve?

### Stage 6: Verification plan

Users usually can't check a proof line by line, so give them checks **they can run themselves** (details in `verification-toolkit.md`):

- Special-case reduction: does the result reduce to the known answer in a known simple case?
- Limits and units: sensible behaviour as parameters go to 0 or ∞? Units correct?
- Numerical comparison: simulation / Monte Carlo / small-scale direct computation against the formula (have the model include code, the user runs it)
- Symbolic computation: verify key algebra with sympy / Mathematica
- Adversarial review: give the problem and the answer to another model or a fresh chat with a reviewer prompt whose job is to find errors
- Probe the weakest step: expand the step the model is least sure about

Pick the 2–4 most effective for this problem, write them as concrete steps ("with $k=1, m=1$ the formula must give $\omega=1$"), and include a ready-to-use reviewer prompt.

---

## Delivery

Deliver in this order, concisely, so the user can act right after reading. Use the label in the user's language:

| # | 中文 | English | Content |
|---|---|---|---|
| 1 | 一句话定位 | In one sentence | What the problem is mathematically, in domain language + the math name (e.g. "This is essentially an identifiability question within an inverse eigenvalue problem") |
| 2 | 形式化卡片 | Formalization card | The compact card |
| 3 | 需要你确认的点 | Please confirm | At most 5, ordered by impact on the conclusion; each says how things change if it's otherwise |
| 4 | 最终 prompt | The prompt | In a code block for copying; for two-stage, two prompts and the order to use them |
| 5 | 验证方案 | How to verify | Concrete checks + reviewer prompt |
| 6 | 延伸阅读关键词 | Search keywords | 3–8 canonical English terms |

If the prompt is long or the user will iterate on it, offer to save it as a file. Close with the one-line offer of the other-language version. If you are confident you can solve the problem yourself, you may offer to attempt it after delivering — the formalization card is also the statement you should work from.

## Reference files

Load from `references/zh/` (Chinese) or `references/en/` (otherwise); file names are the same in both:

- `openai-math-prompt-patterns.md` — the 12 patterns from OpenAI's math project prompts, with short excerpts and practitioner versions. Read when you need to explain why a writing choice matters, or to model a research-level prompt.
- `domain-math-map.md` — domain phrasing → mathematical structure → method keywords → common pitfalls. Read the relevant section in Stage 3.
- `prompt-templates.md` — prompt templates by answer shape, plus follow-up templates. Read the matching template in Stage 4.
- `verification-toolkit.md` — verification methods non-mathematicians can run, plus the reviewer prompt. Read in Stage 6.
- `worked-examples.md` — three complete examples from vague description to final prompt (pharmacokinetics, gene circuit, ML estimator variance). Read on first use or when unsure how detailed the output should be.
