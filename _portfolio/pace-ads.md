---
title: "Your Ride, Your Rules"
subtitle: "PACE-ADS: three AI agents that let a self-driving car read the road, read the person sitting in it, and drive in a way that suits them"
description: "Self-driving cars react to traffic but treat the rider as cargo. PACE-ADS adds three agents — one watching the road, one watching the rider, one deciding — so the same junction can be taken briskly or gently, and a stuck car can be freed by a sentence."
lang: en
order: 0
featured: true
venue: "Research · Journal of Intelligent and Connected Vehicles, 2026"
tags: [LLM Agents, Human-Centered Autonomy, Autonomous Driving, CARLA]
cover_image: /assets/images/portfolio/pace-ads-cover.svg
cover_alt: "Two speed traces through the same red light: a hurried rider gets late firm braking and a brisk restart, an anxious rider gets early gentle braking and a slow restart"
links:
  - label: "Read the preprint"
    url: "https://arxiv.org/abs/2506.11842"
    icon: "fas fa-file-lines"
---

Two people get into the same robotaxi on different days. One is late for a flight. The other is a nervous first-time rider. The car brakes for a red light exactly the same way for both — and gets it wrong twice. Too slow for one, too sharp for the other.

The car has no idea who is sitting in it.

## Why this matters

Today's self-driving systems are built to watch the road. Everything they sense points outward: traffic lights, other cars, pedestrians. The person in the back seat contributes nothing to any decision.

<figure class="figure-wide">
  <img src="/assets/images/portfolio/pace-ads-background.svg" alt="Today's car reacts to traffic but not to the person inside, so every rider gets the same ride and nobody can help when the car is stuck; what is missing is a car that senses the rider and can be told things">
</figure>

That costs two things. **The ride fits nobody in particular** — a single driving style, applied to everyone. And when the car gets confused and stops, **the rider has no way to help**, even when they can plainly see the obstacle is a paper bag.

Both problems have the same root: there is no channel from the person to the driving.

## What we built

**PACE-ADS** opens that channel. Three language-model agents sit beside the car's existing software — none of it is modified — and between them they turn "who is in the car and how are they doing" into an actual driving decision.

<figure class="figure-wide">
  <img src="/assets/images/portfolio/pace-ads-system.svg" alt="Three agents beside the car's own software: a driver agent reads the road, a psychologist agent reads the rider's signals and words, and a coordinator turns both into a driving behaviour handed back to the planner">
</figure>

<div class="project-stats">
  <div class="project-stat"><span class="project-stat-value">3</span><span class="project-stat-label">agents: road, rider, decision</span></div>
  <div class="project-stat"><span class="project-stat-value">0</span><span class="project-stat-label">training — all of it runs zero-shot</span></div>
  <div class="project-stat"><span class="project-stat-value">4</span><span class="project-stat-label">everyday scenarios tested</span></div>
  <div class="project-stat"><span class="project-stat-value">30</span><span class="project-stat-label">repeat runs behind every number</span></div>
</div>

- **The driver agent reads the road.** Not just "there is an object at 23 metres", but what kind of scene this is: weather, work zones, signal state, who is around and what they look about to do.
- **The psychologist agent reads the rider.** It takes raw signals — facial expression, heart rate, EEG — plus anything the rider says out loud, and works out whether they are calm, anxious, or in a hurry, and what they are asking for. There is no separate emotion classifier in front of it; the agent reasons from the raw signals itself.
- **The coordinator decides.** Safety first, then the rider. It picks one of the moves the car already knows how to make — follow, keep lane, change lane, stop — and sets how briskly to do it.

Three design choices keep it honest:

- **Personalization is bounded.** Every setting the coordinator chooses lives inside a fixed safe range tied to traffic law. A rider in a hurry can buy a brisker ride; they cannot buy a shorter gap than the rules allow.
- **It stays out of the way.** It works at the slow, high-level layer and only wakes when the rider's state changes, the rider says something, or the car gets stuck. Real-time control never leaves the car's own software.
- **It asks when it is unsure.** Rather than guessing, the coordinator explains what is holding the car up and puts the question to the rider.

## Does the ride actually change?

We ran four everyday scenarios in CARLA — a red light, a pedestrian crossing, a work zone requiring a lane change, and ordinary car-following — under five rider states, repeating each one 30 times.

<figure class="figure-wide">
  <img src="/assets/images/portfolio/pace-ads-results-personalization.svg" alt="Across five rider states the car cruises slower, stops further from pedestrians and waits for larger gaps as the rider becomes more anxious; the pedestrian clearance never drops below the 1.5 metre safety floor">
</figure>

The pattern holds across all of it. A hurried rider gets a car that cruises near the limit, brakes late and firmly, and pulls away quickly. An anxious rider gets one that cruises at half the speed, starts braking far earlier, and leaves a much wider margin around a pedestrian. And the safety floor holds: even at its most assertive, the car never came closer to a pedestrian than the 1.5 m it is required to keep.

The clearest picture is the work zone, where the car has to merge left past a closed lane:

<figure class="figure-wide" style="max-width: 720px;">
  <a href="/assets/images/portfolio/pace-ads-gap.jpg" target="_blank" rel="noopener"><img src="/assets/images/portfolio/pace-ads-gap.jpg" alt="Two aerial views of the same work zone: with an impatient rider the car merges into a 1.6 metre gap; with an anxious rider it waits for more than 20 metres of clear road"></a>
  <figcaption>Same work zone, same rules, two riders. On the left the car takes the first gap it can legally use. On the right it lets the whole queue go by. Click to enlarge.</figcaption>
</figure>

## And when the car gets stuck

The second half of the system is about recovery. We built three situations a conventional stack cannot talk its way out of: paper bags in the lane, a road fully closed by barricades, and a route planner that keeps looping a roundabout — the failure that stranded a Waymo robotaxi for 17 laps in San Francisco.

<figure class="figure-wide">
  <img src="/assets/images/portfolio/pace-ads-results-recovery.svg" alt="The baseline agent recovers in none of 90 runs; StuckSolver recovers in 83.3 percent and PACE-ADS in 91.1 percent, with gains on the roundabout and road closure and a drop on static obstacles caused by refusals">
</figure>

The baseline never gets out — not once in 90 runs. PACE-ADS recovers in 91% of them, and most of the gain comes from the two scenarios that need someone to actually interpret the situation rather than just detect objects.

<figure class="figure-wide">
  <a href="/assets/images/portfolio/pace-ads-roundabout.jpg" target="_blank" rel="noopener"><img src="/assets/images/portfolio/pace-ads-roundabout.jpg" alt="The car's trajectory loops the roundabout repeatedly until the rider says stop and exit on the right, after which the coordinator breaks the instruction into stop, lane change and cruise, replans the route and exits"></a>
  <figcaption>The roundabout. The car circles until the rider says: "Stop! Exit the roundabout from the right exit." The coordinator breaks that sentence into stop → change lane → cruise, picks a new waypoint, and the car leaves. Click to enlarge.</figcaption>
</figure>

One result runs the other way and is worth keeping in view. On the paper-bag scenario PACE-ADS scores *lower* than our earlier system, because it sometimes refuses the rider's instruction to drive over the bags after checking it against what the driver agent sees. Those are refusals, not failures — but the same mechanism that blocks an unsafe instruction will occasionally block a safe one.

## Can it read a stranger?

All the driving tests used signals from one person. To see whether that transfers, we ran the psychologist agent over all 43 participants in the dataset without adapting it to anyone: **64% accuracy** at naming the rider's state.

Far from perfect — but the shape of the errors matters more than the number. Nearly half the mistakes are between neighbouring intensities of the same feeling (reading "very anxious" as "anxious"), and fewer than 5% flip a hurried rider into an anxious one. So a misread usually makes the adaptation *smaller* than it should be, not backwards. Dropping EEG entirely costs only 3.7 points, which matters for a system that has to run in a real car.

## What it does not solve

- **It is all simulation.** CARLA gives clean perception and perfect actuation. A real car has neither.
- **The rider's feelings were scripted.** We injected emotional signals on a schedule rather than measuring a real person reacting to the car. So these runs show the system responds coherently to a given state — not that a real passenger ends up feeling better.
- **Five states are a tool, not a truth.** Feelings are continuous; "very anxious" and "anxious" are labels we chose to make the behaviour measurable and discussable.
- **The safety check is reasoning, not a guarantee.** The coordinator weighs a rider's instruction against the scene, but that is a judgement, not a proof. Rider guidance is information the system considers — not an order it obeys.
- **2.2 seconds per decision.** Fine at the slow layer, and it currently needs a remote API. Getting it on-board is the next piece of work.

<p style="font-size: 1.4em; font-style: italic; text-align: center; margin: 2rem 0;">A car that cannot tell a calm rider from a frightened one is not fully autonomous — it is just alone.</p>

<p class="project-credit">Joint work with Wenjie Zhao and Qianwen Li at the University of Georgia. Accepted by the <em>Journal of Intelligent and Connected Vehicles</em> (2026); the journal version is not online yet.<br><a href="https://arxiv.org/abs/2506.11842" target="_blank" rel="noopener">Read the preprint on arXiv</a></p>
