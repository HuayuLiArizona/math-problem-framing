# Domain phrasing → mathematical structure → method keywords

How to use: find the row closest to the user's description in their domain to get the "mathematical structure" and English keywords; then use "common pitfalls" to decide what to probe in the interview. A problem often falls in several rows — list them all for the user and say which facet of the problem each one answers. The tables don't aim to be complete; the goal is the right search terms and the right follow-up questions.

## Contents

1. General patterns (cross-domain)
2. Machine learning / deep learning
3. Engineering (control, signals, structures, fluids/heat, reliability, operations research)
4. Medicine and public health
5. Biology (molecular, cellular, ecology, evolution, neuroscience)
6. Physics / chemistry / materials
7. Triage questions when "I don't know what to ask"

---

## 1. General patterns

| What the user might say | Mathematical structure | Methods / keywords | Common pitfalls |
|---|---|---|---|
| "Will it settle down / blow up?" | Fixed points and stability of a dynamical system | linearization, Jacobian eigenvalues, Lyapunov function, contraction mapping | Linear stability ≠ global stability; discrete time needs $\lvert\lambda\rvert<1$, continuous time $\mathrm{Re}\,\lambda<0$ |
| "Behaviour suddenly changes at some parameter value" | Bifurcation | bifurcation analysis (saddle-node, Hopf, pitchfork), continuation | Must say which parameter varies and where the others are fixed |
| "How does it change as things scale up?" | Asymptotic analysis / scaling law | asymptotic expansion, big-O, power-law fitting, dimensional analysis | Must say which quantities change together and at what rates |
| "Can this quantity be uniquely determined from the data?" | Identifiability / inverse problem | identifiability, inverse problem, Fisher information, ill-posedness, regularization | Structural identifiability ≠ practical identifiability (under noise) |
| "The best design / parameters" | Optimization | convex optimization, KKT conditions, Lagrange multipliers, integer programming, Bayesian optimization | "Best" needs an objective and constraints; non-convex problems only give local optima |
| "Two processes, one fast and one slow" | Multiple time scales | singular perturbation, quasi-steady-state approximation, averaging | The approximation needs a large enough time-scale ratio — state it |
| "Evolution with randomness" | Stochastic processes | Markov chain, SDE, master equation, Fokker–Planck | Where the noise enters (observation vs dynamics) changes everything |
| "How much data / how many samples do I need?" | Sample complexity / statistical power | concentration inequalities, power analysis, sample size calculation, Cramér–Rao bound | Needs effect size, variance, significance level and power |
| "Will errors accumulate?" | Error propagation / numerical stability | error propagation, condition number, numerical stability, Grönwall inequality | Distinguish truncation error from round-off error |
| "Effect of the network / connectivity structure" | Graph theory / spectral graph theory | graph Laplacian, spectral gap, centrality, percolation | Directed vs undirected, weighted vs unweighted |

## 2. Machine learning / deep learning

| What the user might say | Mathematical structure | Methods / keywords | Common pitfalls |
|---|---|---|---|
| "Loss suddenly explodes / oscillates / spikes" | Stability of optimization dynamics | edge of stability, sharpness (largest Hessian eigenvalue), $\eta\lambda_{\max}<2$, discrete-time dynamical system | GD results don't transfer directly to Adam; deterministic GD vs SGD |
| "How to tune batch size and learning rate together" | SDE approximation of SGD / noise scale | SDE approximation of SGD, gradient noise scale, linear scaling rule, square-root scaling for Adam | SDE approximations hold only for small learning rates; Adam's preconditioner changes noise scaling relative to SGD |
| "Why does it generalize / overfit?" | Statistical learning theory | generalization bound, Rademacher complexity, PAC-Bayes, uniform convergence, double descent, benign overfitting | Many bounds are vacuous for deep nets — ask whether qualitative understanding or a usable number is wanted |
| "How many samples to learn it?" | Sample complexity | VC dimension, minimax rate, sample complexity lower bound | Both upper and lower bounds; do constants matter? |
| "Can the model represent this function?" | Approximation theory | universal approximation, approximation rates, depth separation | Representable ≠ found by training |
| "Gradient estimator variance is too high" | Variance of estimators | REINFORCE / score function estimator, reparameterization trick, control variates, Rao–Blackwellization | State which distribution the expectation is over and whether parameters appear in it |
| "What is this loss actually optimizing?" | Divergences and variational inference | KL divergence, ELBO, f-divergence, proper scoring rule, mutual information bounds (InfoNCE) | Forward vs reverse KL |
| "What is attention / this layer doing?" | Linear algebra and kernel methods | low-rank structure, kernel interpretation, mean-field limit, NTK | Infinite-width results may not hold at finite width |
| "How do diffusion / flow models work?" | SDEs and PDEs | score matching, Fokker–Planck equation, probability flow ODE, optimal transport | Keep time direction and conventions of forward/reverse processes consistent |
| "Does RL converge?" | Fixed points and contractions | Bellman operator, contraction, stochastic approximation, policy gradient theorem | Contraction can fail with function approximation (deadly triad) |
| "The model is sensitive to input perturbations" | Lipschitz constants and robustness | Lipschitz bound, adversarial robustness, certified radius | Which norm |
| "There is a symmetry / invariance" | Group and representation theory | equivariance, invariance, group convolution, representation theory | Say what the group acts on |
| "GNN expressivity / oversmoothing" | Spectral graph theory, WL test | Weisfeiler–Lehman test, oversmoothing, graph Laplacian spectrum | Node feature initialization affects conclusions |
| "Fitting / extrapolating a scaling law" | Power-law regression and asymptotics | power-law fitting, compute-optimal allocation, Chinchilla-style analysis | Extrapolation beyond the fitted range needs stated assumptions |
| "Is this relationship causal?" | Causal inference | structural causal model, do-calculus, backdoor criterion, instrumental variables | Correlation ≠ causation; write down the assumed causal graph |

## 3. Engineering

| What the user might say | Mathematical structure | Methods / keywords | Common pitfalls |
|---|---|---|---|
| "Will the controller make the system oscillate / go unstable?" | Linear system stability | state-space model, transfer function, Routh–Hurwitz, Nyquist criterion, Lyapunov | Delays, saturation and nonlinearity break linear analysis |
| "How to design the optimal controller" | Optimal control | LQR, Riccati equation, MPC, Pontryagin maximum principle, $H_\infty$ | The choice of cost weights decides the result |
| "Can it be controlled / observed?" | Controllability and observability | controllability / observability Gramian, Kalman rank condition | Theoretically controllable ≠ practically controllable (conditioning) |
| "Estimate states / parameters from noisy measurements" | State estimation | Kalman filter, EKF/UKF, least squares, maximum likelihood, Cramér–Rao bound | Is the noise covariance known? Is the system nonlinear? |
| "Is the sampling rate enough / aliasing?" | Sampling theory | Nyquist–Shannon theorem, aliasing, anti-aliasing filter | Does the band-limited assumption hold? |
| "Filter design / frequency response" | Fourier and z-transforms | Fourier transform, z-transform, FIR/IIR design, Bode plot | Continuous vs discrete transform conventions |
| "Vibration frequencies, modes" | Eigenvalue problems | generalized eigenvalue problem $K\phi=\omega^2 M\phi$, modal analysis, Rayleigh quotient | Boundary conditions decide everything |
| "Infer damage or parameters from frequencies/responses" | Inverse eigenvalue problem / identifiability | inverse eigenvalue problem, model updating, sensitivity analysis, regularization | Symmetric structures make location non-unique; higher modes are noise-sensitive |
| "Structural loads / deformation" | Elasticity PDEs | Euler–Bernoulli / Timoshenko beam, FEM, variational formulation | Range of validity of small-deformation linearity |
| "Heat transfer / flow behaviour" | PDEs + dimensional analysis | heat equation, Navier–Stokes, Buckingham Π theorem, boundary layer, perturbation methods | Dimensionless numbers decide which terms can be neglected |
| "Can I trust the simulation results?" | Numerical analysis | convergence order, stability (CFL condition), stiffness, mesh refinement study | Converging ≠ converging to the correct physical solution |
| "How long until it fails / reliability" | Reliability theory | Weibull distribution, hazard function, Poisson process, Markov reliability model | Are failures independent? Is there ageing? |
| "Queues / throughput / latency" | Queueing theory | M/M/1, Little's law, Kingman formula, stochastic networks | Is the arrival process Poisson? |
| "Resource allocation / scheduling / siting" | Operations research | linear programming, MILP, network flow, scheduling, NP-hardness | At scale only approximations; state the acceptable optimality gap |
| "Communication rate limits" | Information theory | channel capacity, Shannon limit, rate–distortion | Channel model assumptions |

## 4. Medicine and public health

| What the user might say | Mathematical structure | Methods / keywords | Common pitfalls |
|---|---|---|---|
| "How does drug concentration change over time / dosing regimen" | Compartment ODE models | pharmacokinetics, compartment model, first-order elimination, steady state, accumulation factor, superposition | Linear PK assumption; first-order absorption?; how to model missed doses |
| "Dose–effect relationship" | Nonlinear regression | Emax model, Hill equation, dose–response curve, PK/PD link | Delayed effect (effect compartment) |
| "Disease spread / epidemic size" | Epidemic dynamics | SIR/SEIR, basic reproduction number $R_0$, next-generation matrix, branching process | Homogeneous mixing; early stochastic extinction |
| "Is treatment A better than B (RCT)?" | Hypothesis testing and effect estimation | two-sample test, effect size, confidence interval, non-inferiority, multiplicity correction | Pre-specify the primary endpoint; multiple comparisons |
| "How many patients do I need?" | Sample size and power | power analysis, sample size formula, effect size, dropout adjustment | Needs all four inputs: effect, variance, α, power |
| "In observational data, does the exposure cause the outcome?" | Causal inference | confounding, DAG, propensity score (matching / IPTW), regression adjustment, E-value, target trial emulation | Unmeasured confounding can't be removed statistically; write down the assumed confounding structure |
| "Survival time / follow-up data" | Survival analysis | Kaplan–Meier, Cox proportional hazards, censoring, competing risks | Is censoring independent? Proportional hazards? |
| "Probability that a positive tester is truly ill" | Bayes' theorem | sensitivity, specificity, PPV/NPV, prevalence, ROC/AUC | Prevalence drives PPV |
| "Repeated measurements on the same patient" | Longitudinal / hierarchical models | mixed-effects model, GEE, repeated measures | Observations aren't independent |
| "Image reconstruction / denoising" | Inverse problems | Radon transform, regularization, compressed sensing, total variation | Ill-posedness; choice of prior |
| "Tumour growth / treatment response" | Growth ODEs | Gompertz, logistic growth, tumor–immune models | Parameter identifiability is often poor |
| "Is the diagnostic model well calibrated?" | Probability calibration | calibration curve, Brier score, proper scoring rules | Good discrimination ≠ good calibration |

## 5. Biology

| What the user might say | Mathematical structure | Methods / keywords | Common pitfalls |
|---|---|---|---|
| "How will the population change / will it go extinct?" | Population dynamics | logistic equation, Lotka–Volterra, stability of equilibria, stochastic extinction | Deterministic models can't describe extinction of small populations |
| "Enzyme reaction rate / substrate concentration" | Chemical kinetics | Michaelis–Menten, quasi-steady-state approximation, mass-action kinetics | Validity conditions of QSSA (enzyme much scarcer than substrate, etc.) |
| "Cells have two states / switch-like behaviour / memory" | Multistability and bifurcation | bistability, nullcline analysis, saddle-node bifurcation, hysteresis, cooperativity (Hill coefficient) | Bistability usually needs nonlinearity (cooperativity); parameter ranges must be concrete |
| "Periodic oscillations (circadian clock, calcium)" | Limit cycles | Hopf bifurcation, limit cycle, delay differential equation | Delays are a common source of oscillation |
| "Cell-to-cell variability / noise in gene expression" | Stochastic biochemical kinetics | chemical master equation, Gillespie algorithm, Fano factor, transcriptional bursting | Intrinsic vs extrinsic noise |
| "Pattern formation (stripes, spots)" | Reaction–diffusion PDEs | Turing instability, reaction–diffusion, linear stability of homogeneous state | Needs sufficiently different diffusion coefficients |
| "Allele frequency change / drift / selection" | Population genetics | Wright–Fisher, Moran model, fixation probability, diffusion approximation, coalescent | Effective vs census population size |
| "Species relationships / phylogenetic trees" | Phylogenetics | substitution models (JC69, GTR), likelihood on trees, Bayesian phylogenetics | Model misspecification causes long-branch attraction |
| "Sequence alignment / finding functional regions" | Dynamic programming and HMMs | Needleman–Wunsch, Smith–Waterman, hidden Markov model, Viterbi | Choice of scoring matrix |
| "Neuron firing / excitability" | Nonlinear ODEs / phase plane | Hodgkin–Huxley, FitzHugh–Nagumo, phase-plane analysis, excitability | Range of validity of reduced models |
| "Testing thousands of genes at once" | High-dimensional statistics | false discovery rate (Benjamini–Hochberg), multiple testing, shrinkage, sparse regression | Correlation between tests |
| "Inferring regulatory networks from omics data" | Network inference / causal discovery | graphical models, partial correlation, causal discovery, identifiability | Far fewer samples than variables |
| "Spatial distribution of cells / molecules" | Spatial statistics and point processes | Ripley's K, point process, spatial autocorrelation | Edge effects |

## 6. Physics / chemistry / materials

| What the user might say | Mathematical structure | Methods / keywords | Common pitfalls |
|---|---|---|---|
| "Can I estimate the order of magnitude?" | Dimensional analysis and scaling | dimensional analysis, scaling arguments, Buckingham Π | Dimensional analysis can't give the form of the dimensionless function |
| "Correction under a small perturbation" | Perturbation theory | perturbation theory, asymptotic expansion, WKB | Naive expansions fail for singular perturbations |
| "The lowest-energy configuration" | Variational problems / optimization | variational principle, Euler–Lagrange equation, energy minimization | Local vs global minimum |
| "Diffusion / transport" | Parabolic PDEs / random walks | diffusion equation, Fick's law, random walk, Green's function | Anomalous diffusion (sub/super-diffusion) |
| "Phase transitions / critical behaviour" | Statistical mechanics | partition function, mean-field theory, critical exponents, Ising model | Mean-field fails in low dimensions |
| "Crystal symmetry / selection rules" | Group theory | point groups, space groups, character tables, representation theory | Approximate symmetry breaking |
| "Steady states of a reaction network" | Chemical reaction network theory | deficiency theory, mass-action, stoichiometric matrix | Eliminate conserved quantities first |

## 7. Triage questions when "I don't know what to ask"

When the user's description is unclear, these questions most quickly place the problem in one of the rows above:

1. Do you care about **how things change over time** (dynamics), **whether a state is good/stable** (stability), **inferring causes or parameters from data** (inverse problem / statistical inference), **finding the best choice** (optimization), or **under what conditions a claim holds** (proof/judgment)?
2. Does **randomness** matter? Is it measurement error, or does the system itself fluctuate?
3. Are the quantities **continuous** (concentration, voltage) or **discrete** (cell counts, event counts, nodes in a graph)?
4. Are there **two very different scales** (fast/slow, large/small, local/global)?
5. Do you already have **data**, **model equations**, or both? Is the math meant to explain a phenomenon, predict, or guide design?
