---
title: "大模型会是什么样的司机？"
subtitle: "探究 GPT-4o、DeepSeek-V3 和 Llama 3.1 在四个日常驾驶场景、三种语言下如何做驾驶决策"
description: "三个 AI 模型、四个日常交通场景、三种语言、54,000 次驾驶决策。每个模型都有自己的“驾驶性格”，而提问所用的语言也会悄悄改变它。"
lang: zh
order: 1
featured: true
venue: "研究项目 · Journal of Intelligent and Connected Vehicles，2026"
tags: [大语言模型, 自动驾驶, 多语言 AI, 行为分析]
cover_image: /assets/images/portfolio/llm-driving-cover.svg
cover_alt: "三个仪表盘：GPT-4o 偏向谨慎，DeepSeek-V3 和 Llama 3.1 偏向激进"
permalink: /zh/portfolio/llm-driving-behavior/
links:
  - label: "阅读论文"
    url: "https://doi.org/10.26599/JICV.2026.9210095"
    icon: "fas fa-file-lines"
---

想象一个下雨的夜晚，你正在高速匝道口准备汇入。主路上的空档越来越小，一辆货车正快速驶来，车上的乘客还急着赶去医院。有的司机会一脚油门挤进去，有的会打灯等别人让出空间，还有的干脆让整列车先过。

现在，把同一个时刻交给 AI。**它会是哪一种司机？**

## 为什么问这个问题

大语言模型正在被尝试用作自动驾驶系统的“大脑”。它不负责转方向盘，而是决定下一步做什么：现在并道还是再等等，超过骑车人还是跟在后面。大多数评测只看模型选得是否合理，却很少有人问：**它是个什么样的司机？**

这一点很重要。模型从海量的人类文字中学习，在吸收知识的同时，也吸收了我们的习惯，包括我们有多大胆、多谨慎。两个模型可能都给出“合理”的答案，驾驶风格却截然不同。一旦其中一个被装进车里，它的风格也会一起上车。

所以，我们没有给模型打分，而是给它们做了一次**“性格画像”**。

## 我们做了什么

![研究设计：同一个驾驶情境分别用英文、中文和法文交给 GPT-4o、DeepSeek-V3 和 Llama 3.1，每个模型从激进到谨慎的选项中选一个动作并说明理由](/assets/images/portfolio/llm-driving-study-design.svg)

<div class="project-stats">
  <div class="project-stat"><span class="project-stat-value">4</span><span class="project-stat-label">个日常驾驶场景</span></div>
  <div class="project-stat"><span class="project-stat-value">1,500</span><span class="project-stat-label">每个场景的随机情境</span></div>
  <div class="project-stat"><span class="project-stat-value">3 × 3</span><span class="project-stat-label">模型 × 语言</span></div>
  <div class="project-stat"><span class="project-stat-value">54,000</span><span class="project-stat-label">次驾驶决策</span></div>
</div>

- **四个日常场景：**汇入高速、让匝道车辆汇入、跟随骑车人、通过人行横道。
- **每个场景 1,500 个情境，**从天气、昼夜、路面、周围交通、本车状态和乘客紧急程度的所有组合中随机抽取。
- **三个模型，三种语言：**GPT-4o、DeepSeek-V3 和 Llama 3.1 分别用英文、中文和法文接收每一个情境。
- **做选择题，而不是写作文：**每个模型从按“激进到谨慎”排序的选项中选出一个动作，再用一两句话解释理由。

之后我们从两个角度来看：**有序 Logit 模型**是一种分析排序选择的统计方法，能找出哪些因素会把模型推向谨慎或激进；对模型给出的理由做**主题分析**，则能看出它们在决策时关注什么。

<figure class="figure-wide">
  <a href="/assets/images/portfolio/study-pipeline.jpg" target="_blank" rel="noopener"><img src="/assets/images/portfolio/study-pipeline.jpg" alt="研究流程图：四个驾驶场景、构成每个情境的上下文因素、英文法文中文三套提示词、三个大语言模型选出动作并给出理由，以及有序 Logit 模型和主题分析"></a>
  <figcaption>论文中的完整流程图：四个场景、构成每个情境的因素、三套提示词、三个模型，以及两种分析方法。点击可放大。</figcaption>
</figure>

## 我们发现了什么

<div class="finding-grid">
  <div class="finding-card">
    <div class="finding-icon">🧠</div>
    <h3>每个模型都有自己的“驾驶性格”</h3>
    <p>GPT-4o 一贯求稳；DeepSeek-V3 和 Llama 3.1 明显更激进，尤其是在和其他车辆打交道时。</p>
  </div>
  <div class="finding-card">
    <div class="finding-icon">🌐</div>
    <h3>换一种语言，就换了一个司机</h3>
    <p>同样的情境、同样的选项，用中文或法文提问时，模型比用英文时更倾向于选择激进的动作，法文最明显。</p>
  </div>
  <div class="finding-card">
    <div class="finding-icon">🚸</div>
    <h3>遇到骑车人和行人，都会变谨慎</h3>
    <p>前方有骑车人、路口有行人时，每个模型都会更加小心。在人行横道前，几乎所有回答都一样：减速观察。</p>
  </div>
  <div class="finding-card">
    <div class="finding-icon">🚦</div>
    <h3>它们确实在“看路况”</h3>
    <p>乘客是否着急、交通有多繁忙、路上还有谁，都会改变模型的选择。它们并不是在随机作答。</p>
  </div>
</div>

## 为什么这很重要

- **选模型，就是选驾驶风格。**在让大模型替车辆做决策之前，它默认的“脾气”应该是一个有意识的设计选择，而不是意外。
- **语言是一个隐藏的参数。**面向多个国家部署的系统，提示词需要在每种语言下统一标准、分别测试，而不只是翻译一遍。
- **认清边界。**这项研究衡量的是模型在文字里“说”自己会怎么做，并不能说明它已经可以控制一辆真实的汽车。

<p style="font-size: 1.4em; font-style: italic; text-align: center; margin: 2rem 0;">每个模型都已经有了自己的驾驶风格——问题是，我们能否在它握住方向盘之前看清它。</p>

<p class="project-credit">本项目与 Wenjie Zhao、Qianwen Li 在佐治亚大学合作完成，发表于 <em>Journal of Intelligent and Connected Vehicles</em>（2026）。<br><a href="https://doi.org/10.26599/JICV.2026.9210095" target="_blank" rel="noopener">阅读论文</a></p>
