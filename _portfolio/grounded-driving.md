---
title: "Point First, Then Decide"
subtitle: "A dataset and training recipe that make a driving model name the few things on the road that actually decide its next move — and show where they are"
description: "Driving models describe a scene fluently and still cannot tell you which three things on the road are deciding what they do next. This project builds the supervision that makes them point first and plan second, and it makes eight different models better at both."
lang: en
order: 0.5
featured: true
venue: "Research · Under review, 2026"
cover_image: /assets/images/portfolio/grounded-cover.jpg
cover_alt: "A frame from the dataset: a traffic signal and three cyclists marked as the elements that decide the car's next move"
tags: [Vision-Language Models, Autonomous Driving, Dataset, Grounding]
---

Ask a vision-language model what it sees from a car's front camera and you get a fluent paragraph. Ask it which three things in that paragraph are actually deciding what the car does next, and where exactly they are, and the answer falls apart.

This project is about closing that gap: supervision that makes a model point at the evidence before it explains itself.

<figure class="figure-wide">
  <video class="project-video" controls muted loop playsinline preload="metadata" poster="/assets/images/portfolio/grounded-cover.jpg">
    <source src="/assets/video/scene-cyclists.mp4" type="video/mp4">
  </video>
</figure>

## The gap in the training data

Driving datasets for these models come in three shapes, and each one leaves something out. Scene captions describe the whole picture but never say which object mattered. Question-and-answer sets teach a model to answer one question at a time, with no thread running from what it saw to what it did. Written chains of reasoning read well but float free of the image — the model can mention a pedestrian that was never there, and nothing in the data catches it.

What is missing is the link in the middle: *this* object, at *these* pixels, is why the car slows down.

## What the dataset contains

The source footage is Waymo's end-to-end driving set, which deliberately collects the awkward situations — the ones that are rare but decide whether a drive goes well. From 2,492 clips and roughly eleven hours of driving we annotated **416,119 frames** and **395,379 decision-critical elements**, grouped into four families: other vehicles, vulnerable road users, obstacles, and traffic-control devices, with nineteen finer types underneath.

Each frame carries a chain rather than a label. First the setting: weather, visibility, road layout. Then at most five elements that genuinely matter, each with a mask marking exactly where it is, a rank from 1 to 5 for how much it matters, and its state and apparent intention. Then, for each one, a sentence on what it does to the car's options. Then one short rationale tying them together. And finally the decision: a high-level action, plus the trajectory the car should follow over the next five seconds.

The annotation was built by people and machines in turn. Experienced drivers choose and rank the elements that matter and click on them; a segmentation model turns those clicks into masks; a vision-language model fills in object types and reads sign content; language models draft the per-element implications and the frame rationale; rule checks then verify that the stated action and the drawn trajectory actually agree. Half the scene-context annotations were reviewed by hand, element choices were cross-checked between annotators with a third breaking ties, and the automatic steps were spot-checked against human labels — traffic-light state agreed 97.8% of the time, vehicle type 99.0%.

## More scenes from the set

<div class="video-grid">
  <figure>
    <video controls muted loop playsinline preload="none" poster="/assets/images/portfolio/poster-scene-red-light.jpg"><source src="/assets/video/scene-red-light.mp4" type="video/mp4"></video>
    <figcaption>Fire engines pulling out of a station</figcaption>
  </figure>
  <figure>
    <video controls muted loop playsinline preload="none" poster="/assets/images/portfolio/poster-scene-rain.jpg"><source src="/assets/video/scene-rain.mp4" type="video/mp4"></video>
    <figcaption>A wet residential street at dusk</figcaption>
  </figure>
  <figure>
    <video controls muted loop playsinline preload="none" poster="/assets/images/portfolio/poster-scene-school-bus.jpg"><source src="/assets/video/scene-school-bus.mp4" type="video/mp4"></video>
    <figcaption>Passing a parked school bus</figcaption>
  </figure>
  <figure>
    <video controls muted loop playsinline preload="none" poster="/assets/images/portfolio/poster-scene-pull-over.jpg"><source src="/assets/video/scene-pull-over.mp4" type="video/mp4"></video>
    <figcaption>Overtaking a bus on a steep street</figcaption>
  </figure>
  <figure>
    <video controls muted loop playsinline preload="none" poster="/assets/images/portfolio/poster-scene-animal.jpg"><source src="/assets/video/scene-animal.mp4" type="video/mp4"></video>
    <figcaption>Cresting a steep city hill</figcaption>
  </figure>
</div>

## Learning it in two passes

Training a model on the whole chain at once does not work well; the hard parts drown out the easy ones. So the model learns in order.

In the first pass it only has to see: read the setting, decide which elements matter, point to them, and rank them. Everything downstream is hidden from the loss, and the visual encoder is free to adapt.

In the second pass it learns to reason and to plan — the implications, the rationale, the action, the trajectory — and this time the visual encoder is frozen, so the pointing it just learned does not quietly erode while it practises writing. Frames with rare behaviour, like a change from accelerating to braking, are sampled more often, because those are the ones the model would otherwise never see enough of.

Scoring how well a model points needs care, because "close enough" means something different for a traffic light fifty metres away than for a bus filling half the frame. The measure used here scales its tolerance to the size of the object: a prediction counts as a hit if it lands inside the object's mask, or within a distance that grows with the object's own footprint.

## What it changed

Eight open vision-language models were fine-tuned this way, ranging from general-purpose ones to models built specifically for driving. The direction is the same in all of them.

Finding the elements that matter improved the most: **recall went from between 0.10 and 0.49 up to between 0.62 and 0.79**, and the type of each element was named correctly **90–98%** of the time. Explanations judged for quality rose by about **0.36 on a 0–1 scale**, and the driving rationale by about **0.31**.

The planning improved with it. Averaged across models, the five-second trajectory error dropped by roughly **7.8 metres**, and the error at the final point by about **11.9 metres** — on several backbones that is the difference between a path that is vaguely in the right direction and one that is usable.

The result we did not expect: outputs got **shorter**, by about 18.5 tokens, and inference got **faster**, by about 0.32 seconds per frame. Teaching a model what to look at seems to stop it padding.

## Where the limits are

Most of the annotation text was drafted by language models and then reviewed, not written from scratch by people, which is what made the scale possible and is also the honest caveat about it. The grounding measure is new, so numbers from it are not comparable to anything published before. And everything here is offline evaluation on recorded driving: better grounding and lower trajectory error are not the same thing as a car that drives better, and closing that gap needs a vehicle in the loop.

<p class="project-credit">Paper under review; the project name, code and data links are withheld until the review period ends.</p>
