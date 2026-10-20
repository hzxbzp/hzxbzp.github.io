---
title: "The Better Plan Was Already There"
subtitle: "A driving language model often holds a better trajectory than the one it outputs. We sample a few and let a small checker pick."
description: "When a vision-language driving model plans badly, a good plan is often already among its own samples. We keep the model frozen, sample 16 trajectories, and train a small checker to pick one. Position error drops by about 15% across three models, with no retraining."
lang: en
order: 0.4
featured: true
venue: "Research · Under review, 2026"
tags: [Vision-Language Models, Autonomous Driving, Planning, Test-time Selection]
cover_image: /assets/images/portfolio/better-plan-banner.jpg
cover_alt: "Project banner: a driving view with several sampled trajectories, one highlighted, and a sample, verify, select diagram"
---

When a driving model produces a bad plan, there are two possible reasons. It never came up with a good one, or it came up with one and didn't pick it. The usual fixes, more data, more training, a bigger model, all assume the first. This project is about the second.

<figure class="figure-wide">
  <video class="project-video" controls muted loop playsinline preload="metadata" poster="/assets/images/portfolio/poster-plan-green-light.jpg">
    <source src="/assets/video/plan-green-light.mp4" type="video/mp4">
  </video>
</figure>

Green light, route straight ahead. The model's default plan waits, then turns right. The plan our checker picks goes straight, which is what the driver did.

How to read the videos: the left side is the front camera, the right side is the same moment from above. Red is the model's default plan, blue is the one we pick, grey lines are 16 other plans sampled from the same model, and the green dashed line is where the human driver actually went.

## Where it goes wrong

The clearest case is a car standing still that ought to pull away: stopped at a stop sign, or at a light that has just turned green.

We tested three vision-language planners of different sizes, Cosmos-Reason2 (2B), Qwen3-VL (8B) and Alpamayo-1.5 (10B), on the Waymo end-to-end driving benchmark. There are 82 validation frames where the car is stopped and the human driver goes on to move. The models fail to start in 41% to 84% of them. At stop signs, the smallest model stays put in all 12 cases.

## Why "wait" keeps winning

These models write out a trajectory one step at a time, and by default they take the single most likely next step each time. That's called greedy decoding.

Now look at what human drivers do at stop signs in the training data. Some go after 2 seconds, more after 3 or 4, some after 5. About a third don't go within 5 seconds at all. Two out of three do go, but the moment they go is spread over several seconds. So at each step, "keep waiting" is more likely than "go now". Take the most likely step five times in a row and the car never leaves.

<figure class="figure-wide">
  <img src="/assets/images/portfolio/better-plan-why.png" alt="Left: among human drivers stopped at stop signs, start times are spread over 2 to 5 seconds, with 32% staying, so greedy decoding picks wait at every step. Right: greedy decoding starts in 6 to 10% of the stopped frames, while 54 to 65% of 16 random samples from the same models start.">
</figure>

The right half of that figure is the important part. In those stopped frames, greedy decoding starts only 6–10% of the time. Draw 16 random samples from the very same models and 54–65% of them start. The moving plans were in there. The decoding just never chose them.

Picking by majority vote doesn't fix this. When the driver correctly stays put, 40–42% of the samples still start, so a consensus pick trades missed starts for false starts and the overall score goes down.

## What we did

We left the planner alone. For each frame it produces its usual greedy plan plus 16 sampled ones. A small network, two layers of 128 units, scores all 17 and picks one.

It looks at three things. How each plan moves: speed, acceleration, jerk, heading, when it starts. How it sits among the other candidates: how much the samples agree with it and how likely the model thinks it is. And what the car has been doing: its recent motion and the navigation command.

It's trained on logged driving to predict which candidate is closest to what a good driver did. At test time the top-scoring plan wins, but it only replaces the greedy plan if it wins by a clear margin. When the checker isn't sure, the default stays.

<figure class="figure-wide">
  <img src="/assets/images/portfolio/better-plan-method.png" alt="The frozen planner produces a greedy plan and 16 samples; a small trained network scores each from its motion, its agreement with the other samples and the car's recent motion; the highest-scoring plan replaces the greedy one only if it wins by a margin. During training the score is regressed onto a target built from the logged trajectory.">
</figure>

The planner gets no new data and no retraining. Scoring takes about 15 milliseconds. The extra cost is in generating the samples.

## What changed

<figure class="figure-wide">
  <img src="/assets/images/portfolio/better-plan-results.svg" alt="Average position error at 5 seconds, greedy versus our selection. Validation: Cosmos-Reason2 2.06 to 1.74 metres, Qwen3-VL 2.05 to 1.72, Alpamayo-1.5 1.79 to 1.52. Official test server, Alpamayo-1.5: 2.99 to 2.68.">
</figure>

On the validation set, average position error 5 seconds out drops by about 15% for all three models. The official test server can't be tuned against, so it's the stricter check: with the same Alpamayo checkpoint, error goes from 2.99 m to 2.68 m and the benchmark's human-rated score goes from 7.74 to 7.81. Among published methods that use the same three front cameras and no reinforcement-learning post-training, that's the lowest error at both 3 and 5 seconds.

The gains land where you'd expect. When the car should pull away from rest, error falls by 20–27%. When it should speed up, by 22–27%.

It also carries over. A checker trained only on the smallest model's samples works on the other two without retraining, at 1.75 m and 1.52 m against 1.72 m and 1.52 m for checkers trained on each. On a second benchmark, NAVSIM, with three planners released by other groups, 4-second error drops by 10–24%.

## Two more scenes

<div class="video-grid">
  <figure>
    <video controls muted loop playsinline preload="none" poster="/assets/images/portfolio/poster-plan-left-turn.jpg"><source src="/assets/video/plan-left-turn.mp4" type="video/mp4"></video>
    <figcaption>The default plan keeps waiting while the driver turns left. The picked plan turns left.</figcaption>
  </figure>
  <figure>
    <video controls muted loop playsinline preload="none" poster="/assets/images/portfolio/poster-plan-night-turn.jpg"><source src="/assets/video/plan-night-turn.mp4" type="video/mp4"></video>
    <figcaption>At night, red turns green. The picked plan starts the turn right away; the default waits about 1.6 s longer.</figcaption>
  </figure>
</div>

## Costs and limits

It's slower. On one H100, using 4 samples takes about 2.2–2.3 times as long as greedy decoding, and 16 samples about 4.1–4.7 times. For two of the three models, 4 samples already recover most of the gain.

It sometimes moves when it shouldn't. In frames where the right answer is to keep waiting, error rises from about 0.2 m to about 0.5 m, because now and then a creeping-forward plan gets picked.

It doesn't know the route. In one failure case it picks a reasonable straight start where the raters wanted the car to wait or turn left. Giving the checker the intended maneuver would help there.

And everything here is open-loop: each frame is scored against the recorded drive, not driven in simulation.

<p class="project-credit">Paper under review; the project name, code and paper links are withheld until the review period ends.</p>
