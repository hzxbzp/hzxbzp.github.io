---
title: "What Is the Car Doing?"
subtitle: "EgoManeuver relabels seven public driving datasets with one vocabulary of 22 ego-vehicle actions, and DenseEgo learns to recognize them from video alone"
description: "Seven public driving datasets, 190.8 hours from 26 countries, relabeled into one set of 16 lateral and 6 longitudinal actions. A video model trained on it reaches 82.7 and 83.5 mAP, and powers an agent that finds driving clips from a plain-English request."
lang: en
order: 0.3
featured: true
venue: "Research · Under review, 2026"
tags: [Benchmark, Video Understanding, Autonomous Driving, Action Recognition]
cover_image: /assets/images/portfolio/egomaneuver-banner.jpg
cover_alt: "EgoManeuver banner: 7 datasets, 86.6K segments, 16 lateral and 6 longitudinal actions, with an icon for each action"
---

There is an enormous amount of driving video around, from fleets, dashcams and online uploads. Almost none of it says what the car filming it was doing. Was it changing lanes, merging, taking an exit, edging around a parked truck? That label is what you need to find the rare, difficult moments in a large collection, to test a driving model on them, or to train models that learn to drive from video.

EgoManeuver is a benchmark for reading that off the video alone. It records nothing new. It takes seven public datasets that each describe the ego vehicle's motion in their own way, and relabels all of them with one shared vocabulary.

<figure class="figure-wide">
  <video class="project-video" controls playsinline preload="metadata" poster="/assets/images/portfolio/poster-egomaneuver.jpg">
    <source src="/assets/video/egomaneuver.mp4" type="video/mp4">
  </video>
</figure>

## Same motion, different maneuver

The hard part is that many actions look alike if you only watch how the car moves. A lane change, a merge and a branch are all a small sideways drift. What separates them is the road. A lane change moves into a neighboring lane that continues. A merge leaves a lane that is ending, or joins another road. A branch peels off into a lane that splits away, like a turn pocket or an exit. In the same way, a nudge shifts within the lane to clear an obstacle, while a borrow crosses briefly into the next or oncoming lane and comes back.

Existing datasets don't agree on these. HDD has 11 goal-oriented actions, ROAD and ROAD-Waymo use a handful of coarse ones, and others describe actions in free text. So the benchmark defines 16 lateral actions and 6 longitudinal ones, and every segment gets one of each:

- **Lateral:** straight, curve, turn left / right, U-turn, lane change left / right, merge left / right, branch left / right, nudge left / right, borrow left, pull over, pull out.
- **Longitudinal:** stationary, start, stop, accelerate, decelerate, cruise.

<figure class="figure-wide">
  <img src="/assets/images/portfolio/egomaneuver-actions.jpg" alt="Example frames for six of the harder actions: merge left, nudge left, borrow left, pull over, pull out and U-turn">
</figure>

## Building it from seven datasets

The sources are HDD, Argoverse 2, ROAD-Waymo, PhysicalAI-AV, BDD-X, nuScenes and nuPlan. Each brings a different kind of evidence. Some have human action labels, some have HD maps and precise poses, BDD-X has written captions, and PhysicalAI-AV has poses only. The label system turns all of it into the same kind of answer in four steps.

<figure class="figure-wide">
  <img src="/assets/images/portfolio/egomaneuver-overview.png" alt="Overview: seven datasets totalling 190.8 hours from 26 countries; the label system reconstructs motion, labels actions from each kind of evidence, aligns action boundaries, builds segments and runs quality control; the result is 16 lateral and 6 longitudinal actions with a long-tail-aware evaluation">
</figure>

First it reconstructs the car's speed, acceleration, yaw rate and curvature at 10 Hz from whatever the dataset provides. Then it labels actions with a rule suited to each kind of evidence. Human labels are mapped onto the new vocabulary. Where there is an HD map, the car's path is matched to the lane graph, which is what tells lane changes, merges and branches apart. Captions are matched to text patterns. Where the evidence can't settle the question, the segment is left unlabeled rather than guessed.

Next it aligns time. Sources disagree on when a maneuver starts and ends, by up to about 3 seconds for lane changes, so only the core of each action is kept and the uncertain edges are marked as ignored. Last, it cuts the drives into segments.

Quality control runs on top. Every conversion rule was checked against samples. Automatic checks use the yaw rate to confirm that the heading change matches the label, and the video to confirm the car really moves when the label says it does; together they removed 1,747 segments. Nudge versus borrow, which the signals can't separate, was reviewed by hand from the video.

The result is 86.6K segments: 190.8 hours from 26 countries and 7 camera setups. It is very long-tailed, with 45,060 segments of driving straight against 46 of merging right and 10 of nudging right. So the main score, mAP, only counts actions with at least 20 test examples, and the rarest ones are reported as plain hit counts with exact confidence intervals rather than averaged in. Recognition models only ever see the front camera.

## DenseEgo

<figure class="figure-wide">
  <img src="/assets/images/portfolio/egomaneuver-model.png" alt="DenseEgo in two stages: first a V-JEPA 2 encoder is fine-tuned with a spatial readout and temporal transformer to predict actions and ego kinematics at every step; then the encoder is frozen and a segment-level head with a bidirectional transformer and attention pooling predicts the lateral and longitudinal action">
</figure>

The model is built on V-JEPA 2, a self-supervised video encoder, and differs from a plain classifier in two ways.

It keeps spatial detail. Telling a merge from a lane change depends on where the lanes are, so instead of averaging each frame into a single vector, it keeps a grid of features plus a global one and lets a learned query pick out what matters.

It learns from dense labels. The label system gives an action for every moment of every training drive, not just one per segment. In the first stage the encoder is fine-tuned to predict the action at each step, along with the car's own motion. In the second, the encoder is frozen and a small head learns to classify whole segments. At test time it needs nothing but the video.

## Results

<figure class="figure-wide">
  <img src="/assets/images/portfolio/egomaneuver-results.svg" alt="Test-split mAP with 95% confidence intervals. Lateral: DenseEgo 82.7, VideoMAE-B 81.2, SlowFast-R50 77.1, I3D-R50 72.9, TimeSformer-B 72.3, X3D-M 68.7, TSN-R50 48.9. Longitudinal: DenseEgo 83.5, VideoMAE-B 78.5, SlowFast-R50 76.9, X3D-M 67.8, I3D-R50 65.6, TimeSformer-B 46.9, TSN-R50 31.9.">
</figure>

On the test split DenseEgo scores 82.7 mAP on lateral actions and 83.5 on longitudinal ones. The strongest existing model, VideoMAE, gets 81.2 and 78.5. The lateral difference is within the margin of error. The longitudinal one holds up: compared on the same test resamples, the gain is 5.0 points, with a 95% interval of 2.6 to 7.5. DenseEgo also has the lowest false-alarm rate on straight driving, 0.9%.

What it learns carries over to benchmarks with different action definitions. With a new head on the frozen encoder, it reaches 51.3 frame-level mAP on HDD, above the best published video-only methods at 49.1, and 63.8 on ROAD-Waymo, 11.1 points above the dataset's own strongest baseline.

The ablations show where that comes from. Fine-tuning the encoder adds 5.0 lateral and 2.5 longitudinal points over keeping it frozen. Training on the per-step labels rather than segment labels alone lifts rare-class accuracy on validation from 31.6% to 40.3%. Without class-balanced sampling the averages rise, but rare-class accuracy drops to 28.2%, so the balanced version stays.

## Finding clips by asking

<figure class="figure-wide">
  <img src="/assets/images/portfolio/egomaneuver-agent.jpg" alt="Three requests to the retrieval agent: a left lane change on a highway, waiting at a traffic light and then turning left, and pulling over to the side of the road; for each, the predicted action timeline with the retrieved interval outlined">
</figure>

Because DenseEgo labels every moment of a video, it can do the perception for search. An agent takes a request in plain English, works out which actions and context it describes, and looks for matching stretches in the predicted action timeline. It handles single actions, like a left lane change on a highway or pulling over, and ordered sequences, like waiting at a light, starting, and then turning left.

## Where it still struggles

The rare maneuvers that move like common ones. 34% of branch-right segments are taken for a lane change right, and 44% of borrow-left segments for a nudge left. Seven lateral classes have fewer than 20 test examples, too few for a stable average precision, which is why they are reported separately. More examples of these actions are what the benchmark needs next.

<p class="project-credit">Paper under review.</p>
