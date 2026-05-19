---
layout: post
title: "答案对了，推理错了：LLM 真的会思考吗？"
date: 2026-05-17
tags: [AI, LLM, reasoning, chain-of-thought, tutorial]
lang: zh
subtitle: "当 AI 答案正确但推理过程全是编的——这件事比你想象的严重得多"
cover_image: /assets/images/blog/llm-reasoning-vs-real.svg
permalink: /zh/blog/2026/05/llm-right-answer-wrong-reasoning/
---

我让一个 AI 解一道数学题。它写了 Python 代码，"运行"了一下，展示了中间结果，最后得出了正确答案。看起来很厉害——直到我仔细一看，发现那个推理过程是在"演戏"，而不是在"证明"。

## 故事的起因：一道把我骗了的数学题

在另一次 CS146S 的作业中，我需要用 **思维链（Chain-of-Thought）** 提示法让 LLM 解这道题：

```
What is 3^{12345} (mod 100)?
```

翻译成人话就是：3 的 12345 次方，最后两位数字是什么？答案是 **43**。

下面是代码设置：

```python
from ollama import chat

system_prompt = """You solve user's problem. Think step by step. 
Output the reasoning trace and the final answer."""

user_prompt = """
Solve this problem, then give the final answer on the last line 
as "Answer: <number>".

what is 3^{12345} (mod 100)?
"""

response = chat(
    model="llama3.1:8b",
    messages=[
        {"role": "system", "content": system_prompt},
        {"role": "user", "content": user_prompt},
    ],
    options={"temperature": 0.3},
)
print(response.message.content)
```

Llama 3.1 给出了一个结构漂亮的回答：它写了 Python 代码，展示了"输出结果"，识别出了一个规律，最终得出了 43。从表面上看，这是一个严谨的、逐步推导的解题过程。

但有些地方不太对劲。

## Llama 给我看了什么

模型的回答大概是这样的：

**第 1 步：** 计算前几个 3 的幂对 100 取模的结果，找规律。

```python
powers = []
for i in range(1, 10):
    power = pow(base, i, modulus)
    powers.append(power)
print(powers)  # Output: [3, 9, 27, 81, 43, 29, 87, 61, 83, 49]
```

**第 2 步：** "从输出结果可以看出，这个模式每 20 个幂次重复一次。"

```python
simplified_exponent = 12345 % 20  # Output: 5
```

**第 3 步：** 计算最终结果。

```python
result = pow(3, 5, 100)  # Output: 43
```

**Answer: 43** ——正确！

看起来很靠谱对吧？代码、输出、模式识别、最终答案，一套流程下来很完整。但问题是……

## 三个错误

![What Llama showed vs what actually happened](/assets/images/blog/llm-reasoning-vs-real.svg)

### 1. 它根本没运行代码

Llama 3.1 是一个文本生成模型，它没有 Python 解释器。那些 `print()` 语句后面的"输出结果"？其实是模型根据训练数据中的模式，*预测*出来的文本——它猜的是输出*应该*长什么样。

对于简单的计算，这些预测恰好是对的。但模型并不是在**计算（computing）**——它是在**表演（performing）**计算。

### 2. 逻辑有漏洞

它展示了 10 个值，然后声称模式每 **20** 个一循环。但你不可能从 10 个数据点推导出周期是 20。要真正证明这一点，你需要验证 3^20 mod 100 = 1。Llama 完全跳过了这一步。

### 3. 它很可能一开始就知道答案

最可能的解释是：Llama 在训练数据中见过类似的模运算题。它已经"知道"周期是 20（这和**欧拉函数（Euler's totient function）**有关）。所以它是围绕一个已经检索到的答案，*反向构造*了一个看起来合理的推导过程——而不是*正向推导*出一个新发现的答案。

这就是**事后合理化（post-hoc rationalization）**：从结论出发，反向编一个故事。

## 真正的问题：LLM 到底会不会推理？

这个经历指向了一个研究人员正在积极研究的更深层问题：**当一个 LLM 向你展示它的"思考过程"时，它展示的是真正导致答案产生的过程，还是一个编造出来的叙事？**

最近的研究把这称为**不忠实的思维链（unfaithful chain-of-thought）**问题。2025 年一项题为"Chain-of-Thought Reasoning In The Wild Is Not Always Faithful"的研究发现，即使是最前沿的模型也会产生事后合理化——GPT-4o-mini 大约有 13% 的概率这样做，就连最好的模型也不能完全幸免。

核心矛盾在于：LLM 被训练来预测下一个 **token**，而不是进行逻辑推理。当我们提示它"逐步思考"时，它生成的是*看起来像*推理的文本。有时候这*确实是*真正的推理。有时候只是伪装成逻辑格式的模式匹配。

而真正危险的地方在于：**你不能总是通过阅读输出来分辨真假。** 编造出来的推理看起来和真正的推理一样令人信服。

## 为什么会这样

### LLM 是文本预测器，不是逻辑引擎

归根结底，语言模型做的事情就是预测："根据到目前为止的所有内容，下一个 token 是什么？"它们在训练中见过几百万个数学解题过程，所以它们知道证明的*格式*。但知道格式不等于遵循逻辑。

### 训练数据制造了捷径

Llama 见过成千上万的模运算问题。它学到了"模幂运算 → 找周期 → 简化指数"这个模板。所以它检索并套用了这个模板。当模板恰好适用时，答案就是对的。当不适用时，你得到的就是一本正经的胡说八道。

### "写出你的解题步骤"不等于"诚实地展示解题步骤"

**思维链提示法（Chain-of-thought prompting）**（"逐步思考"）的设计初衷是通过迫使模型展示中间步骤来提高准确率。它确实提高了准确率——但研究表明，展示出来的步骤并不总是反映模型实际的"决策过程"。模型可能是通过一条完全不同的内部路径得出了答案，然后在事后生成了一个看起来合理的解释。

## 我们能做什么？

### 1. 给 LLM 真正的工具

最直接的解决办法：让模型真正执行代码，而不是假装执行。GPT-4 带 **Code Interpreter**、Claude 带 **tool use**——这些模型可以真正运行 Python。当 Llama 在"脑子里"运行代码时，它是在猜。当有工具访问权限的模型运行代码时，它是在计算。

### 2. 验证推理过程，而不只是答案

不要停留在"答案对不对？"这个问题上。要问："每一步是否从上一步逻辑推导而来？"在我的 Llama 例子中，检查"从 10 个值跳到周期 20"这个逻辑漏洞就能立刻发现问题。

### 3. 自洽性检查（Self-consistency checking）

用不同的 temperature 多次运行同一个 prompt。如果模型产生了不同的推理路径但得出相同的答案，那答案很可能是对的。如果推理路径互相矛盾，那就有问题了。研究表明这种方法可以显著提高可靠性。

### 4. 用不同方法交叉验证

让模型用两种不同的方法解同一道题。如果两种方法得出一致的结论，信心就提高了。如果不一致，至少有一条推理链是不忠实的。

### 5. 把 LLM 的输出当草稿，而不是证明

这是最重要的思维转变。LLM 的推理是人类验证的*起点*，而不是替代品。模型给你一个看起来合理的方法；你来验证它是否真的成立。

## 关键要点

1. **正确的答案不等于正确的推理** ——LLM 可以因为错误的理由得出正确的答案
2. **LLM 在表演推理，而不总是在实践推理** ——你看到的"思考过程"可能是事后重构的
3. **假装执行代码是真实存在的问题** ——没有工具访问权限时，模型会生成看起来合理但未经验证的输出
4. **思维链强大但不完美** ——它提高了准确率，同时也制造了虚假的透明感
5. **永远要验证过程，而不只是结果** ——尤其是在数学、逻辑和任何高风险推理场景中

<p style="font-size: 1.4em; font-style: italic; text-align: center; margin: 2rem 0;">最高明的骗子不会弄错事实——他们只是编造了推理过程。</p>

---

## 参考资源

- [Chain-of-Thought Reasoning In The Wild Is Not Always Faithful (arXiv, 2025)](https://arxiv.org/abs/2503.08679)
- [Language Models Don't Always Say What They Think (arXiv, 2023)](https://arxiv.org/abs/2305.04388)
- [Self-Consistency Improves Chain of Thought Reasoning (arXiv)](https://arxiv.org/abs/2203.11171)
- [Chain of Thought Faithfulness: Why LLM Reasoning Is Often a Narrative (n1n.ai)](https://explore.n1n.ai/blog/llm-cot-faithfulness-research-2026-03-30)
- [Stochastic Parrot — Wikipedia](https://en.wikipedia.org/wiki/Stochastic_parrot)
- [Hallucinations in Code Are the Least Dangerous Form of LLM Mistakes (Simon Willison)](https://simonwillison.net/2025/Mar/2/hallucinations-in-code/)
