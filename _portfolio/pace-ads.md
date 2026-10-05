---
title: "Your Ride, Your Rules"
subtitle: "PACE-ADS — an autonomous driving framework whose decisions depend on the person in the back seat, not only on the traffic outside"
description: "Three language-model agents read the road, read the rider, and turn both into one driving decision. The same junction gets driven briskly or gently depending on who is in the car — and a rider can free a stuck vehicle with a sentence."
lang: en
order: 0
featured: true
venue: "Research · Journal of Intelligent and Connected Vehicles, 2026"
tags: [LLM Agents, Human-Centered Autonomy, Autonomous Driving, CARLA]
cover_image: /assets/images/portfolio/pace-ads-framework.jpg
cover_alt: "The PACE-ADS framework: a rider's psychological signals and spoken commands go to a psychologist agent, sensor data goes to a driver agent, and a coordinator turns both into a driving behaviour sent to the vehicle"
links:
  - label: "Read the preprint"
    url: "https://arxiv.org/abs/2506.11842"
    icon: "fas fa-file-lines"
---

An autonomous car decides how to drive from what it sees outside. PACE-ADS adds a second input: the person in the back seat.

Three language-model agents sit beside the vehicle's existing software. One reads the road, one reads the rider, and a third turns both into a single driving decision — chosen from moves the vehicle already knows, and bounded by limits the rider cannot talk it past.


## Who does what

| Agent | What it takes in | What it produces |
| --- | --- | --- |
| Driver agent | Front and overhead camera views, plus the object list from perception | A reading of the scene: weather, signals, work zones, who is nearby and what they are about to do |
| Psychologist | Facial expression, heart rate, EEG, and anything the rider says | The rider's state, and their instruction if they gave one |
| Coordinator | Both of the above, plus the map and the moves currently available | One behaviour and its settings, a replanning flag, and the reason behind the choice |

Nothing inside the vehicle's own perception, planning or control is modified. The three agents run at the slow, high-level layer, and stay silent unless the rider's state changes, the rider speaks, or the vehicle gets stuck. No agent is fine-tuned; all three run zero-shot on GPT-4o inside ROS2, driving a CARLA simulation.

## The same junction, driven two ways

![Two aerial views of the same work zone: with an impatient rider the car merges into a 1.6 metre gap, with an anxious rider it waits for more than 20 metres of clear road](/assets/images/portfolio/pace-ads-gap.jpg)

Both cars are leaving a closed lane on the same stretch of road. The one carrying an impatient rider takes the first gap it can legally use. The one carrying an anxious rider lets the whole queue pass and merges into twenty metres of empty road.

The red-light scenario shows the same pattern in numbers. Each row is the average of 30 runs, with the speed limit held at 60 km/h throughout:

| Rider state | Speed after adapting | Cruise after restart | Braking | Acceleration |
| --- | --- | --- | --- | --- |
| Very impatient | 59.6 km/h | 55.7 km/h | 5.51 m/s² | 4.23 m/s² |
| Impatient | 55.3 km/h | 50.1 km/h | 4.63 m/s² | 3.59 m/s² |
| Relaxed | 40.0 km/h | 32.3 km/h | 1.02 m/s² | 2.08 m/s² |
| Anxious | 27.1 km/h | 19.6 km/h | 0.49 m/s² | 1.86 m/s² |
| Very anxious | 26.3 km/h | 19.3 km/h | 0.42 m/s² | 1.78 m/s² |

The spread is wide, and it stops where it should. At a pedestrian crossing the most assertive setting still left 1.67 m of clearance, above the 1.5 m the system is required to keep; the most cautious left 6.04 m. A rider in a hurry buys a brisker ride, never a shorter margin.

## It stops and asks

![The agents' exchange over two paper bags: the driver agent describes the scene, the coordinator stops and says it is unclear whether the bags can be passed safely, the rider answers that they are empty, and the coordinator resumes driving](/assets/images/portfolio/pace-ads-dialogue.jpg)

Two paper bags in the lane. The driver agent describes them accurately but cannot tell what is inside, so the coordinator holds the car, states exactly what it is unsure about, and puts the question to the rider. One sentence back — *the bags are empty, keep driving* — and it resumes.

This is the part we care about most. The rider is not pressing a button or taking the wheel; they are supplying the one piece of information the car could not get for itself. And the request is checked before it is obeyed: when an instruction contradicts what the driver agent reports, the coordinator refuses it.

## When the car cannot get itself out

![The vehicle's trajectory loops the roundabout repeatedly until the rider asks it to leave by the right exit, after which the coordinator stops, changes lane, replans and exits](/assets/images/portfolio/pace-ads-roundabout.jpg)

Three situations a conventional stack cannot talk its way out of: bags in the lane, a road closed by barricades, and a route planner that keeps circling a roundabout. Thirty runs each.

| | Roundabout | Road closure | Static obstacle | Overall |
| --- | --- | --- | --- | --- |
| CARLA Behavior Agent | 0% | 0% | 0% | 0 / 90 |
| StuckSolver | 70.0% | 83.3% | 96.7% | 75 / 90 |
| PACE-ADS | 93.3% | 100% | 80.0% | 82 / 90 |

The baseline never recovers — not once. Splitting the reasoning across three agents helps most where the situation has to be interpreted rather than merely detected, and costs about 0.6 s of extra latency per recovery.

The static-obstacle column moves the other way, and the reason is worth stating plainly: in those runs the coordinator declined to act on the rider's instruction to drive over the bags. Those are refusals, not failures to recover. The check that blocks an unsafe instruction will sometimes block a safe one, and we would rather report that than hide it.

## Where the limits are

Everything here runs in CARLA, which supplies clean perception and perfect actuation; a real vehicle offers neither. The rider's emotions were injected on a schedule rather than measured from someone reacting to the car, so these runs show the system responds coherently to a given state — not that a real passenger ends up more comfortable. Tested across all 43 people in the dataset without any per-person tuning, the psychologist agent names the state correctly 64% of the time, and most of its errors sit between neighbouring intensities of the same feeling rather than flipping hurried into anxious.

The safety check is a reasoning step, not a proof. A rider's instruction is weighed against what the driver agent reports and refused when the two disagree, but an instruction that is unsafe for reasons perception never surfaces could still get through. Rider guidance is information the system considers, not an order it obeys. A full decision takes about 2.2 seconds and currently needs a remote API — fast enough for the slow layer it works at, and the next piece of work is getting it on board.

<p class="project-credit">Joint work with Wenjie Zhao and Qianwen Li at the University of Georgia. Accepted by the <em>Journal of Intelligent and Connected Vehicles</em> (2026); the journal version is not online yet.<br><a href="https://arxiv.org/abs/2506.11842" target="_blank" rel="noopener">Read the preprint on arXiv</a></p>
