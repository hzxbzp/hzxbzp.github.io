---
layout: post
title: "为什么AI不会倒着拼写？分词的秘密"
date: 2026-05-16
tags: [AI, LLM, tokenization, NLP, tutorial]
lang: zh
permalink: /zh/blog/2026/05/why-llms-cant-reverse-words/
subtitle: "LLM是如何编码语言的——以及为什么这让'简单'的任务变得出人意料地困难"
cover_image: /assets/images/blog/tokenization-example.svg
---

你有没有让ChatGPT把一个单词倒过来拼，结果它给了你一个错误答案？我就遇到过——然后我就掉进了一个关于AI到底是怎么"阅读"文字的兔子洞。

## 故事的起因：一次翻车的作业

我当时在做CS146S课程（The Modern Software Developer）的作业，任务很简单：**让一个LLM把一个单词的字母倒序排列**。听起来很容易对吧？

下面是我写的代码，用的是k-shot提示和Llama 3.1：

```python
from ollama import chat

YOUR_SYSTEM_PROMPT = """You reverse the letters of a word. 
Output ONLY the reversed word, with no explanation, no punctuation, 
and no other text.

Word: httpstatus
Reversed: sutatsptth

Word: hello
Reversed: olleh

Word: httpstatus
Reversed: sutatsptth"""

USER_PROMPT = """
Reverse the order of letters in the following word. 
Only output the reversed word, no other text:

httpstatus
"""

EXPECTED_OUTPUT = "sutatsptth"

response = chat(
    model="llama3.1:8b",
    messages=[
        {"role": "system", "content": YOUR_SYSTEM_PROMPT},
        {"role": "user", "content": USER_PROMPT},
    ],
    options={"temperature": 0.5},
)
print(response.message.content.strip())
```

即使给了**5个正确示例**，模型还是一直搞错！它会输出类似"sutattsptth"或"statustpth"的东西——接近了，但就是不对。

这不只是Llama的问题。你试试让GPT-4或Claude去反转"httpstatus"——它们也经常会翻车。但**为什么呢**？

## 答案：LLM看不到字母

关键的洞察是：**LLM并不像人类那样逐个字母地阅读文本。** 它们是按"块"来读的，这些块叫做**词元（Token）**。

当你输入"httpstatus"时，AI看到的并不是：

```
h - t - t - p - s - t - a - t - u - s
```

它看到的实际上是这样的：

```
[http] [status]
```

就这样。两个块。模型根本看不到单个字母。

![分词如何将"httpstatus"拆分为词元](/assets/images/blog/tokenization-example.svg)

## 什么是词元（Token）？

**词元（Token）** 是LLM处理的基本文本单位。你可以把它想象成AI语言世界里的"原子"。词元可以是：

- 一个完整的单词：`hello` → 1个词元
- 单词的一部分：`running` → `run` + `ning`（2个词元）
- 单个字符：罕见字符可能是单独的词元
- 标点符号：`.` `!` `?` 通常各自是一个词元
- 甚至空格：在某些分词器中，空格也是词元的一部分

举个例子，句子"I love AI"可能变成3个词元：`[I]` `[love]` `[AI]`。但"tokenization"可能变成 `[token]` `[ization]`——两个词元。

**为什么不直接用单个字母呢？** 因为那样的计算成本太高了。句子"Hello, how are you?"有20个字符，但只有大约6个词元。更少的词元 = 更少的计算 = 更快更便宜的响应。

## 为什么LLM需要分词器（Tokenizer）？

神经网络只认识数字，不认识文字。所以在任何文本到达AI的"大脑"之前，它必须先被转换成数字。整个过程是这样的：

1. **文本** → 被拆分成词元（由分词器完成）
2. **词元** → 映射为数字ID（来自词汇表）
3. **数字ID** → 由神经网络处理
4. **输出ID** → 转换回词元 → 文本

分词器本质上就是AI的"眼睛"——它决定了模型能看到什么、看不到什么。就像人的眼睛有盲区一样，**分词器也有盲区**。字母级别的操作恰恰就落在了这个盲区里。

## 分词是怎么工作的？BPE算法

最流行的分词方法叫做**字节对编码（Byte Pair Encoding, BPE）**。它的原理出奇地优雅：

1. 从所有单个字符作为你的词汇表开始
2. 统计训练数据中哪对相邻字符出现频率最高
3. 将这对字符合并为一个新的词元
4. 重复第2-3步，直到达到你想要的词汇表大小

![BPE算法分步演示](/assets/images/blog/bpe-algorithm.svg)

比如说，如果你的训练数据里有大量英文文本，"th"出现得非常频繁，所以它很早就会被合并。然后"the"变得常见了，于是也被合并。最终，像"the"、"and"、"is"这样的常见词都会变成单个词元，而罕见词则会被拆分成几部分。

### 一个具体的例子

假设我们要从文本"low lower lowest"构建一个小型BPE词汇表：

| 步骤 | 操作 | 词汇表变化 |
|------|------|------------|
| 0 | 从单个字符开始 | `l, o, w, e, r, s, t` |
| 1 | 最高频字符对：`l` + `o` → 合并 | 添加 `lo` |
| 2 | 最高频字符对：`lo` + `w` → 合并 | 添加 `low` |
| 3 | 最高频字符对：`e` + `r` → 合并 | 添加 `er` |
| 4 | 最高频字符对：`e` + `s` → 合并 | 添加 `es` |

训练完成后，"lower"被分词为 `[low][er]`，而不是5个单独的字符。高效！

## 不同模型的不同分词器

并非所有LLM都使用相同的分词器。下面是一个快速概览：

![分词器对比表](/assets/images/blog/tokenizer-comparison.svg)

### BPE（字节对编码，Byte Pair Encoding）
- **使用者：** GPT-2、GPT-3、GPT-4、LLaMA、Mistral、Claude
- **工作原理：** 迭代地合并高频字符对
- **变体：** Byte-level BPE 从256个字节值开始而非字符，可以处理任何语言和编码

### WordPiece
- **使用者：** BERT、DistilBERT、Electra
- **工作原理：** 类似BPE，但根据似然度选择合并（哪个合并最能提高训练数据的概率）
- **关键区别：** 使用 `##` 前缀表示子词的延续部分（如 "playing" → `play` + `##ing`）

### SentencePiece（Unigram）
- **使用者：** T5、ALBERT、XLNet、Gemma
- **工作原理：** 从一个大词汇表开始，逐步移除对整体似然度影响最小的词元
- **关键区别：** 将文本视为原始字节流（不需要先按空格预分词），因此非常适合多语言模型

### Tiktoken（OpenAI的实现）
- **GPT-4：** 使用 `cl100k_base`，约10万个词元
- **GPT-4o：** 使用 `o200k_base`，约20万个词元（词汇量翻倍！）
- 更大的词汇表 = 更多单词成为单个词元 = 更快的推理速度

## 中文怎么办？跨语言的分词挑战

英文单词之间有空格。中文、日文和泰文没有。那"我喜欢人工智能"应该从哪里切分呢？

- **字符级别：** `[我][喜][欢][人][工][智][能]` — 7个词元。安全但浪费。
- **词级别：** `[我][喜欢][人工智能]` — 3个词元。高效但需要先用分词工具。
- **子词（BPE）：** 一种折中方案——常见词如 `人工智能` 变成单个词元，罕见词则被拆分。

关键性的突破是 **SentencePiece**，它把输入当作原始字节流处理，而不是假设单词之间有空格。这使得它可以自然地适用于任何语言。这就是为什么它是T5和Gemma等多语言模型的首选方案。

词汇表大小在这里也很重要。GPT-4o的分词器（o200k_base，20万词元）是GPT-4（cl100k_base，10万词元）的两倍大，其中很大一部分新增词元都给了非英语语言。试着把中文文本粘贴到 [Tiktokenizer](https://tiktokenizer.vercel.app/) 里看看——GPT-4o对于同样的输入一致地产生更少的词元。

## 自己动手试试！

想看看不同的模型是怎么分词的？试试这个很棒的在线工具：

**[Tiktokenizer](https://tiktokenizer.vercel.app/)** — 粘贴任意文本，就能看到GPT-4、GPT-3.5和其他模型是怎么把它拆成词元的。

试着输入"httpstatus"看看它怎么切分！然后再试试"h t t p s t a t u s"（带空格），注意它们的区别。

## 那到底怎么才能让LLM正确地反转字母？

既然我们已经理解了问题所在，以下是一些有效的解决方案：

### 方法一：先把字符分开

最简单的办法——给模型它真正能"看到"的字符：

```python
# Instead of: "Reverse: httpstatus"
# Do this:
prompt = """The letters are: h, t, t, p, s, t, a, t, u, s
Now write them in reverse order, separated by commas:"""
# Output: s, u, t, a, t, s, p, t, t, h
# Then join: "sutatsptth" ✓
```

### 方法二：思维链（Chain-of-Thought，逐步推理）

让模型先列出所有字母，然后再反转：

```python
prompt = """Reverse the letters in "httpstatus".

Step 1: List each letter with its position:
Position 1: h
Position 2: t
Position 3: t
Position 4: p
Position 5: s
Position 6: t
Position 7: a
Position 8: t
Position 9: u
Position 10: s

Step 2: Now list them from last position to first:"""
```

### 方法三：使用代码执行

最可靠的方法——让模型写代码然后运行：

```python
prompt = """Write Python code to reverse the string "httpstatus" 
and output only the result."""

# Model outputs: print("httpstatus"[::-1])
# Result: sutatsptth ✓
```

### 方法四：针对字符级任务的微调（Fine-tuning）

对于生产系统，可以使用**词元内部位置感知（Token Internal Position Awareness, TIPA）** 来微调模型——训练它们完成需要知道词元内字符位置的任务。这是一个活跃的研究方向。

## 核心要点

1. **LLM看不到单个字母** — 它们看到的是词元（文本块）
2. **分词（Tokenization）是一种权衡**：效率 vs. 字符级感知能力
3. **对AI来说，"简单"的文本任务可能很难** — 当这些任务需要字母级别的操作时
4. **变通方法是有的**：字符分离、思维链和代码执行都能帮上忙
5. **分词器就是AI的"眼睛"** — 它的能力和局限性决定了模型能做什么

<p style="font-size: 1.4em; font-style: italic; text-align: center; margin: 2rem 0;">AI并非误解了语言——它从未以我们的方式看过语言。</p>

---

## 参考资源

- [Tiktokenizer — Online Tokenizer Visualization Tool](https://tiktokenizer.vercel.app/)
- [Let's Build the GPT Tokenizer — Andrej Karpathy (YouTube)](https://www.youtube.com/watch?v=7xTGNNLPyMI)
- [Hugging Face — Tokenization Algorithms Summary](https://huggingface.co/docs/transformers/en/tokenizer_summary)
- [Hugging Face — BPE Tokenization Explained](https://huggingface.co/learn/llm-course/en/chapter6/5)
- [OpenAI Tiktoken Library (GitHub)](https://github.com/openai/tiktoken)
- [How Tokenizers Work in AI Models (Nebius)](https://nebius.com/blog/posts/how-tokenizers-work-in-ai-models)
- [Sebastian Raschka — Implementing BPE from Scratch](https://sebastianraschka.com/blog/2025/bpe-from-scratch.html)
- [Why LLMs Struggle to Count Letters (arXiv)](https://arxiv.org/html/2412.18626v1)
