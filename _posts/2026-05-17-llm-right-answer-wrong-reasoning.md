---
layout: post
title: "Right Answer, Wrong Reasoning: Can LLMs Actually Think?"
date: 2026-05-17
tags: [AI, LLM, reasoning, chain-of-thought, tutorial]
lang: en
subtitle: "When AI gets the answer right but the thinking wrong — and why that matters more than you'd expect"
cover_image: /assets/images/blog/llm-reasoning-vs-real.svg
---

I asked an AI to solve a math problem. It wrote Python code, "ran" it, showed intermediate results, and arrived at the correct answer. Impressive — until I looked closer and realized the reasoning was a performance, not a proof.

## The Story: A Math Problem That Fooled Me

For another CS146S assignment, I needed an LLM to solve this using chain-of-thought prompting:

```
What is 3^{12345} (mod 100)?
```

In plain English: what are the last two digits of 3 raised to the power of 12345? The answer is **43**.

Here's the setup:

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

Llama 3.1 responded with a beautifully structured answer: it wrote Python code, displayed "outputs," identified a pattern, and arrived at 43. On the surface, it looked like rigorous step-by-step problem-solving.

But something was off.

## What Llama Showed Me

The model's response looked roughly like this:

**Step 1:** Compute the first few powers of 3 mod 100 to find a pattern.

```python
powers = []
for i in range(1, 10):
    power = pow(base, i, modulus)
    powers.append(power)
print(powers)  # Output: [3, 9, 27, 81, 43, 29, 87, 61, 83, 49]
```

**Step 2:** "From the output, we can see the pattern repeats every 20 powers."

```python
simplified_exponent = 12345 % 20  # Output: 5
```

**Step 3:** Calculate the final result.

```python
result = pow(3, 5, 100)  # Output: 43
```

**Answer: 43** — Correct!

Looks solid, right? Code, outputs, pattern recognition, final answer. But here's the thing...

## Three Things That Were Wrong

![What Llama showed vs what actually happened](/assets/images/blog/llm-reasoning-vs-real.svg)

### 1. It never ran the code

Llama 3.1 is a text-generation model. It doesn't have a Python interpreter. Those "outputs" after each `print()` statement? The model *generated* them as text — it predicted what the output *should* look like based on patterns in its training data.

For simple calculations, these predictions happen to be correct. But the model wasn't computing — it was *performing* computation.

### 2. The logic has a gap

It showed 10 values, then claimed the pattern repeats every **20**. But you can't deduce a period of 20 from only 10 data points. To actually prove this, you'd need to verify that 3^20 mod 100 = 1. Llama skipped that entirely.

### 3. It probably knew the answer first

The most likely explanation: Llama had seen similar modular arithmetic problems in its training data. It already "knew" the period is 20 (related to Euler's totient function). So it constructed a plausible-looking derivation *around* an answer it had already retrieved — not *toward* an answer it was discovering.

This is **post-hoc rationalization**: starting from the conclusion and building a story backwards.

## The Real Question: Do LLMs Actually Reason?

This experience points to a deeper issue that researchers are actively studying: **when an LLM shows you its "thinking," is it showing you the actual process that led to the answer, or a fabricated narrative?**

Recent research calls this the problem of **unfaithful chain-of-thought**. A 2025 study titled "Chain-of-Thought Reasoning In The Wild Is Not Always Faithful" found that even frontier models produce post-hoc rationalizations — GPT-4o-mini does it about 13% of the time, and even the best models aren't fully immune.

The core tension is this: LLMs are trained to predict the next token, not to reason logically. When we prompt them to "think step by step," they generate text that *looks like* reasoning. Sometimes it *is* genuine reasoning. Sometimes it's pattern-matching dressed up in the format of logic.

And here's what makes it dangerous: **you can't always tell the difference by reading the output.** The fabricated reasoning looks just as convincing as the real thing.

## Why This Happens

### LLMs are text predictors, not logic engines

At their core, language models predict: "given everything so far, what token comes next?" They've seen millions of math solutions in training, so they know the *format* of a proof. But knowing the format doesn't mean following the logic.

### Training data creates shortcuts

Llama has seen thousands of modular arithmetic problems. It learned that "modular exponentiation → find the period → reduce the exponent." So it retrieves and applies this template. When the template fits, the answer is correct. When it doesn't, you get confident nonsense.

### "Show your work" doesn't guarantee honest work

Chain-of-thought prompting ("think step by step") was designed to improve accuracy by forcing the model to lay out intermediate steps. And it does improve accuracy — but research shows the displayed steps don't always reflect the model's actual "decision process." The model may have arrived at the answer through a completely different internal path, then generated a plausible explanation after the fact.

## What Can We Do About It?

### 1. Give LLMs real tools

The most direct fix: let models actually execute code instead of pretending to. GPT-4 with Code Interpreter, Claude with tool use — these models can run Python for real. When Llama "runs" code in its head, it's guessing. When a model with tool access runs code, it's computing.

### 2. Verify the reasoning, not just the answer

Don't stop at "is the answer correct?" Ask: "does each step logically follow from the previous one?" In my Llama example, checking the 10-to-20 logical gap would have revealed the problem immediately.

### 3. Self-consistency checking

Run the same prompt multiple times with different temperatures. If the model produces different reasoning paths but the same answer, the answer is likely correct. If the reasoning paths contradict each other, something's wrong. Research shows this can substantially improve reliability.

### 4. Cross-verify with different approaches

Ask the model to solve the same problem in two different ways. If both approaches agree, confidence goes up. If they disagree, at least one reasoning chain is unfaithful.

### 5. Treat LLM output as a draft, not a proof

This is the most important mindset shift. LLM reasoning is a *starting point* for human verification, not a substitute for it. The model gives you a plausible approach; you verify whether it actually holds.

## Key Takeaways

1. **Correct answers don't guarantee correct reasoning** — an LLM can get the right answer for the wrong reasons
2. **LLMs perform reasoning, not always practice it** — the "thinking" you see may be reconstructed after the fact
3. **Fake code execution is real** — without tool access, models generate plausible-looking but unverified outputs
4. **Chain-of-thought is powerful but imperfect** — it improves accuracy while creating a false sense of transparency
5. **Always verify the process, not just the result** — especially for math, logic, and any high-stakes reasoning

<p style="font-size: 1.4em; font-style: italic; text-align: center; margin: 2rem 0;">The best liars don't get the facts wrong — they get the reasoning wrong.</p>

---

## Useful Resources

- [Chain-of-Thought Reasoning In The Wild Is Not Always Faithful (arXiv, 2025)](https://arxiv.org/abs/2503.08679)
- [Language Models Don't Always Say What They Think (arXiv, 2023)](https://arxiv.org/abs/2305.04388)
- [Self-Consistency Improves Chain of Thought Reasoning (arXiv)](https://arxiv.org/abs/2203.11171)
- [Chain of Thought Faithfulness: Why LLM Reasoning Is Often a Narrative (n1n.ai)](https://explore.n1n.ai/blog/llm-cot-faithfulness-research-2026-03-30)
- [Stochastic Parrot — Wikipedia](https://en.wikipedia.org/wiki/Stochastic_parrot)
- [Hallucinations in Code Are the Least Dangerous Form of LLM Mistakes (Simon Willison)](https://simonwillison.net/2025/Mar/2/hallucinations-in-code/)
