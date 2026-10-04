---
title: "Getting a Stuck Self-Driving Car Moving Again"
subtitle: "StuckSolver: a language-model add-on that works out why an autonomous vehicle is stuck and proposes a legal way out — or acts on one sentence from the passenger"
description: "Self-driving cars sometimes stop for a plastic bag and wait for a human to rescue them. We bolted a language model onto a standard AV stack so the car can reason its way out. Driving score 48.7 → 70.9, success rate 18% → 50%."
lang: en
order: 2
featured: true
venue: "Research · IEEE Intelligent Vehicles Symposium (IV), 2026"
tags: [LLMs, Autonomous Driving, CARLA, Human-in-the-loop]
cover_image: /assets/images/portfolio/stucksolver-cover.svg
cover_alt: "A speed trace: the car drops to a standstill, StuckSolver steps in, and the car returns to speed"
links:
  - label: "Read the paper"
    url: "https://ieeexplore.ieee.org/document/11623937"
    icon: "fas fa-file-lines"
---

In February 2024, a Waymo robotaxi in San Francisco drove around a roundabout **17 times** before engineers rescued it remotely. Nothing was broken. The car simply could not find a move it was allowed to make.

That is the problem this project is about.

## Why this matters

A self-driving car can handle most of a trip. What it handles badly is the rare moment when the road stops making sense: a plastic bag in the lane, a broken-down car ahead, barricades across both lanes. The planner looks for a legal path, finds none, and does the safest thing it knows — it stops. And then it keeps stopping.

<figure class="figure-wide">
  <img src="/assets/images/portfolio/stucksolver-background.svg" alt="Why a stuck car is a hard problem: it stops for a trivial obstacle; today it can only call a remote operator or ask the rider to drive; what is missing is a way out from inside the car">
  <figcaption>The gap this project fills: a car stopped by something trivial, and no good way out from inside it.</figcaption>
</figure>

Today there are two ways out, and both have a hole in them:

- **Call a remote operator.** Someone, somewhere, takes over. It works, but it costs money to keep people on standby, and the car waits while one is found.
- **Let the passenger drive.** Only possible if the passenger *can* drive — which excludes the elderly, people with disabilities, and anyone without a licence. These are exactly the people self-driving cars are supposed to serve.

What is missing is a third option: the car gets itself out, or the passenger helps with a sentence instead of the steering wheel.

## What we built

**StuckSolver** is a language model (GPT-4o) wired into an existing self-driving stack as an **add-on**. Nothing inside the car's own perception, planning or control code changes. It reads what the car already knows and hands back a suggestion — and only when the car is actually stuck.

<figure class="figure-wide">
  <img src="/assets/images/portfolio/stucksolver-system.svg" alt="StuckSolver sits beside the car's own pipeline: it reads the camera image, nearby objects, the car's own state, the map and the list of allowed moves, reasons in three steps, and hands a behaviour plan back to planning">
  <figcaption>How it connects. The car's own modules are untouched; StuckSolver reads what they already produce and hands one decision back.</figcaption>
</figure>

<div class="project-stats">
  <div class="project-stat"><span class="project-stat-value">3</span><span class="project-stat-label">reasoning steps: look, diagnose, decide</span></div>
  <div class="project-stat"><span class="project-stat-value">0</span><span class="project-stat-label">training — it runs zero-shot</span></div>
  <div class="project-stat"><span class="project-stat-value">5<small>km/h</small></span><span class="project-stat-label">below this for 1 second, it wakes up</span></div>
  <div class="project-stat"><span class="project-stat-value">220</span><span class="project-stat-label">closed-loop routes it was tested on</span></div>
</div>

The reasoning runs in three plain steps:

1. **Look.** From the front camera and the object list: what is ahead, how far, how fast, and what the traffic lights and signs say.
2. **Diagnose.** Is the car genuinely stuck, or just correctly waiting at a red light with pedestrians crossing? If it is stuck, why?
3. **Decide.** Choose one move the car is *already allowed* to make — stop, keep lane, follow, change lane — plus a point to restart the route from if a detour is needed.

Two design choices did most of the work:

- **It stays out of the way.** It is silent while the car drives normally and wakes only after the car has been under 5 km/h for more than a second. So it costs nothing in the 99% of driving that is fine, and it never fights the planner.
- **It only picks from legal moves.** The model does not invent a trajectory. It chooses from the behaviours the car's own planner currently offers, so whatever it suggests, the car already knows how to execute safely.

And because it speaks language, the passenger can join in: *"It's just a bag — you can drive over it."* StuckSolver turns that sentence into a lane change the car can actually perform. No driving licence required.

## Does it work?

We tested it in CARLA on **Bench2Drive**, a benchmark of 220 routes, each containing one hard corner case. Two numbers matter: **Driving Score** (how well the route was driven, counting violations) and **Success Rate** (how often the route was finished on time and clean).

<figure class="figure-wide">
  <img src="/assets/images/portfolio/stucksolver-results.svg" alt="Driving Score rises from 48.7 to 65.2, and to 70.9 with passenger guidance; Success Rate rises from 18.2% to 36.3%, and to 50.0%, matching the best end-to-end model">
  <figcaption>Results on Bench2Drive. Both charts are higher-is-better; the dashed line is the strongest end-to-end model reported on the same benchmark.</figcaption>
</figure>

Reading it in plain terms:

- Bolting StuckSolver onto a plain rule-based agent lifted its driving score by **about a third**, and **doubled** how often it finished a route cleanly.
- Add a sentence of passenger guidance where it helps — which happened on roughly **15% of the routes** — and the pair lands level with **Raw2Drive**, the strongest end-to-end driving model reported on this benchmark.

That last point is the one we care about. A simple, interpretable, rule-based car plus a language model reached the performance of a heavyweight learned system, without retraining anything.

<figure class="figure-wide">
  <a href="/assets/images/portfolio/stucksolver-recovery.jpg" target="_blank" rel="noopener"><img src="/assets/images/portfolio/stucksolver-recovery.jpg" alt="Speed trace from the simulator: the car is driving at 20 km/h, brakes to a standstill for plastic bags in the lane, StuckSolver intervenes at about 6.6 seconds, and the car is back at 20 km/h by 14 seconds"></a>
  <figcaption>A real run. The car is cruising at 20 km/h, brakes for plastic bags in its lane, and sits at zero. StuckSolver steps in at about 6.6 s, judges the bags harmless, and the car is back up to speed by 14 s. Click to enlarge.</figcaption>
</figure>

<figure class="figure-wide" style="max-width: 640px;">
  <a href="/assets/images/portfolio/stucksolver-reroute.jpg" target="_blank" rel="noopener"><img src="/assets/images/portfolio/stucksolver-reroute.jpg" alt="Both lanes blocked by barricades; StuckSolver decides to stop and re-plan, and the map shows the new route around the block"></a>
  <figcaption>When there is no way through, it says so. Barricades block both lanes; StuckSolver reports that lane changing is not an option and asks for a new route — the red line is the detour it triggered. Click to enlarge.</figcaption>
</figure>

<div class="finding-grid">
  <div class="finding-card">
    <div class="finding-icon">🧩</div>
    <h3>An add-on, not a rewrite</h3>
    <p>It connects through ordinary interfaces and changes nothing inside the car's own modules — so an existing fleet could adopt it without redesigning its stack.</p>
  </div>
  <div class="finding-card">
    <div class="finding-icon">💬</div>
    <h3>Help without a steering wheel</h3>
    <p>A passenger who cannot drive can still get the car moving, by saying what they see. The model checks the suggestion against traffic rules before acting on it.</p>
  </div>
  <div class="finding-card">
    <div class="finding-icon">🔍</div>
    <h3>It explains itself</h3>
    <p>Every decision comes with the reason behind it in plain sentences — much easier to audit than a number coming out of a neural network.</p>
  </div>
  <div class="finding-card">
    <div class="finding-icon">🎚️</div>
    <h3>A dial, not a switch</h3>
    <p>Change when it wakes up and you slide between "cheap rule-based car" and "language model involved almost continuously", trading compute for adaptability.</p>
  </div>
</div>

## What it does not solve

- **It is slow.** About 2.8 seconds per query. Fine for a car that is already standing still; useless for anything time-critical. A distilled, faster model is the next step.
- **It is simulation.** Everything here is CARLA. Real roads, real sensors, real latency are still ahead.
- **Passenger guidance is a demonstration, not a product.** We gave simple instructions where they obviously helped. Knowing *when* to ask a human, and how much to trust the answer, is its own research problem.

<p style="font-size: 1.4em; font-style: italic; text-align: center; margin: 2rem 0;">The hard part was never the driving. It was knowing that a plastic bag is only a plastic bag.</p>

<p class="project-credit">Joint work with Qianwen Li at the University of Georgia. Published at the <em>IEEE Intelligent Vehicles Symposium (IV 2026)</em>, Detroit, June 2026.<br><a href="https://ieeexplore.ieee.org/document/11623937" target="_blank" rel="noopener">Read the paper on IEEE Xplore</a></p>
