# OpenAI math 项目的 prompt 规律

来源：https://github.com/openai/math （Apache-2.0）。仓库收录了 OpenAI 内部模型在开放研究问题上产出的数学论文（372 个结果族、719 篇手稿，约 42% 的主要结果有 Lean 形式化）。`reasoning_traces/` 目录下 10 份推理摘要附有 **Original prompt (excerpts)**，是观察他们如何给模型出题的直接材料。下面的规律从这些 prompt 中提炼，原文只做简短摘录。

## 目录

1. 开头定交付物与成功标准
2. 完全自包含的定义
3. 写死量词与"什么固定、什么变化"
4. 逐条消除边界歧义
5. 正反两种结局都给验收标准
6. 点名"不是在问什么"
7. 预先堵住已知的偷懒路径
8. 研究方向 + 阶段性里程碑
9. 要求自检：假设在哪里用到、能否还原已知特例
10. 带注释的参考文献
11. 交代改进点在哪里（前沿位置）
12. 给出等价表述与允许的约化
附：仓库给出的元经验

---

## 1. 开头定交付物与成功标准

> "Please give a complete rigorous solution to the following problem. Even if the problem is "open", the intention is that you should resolve it…"（Mézard–Parisi）
>
> 随后说明：完整严格的解决或"well-presented, rigorous refutation"都可以；最终稿不鼓励暴力枚举和计算机辅助证明。

**为什么重要**：模型知道"做到什么程度算完成"，也知道"推翻命题"同样是成功——这消除了模型硬凑证明的压力。

**实践者版本**：第一段写清要的是推导、证明、界、算法还是方案；允许"在给定假设下结论不成立"作为合法答案；允许"题目不适定"作为合法答案，并要求给出最小修正。实践者通常*欢迎*数值验证，这一点与 OpenAI 相反。

## 2. 完全自包含的定义

Kaplansky 问题里，群代数 $K[G]$ 从"有限形式和、卷积乘法、单位元"开始定义；Mézard–Parisi 问题引用了论文中的方程编号，但仍然写出 "Here is a complete definition of its finite-level trial functional"，把整套构造重写一遍。

**为什么重要**：模型对某个记号的"默认理解"可能和你的不一样；不同文献的约定也可能互相冲突（归一化、符号、是否含常数项）。

**实践者版本**：领域里"大家都知道"的东西恰恰最需要定义——"信噪比"是功率比还是幅度比？"半衰期"指哪个房室？"学习率"含不含 warmup 调度？引用论文时，把需要的定义抄进来。

## 3. 写死量词与"什么固定、什么变化"

> "The alphabet, all predicate truth tables, and k=max_f k_f are fixed independently of the input size"（Max-CSP 问题）
>
> "there is a finite beta_0(d,S)>0 such that for every finite beta>=beta_0 …"（Heisenberg 问题）——常数允许依赖什么，写在记号里。
>
> "All model parameters and disorder laws are fixed as N→∞"（Mézard–Parisi）

**为什么重要**："对所有 / 存在"的顺序、常数依赖什么、哪些量随规模变化，决定了命题的真假。模型最常见的偷换之一就是悄悄让常数依赖本不该依赖的量。

**实践者版本**：说"当样本量很大时"要写成"固定维度 $d$，令 $n\to\infty$"还是"$d/n\to\gamma\in(0,1)$"；说"学习率足够小"要写成"存在 $\eta_0>0$（可依赖于 $L$ 和 $\mu$，不依赖于 $T$），使得对所有 $\eta<\eta_0$…"。

## 4. 逐条消除边界歧义

例子：零值实例从比值中排除，不引入 0/0 约定；集合为空时规定 $\alpha_\Gamma=1$；索引"有放回"抽样；元组内变量允许重复；每条无序边只计一次；$D$ 取非负平方根；$\mathbb N=\{1,2,\dots\}$。

**为什么重要**：这些小地方是"模型的解看起来对、但其实解的是另一个问题"的主要来源。

**实践者版本**：退化情形（零剂量、零噪声、单个样本）、初始条件、边界条件、缺失数据怎么处理、比值分母为零时怎么办、离散化步长的约定。

## 5. 正反两种结局都给验收标准

> "A disproof must provide a set with divergent reciprocal sum and some fixed length k>=3 for which no such progression exists."（等差数列问题）
>
> 反例必须给出素数、域、良定义的群和两个有限和，并认证 $ab=1$；"A finitely presented group together with unproved word equalities or inequalities is not a certificate."（Kaplansky）

**为什么重要**：模型知道什么样的反例才算数，就不会用"看起来像反例"的东西糊弄；也不会在命题其实为假时硬凑证明。

**实践者版本**："如果你认为该方法在这种设定下不收敛，请给出一个具体的参数组合和初值，并说明如何用数值实验复现发散。""如果识别不唯一，请构造两组不同参数产生完全相同观测的例子。"

## 6. 点名"不是在问什么"

> "The target is direct finiteness of group algebras, not the assertion that every group has no zero divisors…"（Kaplansky）
>
> "No attainment at a fixed level or by a particular trial law is asserted."（Mézard–Parisi）
>
> "It imposes no magnetization requirement on the rotation-invariant mixture…"（Heisenberg）

**为什么重要**：模型很容易滑向一个相邻的、更容易或更有名的问题。点名排除比事后纠正便宜得多。

**实践者版本**："我不是在问最优学习率是多少，而是在问为什么线性缩放会失稳。""不需要综述所有因果推断方法，只需要针对这种混杂结构的方案。""不需要数值求解 PDE，需要的是参数依赖关系的定性结论。"

## 7. 预先堵住已知的偷懒路径

> 任何只对 sofic、residually finite、amenable 或 surjunctive 群成立的证明，"must identify its restriction instead of silently extending it to arbitrary groups."（Kaplansky）
>
> 关于复算子代数的定理不能未经论证就搬到特征 $p$；引用的文献中关于非 sofic 群的历史评论 "must not be assumed current."

**为什么重要**：出题人知道这个问题上最常见的错误论证长什么样，提前写进 prompt。

**实践者版本**：把你所在领域的典型错误写进去——"不要假设样本独立，数据有重复测量""不要用小信号线性化后的结论推断大扰动行为，除非证明了线性化有效的范围""如果只在高斯噪声下成立，请明确说明"。

## 8. 研究方向 + 阶段性里程碑

> "Existing proofs for characteristic zero and for surjunctive groups are useful starting points."
>
> "A useful first milestone is a new class of groups not covered by the known sufficient hypotheses… These milestones do not replace the full quantifiers of the target."（Kaplansky）

**为什么重要**：给出起点能节省大量探索；声明"里程碑不等于完成"能防止模型把部分结果包装成完整答案。

**实践者版本**："可以先做一维 / 线性 / 两个房室的情形，再推广；如果只能完成特例，请明确说明哪些步骤无法推广以及原因。"

## 9. 要求自检

> "Check explicitly where finite support, the field characteristic, torsion, and any approximation assumption enter. Verify the argument recovers the elementary finite-group case…"（Kaplansky）

推理摘要也显示模型自己会做类似检查：用常数相互作用做归一化检查、反复审计恒等式、检查极限次序。

**实践者版本**：要求标明每个假设在哪一步用到（这样你能看出放宽某个假设会影响哪里）；要求结果在已知特例下退化为已知答案；量纲检查；给一个小数值例子验算。

## 10. 带注释的参考文献

Kaplansky 问题列出两篇起点文献，各自注明：一篇 "Provides a known sufficient surjunctivity hypothesis, not an all-groups solution"；另一篇给出精确定义和在 surjunctivity 下的证明，但其历史评论不能当作现状。

**为什么重要**：防止模型把"相关文献"误读为"已解决的文献"，也防止它引用过时的结论。

**实践者版本**：给出你知道的关键论文 / 教材章节，并说明你从中用到什么（"用它的模型设定，不用它的近似"）。也要求模型对自己引用的定理给出出处，以便核对——LLM 有时会编造引用。

## 11. 交代改进点在哪里

> "The intended improvement is to obtain matching NP-hardness without the Unique Games Conjecture."（Max-CSP）
>
> "This is the sharpness conjecture immediately following Theorem 4"（Mézard–Parisi）

**为什么重要**：模型知道已知结果止步于何处、新意在哪里，就不会把已知结果复述一遍当作答案。

**实践者版本**："我已经知道小学习率下 SGD 收敛的标准结果，我想知道的是 batch size 和学习率同时变化时……""文献里的公式假设了均匀截面，我的梁是变截面的。"

## 12. 给出等价表述与允许的约化

> "equivalently the weak operator closure…"；"equivalently their Gram matrix is positive semidefinite"
>
> "It is enough to establish the assertion for every finitely generated G and then explain the reduction"（Kaplansky）

**为什么重要**：等价表述帮助模型对上它熟悉的形式；明确允许的约化让模型敢于先简化。

**实践者版本**："可以先无量纲化""可以假设时间单位使得 $k_{el}=1$""连续时间和离散时间版本都可以，但请说明从一个到另一个的对应关系"。

---

## 附：仓库给出的元经验

- **规模与筛选**：约 4000 个问题、平均每题约 3 小时推理，汇总并按重要性筛选后得到 719 篇手稿。高难度问题上，大多数尝试不会直接成功——对实践者意味着：难问题要预期多轮迭代，先拿特例和部分结果。
- **验证不可省**：仓库明确说未形式化的结果可能有问题；已有论文因一个符号错误使论证失效而被撤回，连带两篇依赖它的论文。对实践者意味着：任何 LLM 给出的推导，都要配一个你能自己执行的检查。
- **推理过程的样子**：摘要显示模型会先尝试找反例、用简单特例做归一化检查、反复审计关键步骤、注意极限次序。在 prompt 里显式要求这些动作，等于把好习惯写进任务说明。
- **问题陈述就是形式化目标**：有 Lean 形式化的结果配有 scope notes，逐条说明形式化覆盖了什么、没覆盖什么（例如"没有给出常数的显式值"）。对实践者意味着：交付答案时也要说明"答案在什么范围内成立"。
