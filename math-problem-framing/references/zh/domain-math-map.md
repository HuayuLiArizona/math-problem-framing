# 领域说法 → 数学结构 → 方法关键词

用法：先按领域找到最接近用户描述的行，拿到"数学结构"和英文关键词；再看"常见陷阱"决定访谈时要追问什么。一个问题常常同时落在几行里——把它们都列给用户，说明各自回答的是问题的哪一面。表格不求全，目的是给出正确的检索词和追问方向。

## 目录

1. 通用模式（跨领域）
2. 机器学习 / 深度学习
3. 工程（控制、信号、结构、流体热、可靠性、运筹）
4. 医学与公共卫生
5. 生物（分子、细胞、生态、进化、神经）
6. 物理 / 化学 / 材料
7. "我不知道该问什么"时的分类问题

---

## 1. 通用模式

| 你可能这样描述 | 数学结构 | 方法 / 关键词 | 常见陷阱 |
|---|---|---|---|
| "会不会稳定下来 / 会不会发散" | 动力系统的不动点与稳定性 | linearization, Jacobian eigenvalues, Lyapunov function, contraction mapping | 线性稳定 ≠ 全局稳定；离散时间判据是 $\lvert\lambda\rvert<1$，连续时间是 $\mathrm{Re}\,\lambda<0$ |
| "参数变到某个值突然变了" | 分岔 | bifurcation analysis (saddle-node, Hopf, pitchfork), continuation | 需要说清哪个参数在变、其他参数固定在哪 |
| "随着规模增长怎么变" | 渐近分析 / scaling law | asymptotic expansion, big-O, power law fitting, dimensional analysis | 必须说明哪些量同时变化、比例如何 |
| "这个量能不能从数据里唯一确定" | 可辨识性 / 逆问题 | identifiability, inverse problem, Fisher information, ill-posedness, regularization | 结构可辨识 ≠ 实际可辨识（噪声下） |
| "最好的方案 / 参数" | 优化 | convex optimization, KKT conditions, Lagrange multipliers, integer programming, Bayesian optimization | "最好"必须有目标函数和约束；非凸时只有局部最优 |
| "两个快慢过程" | 多时间尺度 | singular perturbation, quasi-steady-state approximation, averaging | 近似成立需要时间尺度比足够大，要写明比值 |
| "带随机性的演化" | 随机过程 | Markov chain, SDE, master equation, Fokker–Planck | 噪声加在哪里（观测 vs 动力学）结论完全不同 |
| "需要多少数据 / 样本" | 样本复杂度 / 统计功效 | concentration inequalities, power analysis, sample size calculation, Cramér–Rao bound | 需要效应量、方差、显著性水平、功效四个输入 |
| "误差会不会累积" | 误差传播 / 数值稳定性 | error propagation, condition number, numerical stability, Grönwall inequality | 区分截断误差与舍入误差 |
| "网络 / 连接结构的影响" | 图论 / 谱图论 | graph Laplacian, spectral gap, centrality, percolation | 有向 / 无向、加权 / 不加权要说清 |

## 2. 机器学习 / 深度学习

| 你可能这样描述 | 数学结构 | 方法 / 关键词 | 常见陷阱 |
|---|---|---|---|
| "loss 突然爆掉 / 震荡 / spike" | 优化动力学的稳定性 | edge of stability, sharpness (largest Hessian eigenvalue), $\eta\lambda_{\max}<2$, discrete-time dynamical system | GD 的结论不能直接搬到 Adam；要说明是确定性 GD 还是 SGD |
| "batch size 和学习率怎么一起调" | SGD 的随机微分方程近似 / 噪声尺度 | SDE approximation of SGD, gradient noise scale, linear scaling rule, square-root scaling for Adam | SDE 近似只在小学习率下成立；Adam 的预条件使噪声缩放不同于 SGD |
| "为什么泛化好 / 过拟合" | 统计学习理论 | generalization bound, Rademacher complexity, PAC-Bayes, uniform convergence, double descent, benign overfitting | 很多界在深网上是空洞的（vacuous），要问清是想要定性理解还是可用的数值界 |
| "需要多少样本才能学会" | 样本复杂度 | VC dimension, minimax rate, sample complexity lower bound | 上界与下界都要；常数是否重要 |
| "模型能不能表示某个函数" | 逼近论 | universal approximation, approximation rates, depth separation | 能表示 ≠ 能被训练找到 |
| "梯度估计方差太大" | 估计量方差分析 | REINFORCE / score function estimator, reparameterization trick, control variates, Rao–Blackwellization | 要写清期望对什么分布取、参数是否出现在分布里 |
| "这个 loss / 目标到底在优化什么" | 散度与变分推断 | KL divergence, ELBO, f-divergence, proper scoring rule, mutual information bounds (InfoNCE) | 写清是前向 KL 还是反向 KL |
| "attention / 某层在做什么" | 线性代数与核方法 | low-rank structure, kernel interpretation, mean-field limit, NTK | 无穷宽极限的结论不一定适用于有限宽 |
| "扩散模型 / 流模型的原理" | SDE 与 PDE | score matching, Fokker–Planck equation, probability flow ODE, optimal transport | 前向 / 反向过程的时间方向与约定要统一 |
| "强化学习收敛性" | 不动点与压缩映射 | Bellman operator, contraction, stochastic approximation, policy gradient theorem | 函数逼近下压缩性可能失效（deadly triad） |
| "模型对输入扰动敏感" | Lipschitz 常数与鲁棒性 | Lipschitz bound, adversarial robustness, certified radius | 用哪个范数 |
| "有对称性 / 不变性" | 群论与表示论 | equivariance, invariance, group convolution, representation theory | 要写清群作用在什么上 |
| "GNN 的表达能力 / 过平滑" | 谱图论、WL 测试 | Weisfeiler–Lehman test, oversmoothing, graph Laplacian spectrum | 节点特征的初始化影响结论 |
| "scaling law 拟合 / 外推" | 幂律回归与渐近 | power-law fitting, compute-optimal allocation, Chinchilla-style analysis | 外推需要说明拟合区间外的假设 |
| "两个变量之间是不是因果" | 因果推断 | structural causal model, do-calculus, backdoor criterion, instrumental variables | 相关 ≠ 因果；需要写出假定的因果图 |

## 3. 工程

| 你可能这样描述 | 数学结构 | 方法 / 关键词 | 常见陷阱 |
|---|---|---|---|
| "控制器会不会让系统振荡 / 失稳" | 线性系统稳定性 | state-space model, transfer function, Routh–Hurwitz, Nyquist criterion, Lyapunov | 时延、饱和、非线性会破坏线性分析 |
| "怎么设计控制器最优" | 最优控制 | LQR, Riccati equation, MPC, Pontryagin maximum principle, $H_\infty$ | 代价函数权重的选择决定结果 |
| "能不能控制 / 能不能观测到" | 可控性与可观性 | controllability / observability Gramian, Kalman rank condition | 理论可控 ≠ 实际可控（条件数） |
| "从带噪测量估计状态 / 参数" | 状态估计 | Kalman filter, EKF/UKF, least squares, maximum likelihood, Cramér–Rao bound | 噪声协方差是否已知；是否非线性 |
| "采样率够不够 / 混叠" | 采样理论 | Nyquist–Shannon theorem, aliasing, anti-aliasing filter | 带限假设是否成立 |
| "滤波器设计 / 频率响应" | 傅里叶与 z 变换 | Fourier transform, z-transform, FIR/IIR design, Bode plot | 连续 / 离散变换约定 |
| "振动频率、模态" | 特征值问题 | generalized eigenvalue problem $K\phi=\omega^2 M\phi$, modal analysis, Rayleigh quotient | 边界条件决定一切 |
| "从频率/响应反推损伤或参数" | 逆特征值问题 / 可辨识性 | inverse eigenvalue problem, model updating, sensitivity analysis, regularization | 对称结构导致位置不唯一；高阶模态对噪声敏感 |
| "结构受力 / 变形" | 弹性力学 PDE | Euler–Bernoulli / Timoshenko beam, FEM, variational formulation | 小变形线性假设的适用范围 |
| "传热 / 流动规律" | 偏微分方程 + 量纲分析 | heat equation, Navier–Stokes, Buckingham Π theorem, boundary layer, perturbation methods | 无量纲数决定哪些项可忽略 |
| "数值仿真结果可信吗" | 数值分析 | convergence order, stability (CFL condition), stiffness, mesh refinement study | 收敛 ≠ 收敛到正确的物理解 |
| "设备多久会坏 / 可靠性" | 可靠性理论 | Weibull distribution, hazard function, Poisson process, Markov reliability model | 失效是否独立；是否有老化 |
| "排队 / 吞吐 / 延迟" | 排队论 | M/M/1, Little's law, Kingman formula, stochastic networks | 到达过程是否泊松 |
| "资源分配 / 调度 / 选址" | 运筹优化 | linear programming, MILP, network flow, scheduling, NP-hardness | 规模大时只能近似；要说明能接受的最优性间隙 |
| "通信速率极限" | 信息论 | channel capacity, Shannon limit, rate–distortion | 信道模型假设 |

## 4. 医学与公共卫生

| 你可能这样描述 | 数学结构 | 方法 / 关键词 | 常见陷阱 |
|---|---|---|---|
| "药物浓度怎么随时间变 / 给药方案" | 房室模型 ODE | pharmacokinetics, compartment model, first-order elimination, steady state, accumulation factor, superposition | 线性 PK 假设；吸收是否一级；漏服如何建模 |
| "剂量和效果的关系" | 非线性回归 | Emax model, Hill equation, dose–response curve, PK/PD link | 效应延迟（effect compartment） |
| "疾病传播 / 流行规模" | 传染病动力学 | SIR/SEIR, basic reproduction number $R_0$, next-generation matrix, branching process | 均匀混合假设；早期随机灭绝 |
| "疗法 A 是否比 B 好（RCT）" | 假设检验与效应估计 | two-sample test, effect size, confidence interval, non-inferiority, multiplicity correction | 主要终点要预先定义；多重比较 |
| "需要多少病人" | 样本量与功效 | power analysis, sample size formula, effect size, dropout adjustment | 四个输入（效应、方差、α、功效）缺一不可 |
| "观察性数据里，暴露是否导致结局" | 因果推断 | confounding, DAG, propensity score (matching / IPTW), regression adjustment, E-value, target trial emulation | 未测混杂无法被统计方法消除；要写出假定的混杂结构 |
| "生存时间 / 随访数据" | 生存分析 | Kaplan–Meier, Cox proportional hazards, censoring, competing risks | 删失是否独立；比例风险假设 |
| "检测阳性的人真患病的概率" | 贝叶斯定理 | sensitivity, specificity, PPV/NPV, prevalence, ROC/AUC | 患病率决定 PPV |
| "多次测量的同一病人" | 纵向 / 层次模型 | mixed-effects model, GEE, repeated measures | 观测不独立 |
| "影像重建 / 去噪" | 逆问题 | Radon transform, regularization, compressed sensing, total variation | 不适定性；先验的选择 |
| "肿瘤生长 / 治疗反应" | 增长模型 ODE | Gompertz, logistic growth, tumor–immune models | 参数可辨识性常常很差 |
| "诊断模型是否校准良好" | 概率校准 | calibration curve, Brier score, proper scoring rules | 区分度好 ≠ 校准好 |

## 5. 生物

| 你可能这样描述 | 数学结构 | 方法 / 关键词 | 常见陷阱 |
|---|---|---|---|
| "种群数量怎么变 / 会不会灭绝" | 种群动力学 | logistic equation, Lotka–Volterra, stability of equilibria, stochastic extinction | 确定性模型无法描述小种群灭绝 |
| "酶反应速率 / 底物浓度" | 化学动力学 | Michaelis–Menten, quasi-steady-state approximation, mass-action kinetics | QSSA 的有效条件（酶远少于底物等） |
| "细胞有两种状态 / 开关行为 / 记忆" | 多稳态与分岔 | bistability, nullcline analysis, saddle-node bifurcation, hysteresis, cooperativity (Hill coefficient) | 双稳态通常需要非线性（协同性）；参数范围要具体 |
| "周期振荡（生物钟、钙振荡）" | 极限环 | Hopf bifurcation, limit cycle, delay differential equation | 时延是振荡的常见来源 |
| "基因表达的细胞间差异 / 噪声" | 随机生化动力学 | chemical master equation, Gillespie algorithm, Fano factor, transcriptional bursting | 内在噪声 vs 外在噪声 |
| "图案形成（条纹、斑点）" | 反应扩散 PDE | Turing instability, reaction–diffusion, linear stability of homogeneous state | 需要扩散系数差异足够大 |
| "等位基因频率变化 / 漂变 / 选择" | 群体遗传学 | Wright–Fisher, Moran model, fixation probability, diffusion approximation, coalescent | 有效种群大小 vs 实际大小 |
| "物种关系 / 进化树" | 系统发生学 | substitution models (JC69, GTR), likelihood on trees, Bayesian phylogenetics | 模型错配导致长枝吸引 |
| "序列比对 / 识别功能区" | 动态规划与 HMM | Needleman–Wunsch, Smith–Waterman, hidden Markov model, Viterbi | 打分矩阵的选择 |
| "神经元放电 / 兴奋性" | 非线性 ODE / 相平面 | Hodgkin–Huxley, FitzHugh–Nagumo, phase-plane analysis, excitability | 降维模型的适用范围 |
| "成千上万个基因同时检验" | 高维统计 | false discovery rate (Benjamini–Hochberg), multiple testing, shrinkage, sparse regression | 检验之间的相关性 |
| "从组学数据推断调控网络" | 网络推断 / 因果发现 | graphical models, partial correlation, causal discovery, identifiability | 样本量远小于变量数 |
| "细胞 / 分子的空间分布" | 空间统计与点过程 | Ripley's K, point process, spatial autocorrelation | 边界效应 |

## 6. 物理 / 化学 / 材料

| 你可能这样描述 | 数学结构 | 方法 / 关键词 | 常见陷阱 |
|---|---|---|---|
| "能不能估个数量级" | 量纲分析与标度 | dimensional analysis, scaling arguments, Buckingham Π | 无量纲函数的形式无法由量纲分析得到 |
| "小扰动下的修正" | 摄动理论 | perturbation theory, asymptotic expansion, WKB | 奇异摄动时朴素展开失效 |
| "能量最低的构型" | 变分问题 / 优化 | variational principle, Euler–Lagrange equation, energy minimization | 局部极小 vs 全局极小 |
| "扩散 / 输运" | 抛物型 PDE / 随机游走 | diffusion equation, Fick's law, random walk, Green's function | 反常扩散（亚扩散 / 超扩散） |
| "相变 / 临界行为" | 统计力学 | partition function, mean-field theory, critical exponents, Ising model | 平均场在低维失效 |
| "晶体对称性 / 选择定则" | 群论 | point groups, space groups, character tables, representation theory | 对称性的近似破缺 |
| "反应网络稳态" | 化学反应网络理论 | deficiency theory, mass-action, stoichiometric matrix | 守恒量需要先消去 |

## 7. "我不知道该问什么"时的分类问题

当用户描述不清时，这几个问题能最快把问题分到上面某一行：

1. 你关心的是**随时间怎么变**（动力学）、**某个状态好不好/稳不稳**（稳定性）、**从数据推回原因或参数**（逆问题/统计推断）、**找最好的选择**（优化），还是**某个说法在什么条件下成立**（证明/判断）？
2. 系统里的**随机性**重要吗？是测量误差，还是系统本身在随机波动？
3. 量是**连续的**（浓度、电压）还是**离散的**（细胞数、事件次数、图上的节点）？
4. 有没有**两个差别很大的尺度**（快/慢、大/小、局部/整体）？
5. 你已经有**数据**、**模型方程**，还是两者都有？想用数学做的是解释现象、预测、还是指导设计？
