# math-problem-framing

> Turn a research problem from your field into a precise math problem — and into a prompt an LLM can actually solve.
>
> 把你所在领域的研究难题，变成精确的数学问题，再变成 LLM 真正解得出来的 prompt。

A bilingual (English / 中文) [Claude skill](https://support.claude.com/en/articles/12512180-use-skills-in-claude) for researchers who use math every day but are not mathematicians: ML researchers, engineers, clinicians, biologists, chemists, physicists.

[English](#english) · [中文](#中文)

---

## English

### Why

When an LLM gets a math question wrong, the problem is often not its reasoning but the question itself. Objects are undefined, quantifiers are missing, "optimal" has no criterion, assumptions are hidden. The model silently picks an interpretation and then cleanly solves a problem that is not yours. If you are not a mathematician, that is hard to spot.

The prompts OpenAI used to pose open research problems to its models ([openai/math](https://github.com/openai/math)) follow a consistent pattern: fully self-contained definitions, explicit quantifiers, acceptance criteria for both a proof and a refutation, an explicit list of what is *not* being asked, and required self-checks. But those prompts were written by mathematicians who already knew the precise statement.

This skill works one step earlier. It helps you get from "here is what I see in my lab / model / data" to a mathematical statement you can confirm is really your problem. Then it builds the prompt and tells you how to check the answer without being able to read a proof line by line.

### What you get

For each problem, the skill returns:

1. **In one sentence**: what your problem is mathematically, in your field's language plus the math name (e.g. "an identifiability question in an inverse eigenvalue problem").
2. **Formalization card**: notation table, known vs unknown, model, assumptions (each tagged as confirmed by you / inferred / simplification), and the goal with explicit quantifiers. It also explains back, in your field's terms, which choices it made on your behalf.
3. **Please confirm**: up to five open points, ordered by how much they would change the answer.
4. **The prompt**: copy-ready, for any reasoning model (ChatGPT, Claude, Gemini, …). For "I don't know what kind of math this is" problems, it gives a two-stage prompt: a method map first, then the solve.
5. **How to verify**: checks you can run yourself (special cases, limits, units, simulation code, expected numbers) plus a reviewer prompt for a second model.
6. **Search keywords**: canonical English terms for the methods involved.

### Install

**Claude (claude.ai)**

1. Make sure *Code execution and file creation* is enabled (Settings → Capabilities on Free/Pro/Max; on Team/Enterprise your owner controls this).
2. Zip the `math-problem-framing/` folder from this repo. The folder name must stay `math-problem-framing`.
3. Go to **Customize → Skills**, click **+ → Create skill → Upload a skill**, and upload the ZIP.

See [Use skills in Claude](https://support.claude.com/en/articles/12512180-use-skills-in-claude) for details.

**Claude Code**

```bash
git clone https://github.com/HuayuLiArizona/math-problem-framing.git
cp -r math-problem-framing/math-problem-framing ~/.claude/skills/      # all projects
# or: cp -r math-problem-framing/math-problem-framing <your-project>/.claude/skills/
```

See [Extend Claude with skills](https://code.claude.com/docs/en/skills).

### Usage

The skill runs **only when you call it**. Ordinary math, statistics or prompt-writing questions are answered normally without it.

```text
/math-problem-framing I train a 1B transformer with AdamW. Scaling batch size 256→2048 with
learning rate ×8 gives early loss spikes; with ×√8 it's fine. Help me write a prompt so GPT
can derive why.
```

```text
/math-problem-framing 我们在简支钢梁桥上能识别前 3 阶竖向固有频率（精度约 ±0.5%）。
只靠频率变化，理论上能不能唯一确定损伤的位置和程度？帮我写一个问 LLM 的 prompt。
```

You can also ask by name: "use math-problem-framing on this…" / "用 math-problem-framing 帮我…".

- Type `/math-problem-framing` alone and it will ask you to describe your problem and list what helps.
- Once called, follow-ups about the same problem stay in the skill: answering its questions, revising the prompt, or pasting the LLM's answer for review.
- It replies in your language. Chinese prompts keep key terms' English names in parentheses, e.g. 一致收敛 (uniform convergence). At the end it offers the prompt in the other language.

**Claude Code only:** to make "only when called" a hard guarantee, you can add `disable-model-invocation: true` to the frontmatter of `SKILL.md`. It is not included by default because the claude.ai uploader may not accept extra frontmatter keys.

### How it works

```text
Stage 0  Where are you stuck? What shape of answer do you need?
Stage 1  Short domain interview (3–5 questions per round, in your field's language)
Stage 2  Formalization card + explain-back in your field's terms   ← the key step
Stage 3  Locate the mathematical area and candidate methods
Stage 4  Assemble the prompt (structure adapted from OpenAI's math prompts)
Stage 5  Ambiguity audit
Stage 6  Verification plan
```

If it cannot ask you questions (e.g. "just give me the prompt"), it proceeds with explicit, labelled assumptions and lists the ones that matter most.

### Repository layout

```text
math-problem-framing/
├── SKILL.md                              # workflow, language policy, invocation rules
└── references/
    ├── en/                               # English references
    │   ├── openai-math-prompt-patterns.md   # 12 patterns from openai/math prompts
    │   ├── domain-math-map.md               # domain phrasing → math structure → methods → pitfalls
    │   ├── prompt-templates.md              # templates by answer type + reviewer prompt
    │   ├── verification-toolkit.md          # checks non-mathematicians can run
    │   └── worked-examples.md               # pharmacokinetics, gene circuit, ML estimator variance
    └── zh/                               # 中文镜像，内容一致
```

Only one language folder is loaded per conversation.

### Testing

Tested with Claude Opus 5.5 during development:

- **Behaviour.** On ML, clinical-statistics, structural-engineering and neuroscience problems, we compared answers with and without the skill. Without the skill, Claude usually identified the right mathematics too. The differences were in the prompts it wrote:
  - Without the skill, 3 of 4 prompts stated the expected conclusion inside the prompt, which invites the target model to agree.
  - With the skill, the prompts consistently had an explicit assumptions list, a "not being asked" section, self-checks and a reviewer prompt.
- **Invocation.** We ran 18 requests, 2 runs each, with the skill installed:
  - All 8 requests that named the skill invoked it.
  - None of the 10 similar requests that did not name it did. An earlier auto-triggering version fired on 6 of those 10.
- **Language.** English requests loaded only `references/en/`, Chinese requests only `references/zh/`. Replies and prompts matched the user's language.

### Limitations

- **It frames problems; it does not guarantee solutions.** For research-level problems, partial results and special cases are often the realistic target. In OpenAI's project, roughly 4,000 problems led to 719 manuscripts in 372 result families.
- **Always run the verification checks.** LLM derivations can be confidently wrong.
- **Responses are long and can be slow.** In unattended test runs the skill often computed reference numbers itself before answering, taking 17–77 minutes versus 2–12 minutes without the skill. In normal use it asks you a few questions first.

### Acknowledgements

The prompt patterns are distilled from the problem statements in [openai/math](https://github.com/openai/math) (Apache-2.0). Short excerpts are quoted with attribution in `references/*/openai-math-prompt-patterns.md`. The skill was built and evaluated with Anthropic's skill-creator.

### License

[Apache License 2.0](LICENSE). Third-party attributions are listed in [NOTICE](NOTICE).

---

## 中文

### 为什么需要它

LLM 答错数学题，很多时候不是推理能力不够，而是题目本身没说清楚：对象没定义、量词缺失、"最优"没有标准、假设藏在心里。模型会悄悄替你选一种理解，然后漂亮地解出一个**不是你的问题**的问题。非数学专业的人很难发现这一点。

OpenAI 给模型出开放研究问题时用的 prompt（[openai/math](https://github.com/openai/math)）有一套很一致的写法：完全自包含的定义、写清楚的量词、证明和反驳各自的验收标准、明确列出"不在问什么"、要求自检。但那些 prompt 是数学家写的，他们本来就知道精确的数学陈述是什么。

这个 skill 往前多走一步：帮你从"我在实验 / 模型 / 数据里看到的现象"走到一个你能确认"对，这就是我的问题"的数学陈述，再据此写 prompt，并告诉你在看不懂证明的情况下怎么检查答案。

### 你会得到什么

1. **一句话定位**：你的问题在数学上是什么（领域语言 + 数学名称，比如"逆特征值问题里的可辨识性问题"）。
2. **形式化卡片**：记号表、已知与未知、模型、假设（标明来源：你确认的 / 推测的 / 为可解而做的简化）、带完整量词的目标。还会用你的领域语言讲回给你听，指出它替你做了哪些选择。
3. **需要你确认的点**：最多 5 条，按对结论的影响排序。
4. **最终 prompt**：可直接复制，适用于各家推理模型（ChatGPT、Claude、Gemini……）。如果你连"该用什么数学"都不确定，会给两阶段 prompt：先让模型做方法地图，再求解。
5. **验证方案**：你自己就能执行的检查（特例、极限、量纲、模拟代码、预期数值），外加一份交给另一个模型的审稿 prompt。
6. **延伸阅读关键词**：相关方法的英文规范术语。

### 安装

**Claude（claude.ai）**

1. 确认已开启"代码执行与文件创建"（Free/Pro/Max 在 Settings → Capabilities；Team/Enterprise 由管理员控制）。
2. 把本仓库里的 `math-problem-framing/` 文件夹压缩成 ZIP，文件夹名保持 `math-problem-framing` 不变。
3. 打开 **Customize → Skills**，点 **+ → Create skill → Upload a skill**，上传 ZIP。

详见 [Use skills in Claude](https://support.claude.com/en/articles/12512180-use-skills-in-claude)。

**Claude Code**

```bash
git clone https://github.com/HuayuLiArizona/math-problem-framing.git
cp -r math-problem-framing/math-problem-framing ~/.claude/skills/      # 所有项目可用
# 或者：cp -r math-problem-framing/math-problem-framing <你的项目>/.claude/skills/
```

详见 [Extend Claude with skills](https://code.claude.com/docs/en/skills)。

### 使用方法

这个 skill **只在你调用时运行**。普通的数学、统计或写 prompt 的问题不会启用它，Claude 照常回答。

```text
/math-problem-framing 我是普外科研究生，在做回顾性队列研究：术后 24 小时内早下床能不能降低肺部并发症？
1800 例，但早下床的病人更年轻、手术更短、ASA 更低。想让 ChatGPT 帮我设计分析方法、判断样本量够不够。
```

也可以点名调用："用 math-problem-framing 帮我……" / "use math-problem-framing on this…"。

- 只输入 `/math-problem-framing` 时，它会请你描述问题，并告诉你哪些信息最有用。
- 调用之后，同一个问题的后续对话都在 skill 里进行：回答它的追问、改 prompt、把 LLM 的回答贴回来审。
- 你用中文它就用中文，用英文就用英文。中文 prompt 的关键术语会在括号里保留英文原名，比如"一致收敛 (uniform convergence)"。结尾会问你要不要另一种语言的版本。

**仅限 Claude Code：** 如果想从机制上保证"只在调用时运行"，可以在 `SKILL.md` 的 frontmatter 里加一行 `disable-model-invocation: true`。默认没有加，因为 claude.ai 的上传器可能不接受额外的 frontmatter 字段。

### 工作流程

```text
Stage 0  你卡在哪一步？需要什么形式的答案？
Stage 1  简短访谈（每轮 3–5 个问题，用你的领域语言问）
Stage 2  形式化卡片 + 用领域语言回译确认    ← 最关键的一步
Stage 3  定位数学领域与候选方法
Stage 4  组装 prompt（结构改编自 OpenAI 数学项目的 prompt）
Stage 5  歧义审计
Stage 6  验证方案
```

如果没法向你提问（比如你说"直接给我 prompt"），它会带着标注清楚的假设继续，并把最影响结论的几条列出来。

### 仓库结构

```text
math-problem-framing/
├── SKILL.md                              # 工作流程、语言策略、调用规则
└── references/
    ├── en/                               # English references
    └── zh/                               # 中文参考资料（与 en 内容一致）
        ├── openai-math-prompt-patterns.md   # 从 openai/math 提炼的 12 条 prompt 规律
        ├── domain-math-map.md               # 领域说法 → 数学结构 → 方法 → 常见陷阱
        ├── prompt-templates.md              # 按答案类型分的模板 + 审稿 prompt
        ├── verification-toolkit.md          # 非数学家能执行的检查方法
        └── worked-examples.md               # 药代动力学、基因回路、ML 估计量方差三个完整示例
```

每次对话只加载其中一种语言的文件夹。

### 测试情况

开发过程中用 Claude Opus 5.5 做了测试：

- **行为对比。** 在机器学习、临床统计、结构工程、神经科学四类问题上，对比了用和不用 skill 的回答。不用 skill 时，Claude 通常也能找对数学方向，差别主要在它写出的 prompt：
  - 不用 skill 时，4 个 prompt 里有 3 个把预期结论直接写进了 prompt，会诱导目标模型顺着说。
  - 用 skill 时，prompt 都包含明确的假设清单、"不在问什么"、自检要求和审稿 prompt。
- **调用控制。** 安装 skill 后测了 18 条请求，每条 2 次：
  - 点名调用的 8 条全部启用了 skill。
  - 没点名的 10 条相近请求一次都没启用。之前会自动触发的版本在这 10 条里有 6 条被启用。
- **语言。** 英文请求只加载了 `references/en/`，中文请求只加载了 `references/zh/`，回复和 prompt 的语言都与用户一致。

### 局限

- **它负责把问题定义清楚，不保证问题能被解出来。** 研究级问题往往只能先拿到特例或部分结果。OpenAI 的项目里，大约 4000 个问题最终整理出 372 个结果族、719 篇手稿。
- **一定要做验证。** LLM 的推导可能错得很自信。
- **回答比较长，有时比较慢。** 在无人值守的测试中，它常常先自己算出核对用的数字再回答，用时 17–77 分钟，不用 skill 时为 2–12 分钟。正常使用时它会先问你几个问题。

### 致谢

prompt 规律提炼自 [openai/math](https://github.com/openai/math)（Apache-2.0）中的问题陈述，`references/*/openai-math-prompt-patterns.md` 中的短摘录均注明出处。本 skill 使用 Anthropic 的 skill-creator 构建并评测。

### 许可证

[Apache License 2.0](LICENSE)。第三方内容的出处见 [NOTICE](NOTICE)。
