---
layout: post
title: "和 AI 对话的艺术：提示工程实用指南"
date: 2026-05-18
tags: [AI, LLM, prompt-engineering, tutorial, techniques]
lang: zh
subtitle: "一次作业让我学会了六种技巧——为什么提问方式比模型本身更重要"
cover_image: /assets/images/blog/prompt-techniques-overview.svg
permalink: /zh/blog/2026/05/prompt-engineering-guide/
---

同一个模型，同一个问题，答案却天差地别。我发现，一个没用的 AI 回答和一个惊艳的回答之间的差距，往往和模型本身无关——关键在于你怎么问。

## 故事的起点：一次作业，六种技巧

我的 CS146S 课程（The Modern Software Developer）布置了一组编程练习，它们有一个共同的结构：让 Llama 3.1（8B）正确完成一个任务。关键在于，每道题要求使用*不同的提示技巧*——而结果的差异令人震惊。

同一个模型，同一台机器，能力却完全不同，仅仅因为换了一种提问方式。

以下是我实现的六种技巧，按从简单到强大的顺序排列。

![从基础到高级的提示技巧](/assets/images/blog/prompt-techniques-overview.svg)

## 1. 少样本提示（Few-Shot Prompting）："用例子来教"

**核心思路：** 不要解释规则，而是给模型展示正确的输入-输出示例，让它自己找到规律。

在我的作业中，我需要让 Llama 反转 "httpstatus" 这个词。与其解释什么是反转，不如直接给它看例子：

```python
system_prompt = """You reverse the letters of a word. 
Output ONLY the reversed word.

Word: httpstatus
Reversed: sutatsptth

Word: hello
Reversed: olleh

Word: httpstatus
Reversed: sutatsptth"""
```

**为什么有效：** 大语言模型本质上是模式补全机器。当它们看到一致的输入 → 输出模式时，就会自动推断。少样本提示正是利用了这一点，给模型一个可以遵循的"模板"。

**需要注意的坑：**
- **示例的质量比数量重要得多。** 一个错误的示例就能把模型带偏。三个干净、多样的示例通常比十个重复的效果好。
- **顺序会影响结果。** 把简单的例子放前面，难的放后面。模型对最近的上下文更敏感。
- **它并不是真的"理解"了。** 模型只是在模仿模式，并没有真正"搞懂"——这也是为什么它在边界情况（比如不常见的分词方式）下依然会失败（参见[我的第一篇博客](/zh/blog/2026/05/why-llms-cant-reverse-words/)）。

## 2. 思维链（Chain-of-Thought）："一步一步想"

**核心思路：** 要求模型在给出最终答案之前，先展示推理过程。

我用这个技巧来计算 3^{12345} mod 100：

```python
system_prompt = """You solve user's problem. Think step by step. 
Output the reasoning trace and the final answer."""
```

**为什么有效：** 当你强制模型生成中间步骤时，这些步骤会变成额外的上下文，引导最终答案的生成。就像给模型一张"草稿纸"——把推理过程写出来这个动作本身，就能帮助它保持在正确的轨道上。

**需要注意的坑：**
- **推理过程可能是假的。** 正如我发现并[写过的](/zh/blog/2026/05/llm-right-answer-wrong-reasoning/)，模型可能生成看起来很合理的步骤，但这些步骤实际上并不支持它的结论。它可能先知道答案，再反过来编造推理过程。
- **更长的输出意味着更高的成本。** 思维链会显著增加 token 消耗。对于简单任务来说，没必要。
- **垃圾进，垃圾出。** 如果第一步就错了，后面每一步都会把错误放大。

## 3. 自洽性（Self-Consistency）："问三遍，听多数的"

**核心思路：** 让模型用不同的方法多次解决同一个问题，然后选择出现次数最多的答案。

我的实现是整组作业中最精心设计的提示：

```python
system_prompt = """You will solve every problem multiple times 
using different reasoning approaches.

1. Reasoning 1: Solve step by step, directly.
   Answer: <number>

2. Reasoning 2: Solve again from scratch, 
   using a different framing (e.g. work backwards).
   Answer: <number>

3. Reasoning 3: Solve by checking concrete 
   positions or values.
   Answer: <number>

4. Compare the three answers. Pick the majority vote.
   Answer: <number>"""
```

代码会把这个过程跑 5 次，然后在所有运行结果中取多数票。

**为什么有效：** 不同的推理路径会碰到不同的失败模式。如果三种方法中有两种得出了相同的答案，那个共同答案更可能是对的。道理就像"找三个医生看看"：一个可能误诊，但如果两个意见一致，就更值得信赖。

**需要注意的坑：**
- **成本会翻倍。** 每次调用消耗 3 倍的 token，而且要运行多次。要提前算好预算。
- **多样性很重要。** 如果三种"不同"的方法本质上只是同一种方法的不同说法，那就没有任何好处。要明确指定每种方法*具体*应该怎么不同。
- **不是所有任务都适用。** 对于创意写作或主观性任务，没有一个"正确答案"可以投票。

## 4. 检索增强生成（RAG）："这是你需要知道的"

**核心思路：** 给模型提供外部文档作为上下文，这样它就不用依赖（可能过时或错误的）训练数据。

我的作业要求编写一个调用文档化 API 的函数。关键是把 API 文档直接喂进提示里：

```python
user_prompt = f"""Context (use ONLY this information):
{api_docs}

Task: Write a Python function fetch_user_name(user_id, api_key) 
that calls the documented API.

Requirements:
- Use the documented Base URL and endpoint.
- Send the documented authentication header.
- Return only the user's name string."""
```

**为什么有效：** 大语言模型在缺乏信息时会产生幻觉（Hallucination）。RAG 通过在提示中直接提供模型需要的确切信息来解决这个问题。不用猜测，不会凭空捏造端点或参数。

**需要注意的坑：**
- **上下文窗口（Context Window）有限。** 你不可能把整个代码库都塞进提示里。要精心挑选最相关的文档。
- **"仅使用以下信息"这句话很重要。** 没有它，模型可能会把上下文和训练数据混在一起，发明出根本不存在的字段。
- **检索质量决定一切。** RAG 的效果取决于你喂给它的文档。检索质量差 → 答案就差，模型再好也没用。

## 5. 反思（Reflexion）："从错误中学习"

**核心思路：** 让模型先生成一个答案，测试它，把哪里错了告诉它，然后让它修复代码。

我的作业实现了一个两轮循环：生成 → 测试 → 展示失败 → 重新生成。一个关键细节：**两轮使用不同的系统提示。** 第一轮用简单的生成提示；反思轮用专门的纠错提示，并附带失败的上下文信息。

**第一轮——初始生成**（简单，无反馈）：

```python
system_prompt = """You are a coding assistant. Output ONLY a single 
fenced Python code block that defines the function 
is_valid_password(password: str) -> bool. No prose or comments."""
```

**第二轮——反思**（给出之前的代码 + 哪里出了问题）：

```python
reflexion_prompt = """You are a coding assistant performing 
self-correction (reflexion).

You will be given:
1. A previous implementation that failed some test cases.
2. A list of failing test cases with expected vs. actual results 
   and specific validation rules that failed.

Your job:
- Read the failure report carefully.
- Identify exactly which checks were wrong, missed, or inverted.
- Produce a corrected implementation."""
```

反思提示会收到实际的失败输出作为上下文——比如：`"Input: Password1 → expected True, got False. Failing checks: missing special"`。第一轮往往会漏掉边界情况。但在看到*具体*哪些测试失败了以及*为什么*之后，第二轮就能搞定。

**为什么有效：** 反思模仿了人类调试的方式：写代码 → 跑测试 → 看报错 → 修复。模型不需要第一次就写对，它只需要在具体反馈的引导下*最终*写对就行。

**需要注意的坑：**
- **反馈质量至关重要。** "错了，再试一次"毫无用处。"输入 'Password1' 返回了 True，但应该返回 False，因为缺少特殊字符"——这才是可操作的反馈。
- **一轮迭代可能不够。** 复杂的 bug 可能需要多轮反思。但轮数太多，模型可能会原地打转。
- **模型可能矫枉过正。** 它可能修好了失败的测试，却搞坏了之前通过的。一定要重新运行所有测试，而不只是失败的那些。

## 6. 工具调用（Tool Calling）："用真工具，别靠猜"

**核心思路：** 与其让模型去计算或猜测，不如让它调用外部工具（API、代码解释器、数据库），用真实的结果来工作。

我的作业要求模型调用一个解析源文件的 Python 函数：

```python
system_prompt = """You are a tool-calling assistant. 
Respond with ONLY a JSON object:
{"tool": "<tool_name>", "args": {<arguments>}}

Available tools:
[
  {
    "name": "output_every_func_return_type",
    "description": "Parse a Python file and return 
    function_name: return_type for each function.",
    "parameters": {
      "file_path": { "type": "string" }
    }
  }
]"""
```

模型输出一个结构化的工具调用；代码*真正执行*这个工具，并返回真实结果。

**为什么有效：** 这就是我在[第二篇博客](/zh/blog/2026/05/llm-right-answer-wrong-reasoning/)中提到的"假装执行代码"问题的解决方案。模型不再*假装*运行代码，而是*真正*运行代码。不再*猜测* API 返回什么，而是*实际调用* API。真实数据，真实结果，没有幻觉。

**需要注意的坑：**
- **提示格式必须严格。** 模型需要输出你的代码能解析的有效 JSON。JSON 外面多一个字就会导致解析失败。对输出格式要非常明确。
- **安全性很重要。** 如果模型可以调用任意工具，它就可能造成破坏。一定要对工具调用进行验证和沙箱隔离（Sandboxing）。
- **不是所有模型都原生支持。** 小模型可能很难生成结构化的 JSON 输出。大模型（GPT-4、Claude）有内置的工具调用支持。

## 这些技巧为什么会存在？

所有六种技巧都在解决同一个根本问题：**大语言模型是文本预测器，不是推理器。** 它们根据上下文预测最可能的下一个 token。每一种提示技巧，本质上都是一种*工程化地构造上下文*的方式，让最可能的下一个 token 同时也是正确的那个。

| 技巧 | 核心策略 |
| :--- | :--- |
| 少样本提示（Few-Shot） | 建立一个模型可以延续的模式 |
| 思维链（Chain-of-Thought） | 创建引导答案的中间上下文 |
| 自洽性（Self-Consistency） | 通过聚合多次尝试来降低方差 |
| 检索增强生成（RAG） | 用真实数据替代猜测 |
| 反思（Reflexion） | 利用反馈逐步缩小到正确答案 |
| 工具调用（Tool Calling） | 对需要计算的任务，直接跳过生成环节 |

这个递进关系讲述了一个故事：我们一开始寄希望于模型能"直接搞定"，然后逐渐给它更多的结构、更多的数据、更多的工具——因为光靠希望不是策略。

## 自己动手试试

想练习提示工程吗？这里有几个优秀的资源：

**[Prompting Guide](https://www.promptingguide.ai/)** — 一个全面的开源参考指南，涵盖从零样本到高级智能体模式的所有技巧。非常适合作为速查手册。

**[Learn Prompting](https://learnprompting.org/)** — 一个互动课程，有动手练习。免费且结构清晰，适合从入门到进阶的所有用户。

**[Anthropic's Interactive Tutorial](https://github.com/anthropics/prompt-eng-interactive-tutorial)** — 基于 Jupyter notebook 的课程，每节课底部都有内置的实验环境，可以实时动手。

## 关键收获

1. **提示就是程序。** 你怎么问，往往比你用哪个模型更重要。
2. **从简单开始，按需升级。** 少样本提示能搞定大多数任务；只有当简单提示失败时，才去用思维链、RAG 或工具调用。
3. **每种技巧都有代价。** 更多 token、更多 API 调用、更高复杂度。要根据任务的重要程度来选择技巧。
4. **永远验证输出。** 没有任何技巧能完全消除幻觉——它们只是降低了概率。
5. **组合使用。** 真正的威力来自混合搭配：RAG + 思维链 + 工具调用，这就是生产级 AI 系统的工作方式。

<p style="font-size: 1.4em; font-style: italic; text-align: center; margin: 2rem 0;">每一个提示技巧，都不过是我们早已拥有却遗忘了的沟通能力。</p>

---

## 参考资源

- [Prompt Engineering Guide](https://www.promptingguide.ai/)
- [Learn Prompting — Free Interactive Course](https://learnprompting.org/)
- [Anthropic Interactive Prompt Engineering Tutorial (GitHub)](https://github.com/anthropics/prompt-eng-interactive-tutorial)
- [OpenAI Prompt Engineering Best Practices](https://platform.openai.com/docs/guides/prompt-engineering)
- [Self-Consistency Improves Chain of Thought Reasoning (arXiv)](https://arxiv.org/abs/2203.11171)
- [Reflexion: Language Agents with Verbal Reinforcement Learning (arXiv)](https://arxiv.org/abs/2303.11366)
- [Chain-of-Thought Prompting Elicits Reasoning in LLMs (arXiv)](https://arxiv.org/abs/2201.11903)
- [Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks (arXiv)](https://arxiv.org/abs/2005.11401)
