---
title: "What Kind of Driver Is an LLM?"
subtitle: "Probing how GPT-4o, DeepSeek-V3 and Llama 3.1 make driving decisions across four everyday scenarios and three languages"
description: "Three AI models, four everyday traffic scenarios, three languages, 54,000 decisions. Each model turned out to have its own driving personality, and the language you ask in quietly changes it."
lang: en
order: 1
featured: true
venue: "Research · Journal of Intelligent and Connected Vehicles, 2026"
tags: [LLMs, Autonomous Driving, Multilingual AI, Behavior Analysis]
cover_image: /assets/images/portfolio/llm-driving-cover.svg
cover_alt: "Three gauges: GPT-4o leans cautious, DeepSeek-V3 and Llama 3.1 lean assertive"
links:
  - label: "Read the paper"
    url: "https://doi.org/10.26599/JICV.2026.9210095"
    icon: "fas fa-file-lines"
---

Picture a freeway on-ramp on a rainy night. The gap in traffic is closing, a truck is coming up fast, and your passenger needs to get to the hospital. One driver floors it into the gap. Another signals and waits for someone to make room. A third lets the whole line go by.

Now hand that same moment to an AI. **What kind of driver is it?**

## Why this question

Large language models are being tried out as the "brain" of self-driving systems. They don't turn the wheel; they decide *what* to do next: merge now or wait, pass the cyclist or stay behind. Most evaluations check whether the model picks a sensible action. Very few ask **what kind of driver** it is.

That gap matters. A model learns from an enormous amount of human writing, and it picks up our habits along with our knowledge, including how bold or careful we tend to be. Two models can both give "reasonable" answers and still drive in completely different styles. Put one of them in a car, and its style comes along for the ride.

So instead of grading the models, we set out to **profile** them.

## What we did

![How we probed each model: a driving situation is described in English, Chinese or French to GPT-4o, DeepSeek-V3 and Llama 3.1, and each picks a move from assertive to cautious and explains why](/assets/images/portfolio/llm-driving-study-design.svg)

<div class="project-stats">
  <div class="project-stat"><span class="project-stat-value">4</span><span class="project-stat-label">everyday driving scenarios</span></div>
  <div class="project-stat"><span class="project-stat-value">1,500</span><span class="project-stat-label">random situations per scenario</span></div>
  <div class="project-stat"><span class="project-stat-value">3 × 3</span><span class="project-stat-label">models × languages</span></div>
  <div class="project-stat"><span class="project-stat-value">54,000</span><span class="project-stat-label">driving decisions</span></div>
</div>

- **Four everyday scenarios:** merging onto a freeway, letting another car merge in, following a cyclist, and approaching a pedestrian crossing.
- **1,500 situations for each,** drawn at random from every combination of weather, time of day, road surface, surrounding traffic, the car's own state, and how urgent the passenger is.
- **Three models, three languages:** GPT-4o, DeepSeek-V3 and Llama 3.1 each received every situation in English, Chinese and French.
- **A menu, not an essay:** each model picked one move from options ranked from assertive to cautious, then explained its choice in a sentence or two.

We then looked through two lenses. An **ordered logit model**, a statistical tool for ranked choices, shows which factors push a model toward caution or toward assertiveness. A **thematic analysis** of the explanations shows what each model pays attention to when it decides.

<figure class="figure-wide">
  <a href="/assets/images/portfolio/study-pipeline.jpg" target="_blank" rel="noopener"><img src="/assets/images/portfolio/study-pipeline.jpg" alt="The study pipeline: four driving scenarios, the contextual factors that make up each situation, prompt sets in English, French and Chinese, three large language models choosing an option and giving a justification, and the ordered logit and thematic analyses"></a>
  <figcaption>The full pipeline from the paper: the four scenarios, the factors behind every situation, the three prompt sets, the three models, and the two analyses. Click to enlarge.</figcaption>
</figure>

## What we found

<div class="finding-grid">
  <div class="finding-card">
    <div class="finding-icon">🧠</div>
    <h3>Each model has a driving personality</h3>
    <p>GPT-4o consistently plays it safe. DeepSeek-V3 and Llama 3.1 are noticeably more assertive, especially when the other road user is a car.</p>
  </div>
  <div class="finding-card">
    <div class="finding-icon">🌐</div>
    <h3>The language changes the driver</h3>
    <p>Same situation, same options. Asked in Chinese or French, the models chose more assertive moves than in English, and French pushed them the furthest.</p>
  </div>
  <div class="finding-card">
    <div class="finding-icon">🚸</div>
    <h3>Cyclists and pedestrians bring out caution</h3>
    <p>With a cyclist ahead or a pedestrian at the crossing, every model became more careful. At the crossing, almost every answer was the same: slow down and watch.</p>
  </div>
  <div class="finding-card">
    <div class="finding-icon">🚦</div>
    <h3>They really do read the situation</h3>
    <p>An urgent passenger, heavier traffic and who else is on the road all shifted the choices. The models are not answering at random.</p>
  </div>
</div>

## Why it matters

- **Choosing a model is choosing a driving style.** Before an LLM makes decisions for a vehicle, its default temperament should be a design decision, not a surprise.
- **Language is a hidden setting.** A system that ships in several countries needs its prompts standardised and tested in every language, not just translated.
- **Know the limits.** This study measures what models *say* they would do, in text. It does not show that they are ready to control a real car.

<p style="font-size: 1.4em; font-style: italic; text-align: center; margin: 2rem 0;">Every model already has a driving style — the question is whether we notice it before it takes the wheel.</p>

<p class="project-credit">Joint work with Wenjie Zhao and Qianwen Li at the University of Georgia, published in the <em>Journal of Intelligent and Connected Vehicles</em> (2026).<br><a href="https://doi.org/10.26599/JICV.2026.9210095" target="_blank" rel="noopener">Read the paper</a></p>
