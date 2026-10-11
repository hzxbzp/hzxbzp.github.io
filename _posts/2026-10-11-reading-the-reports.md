---
layout: post
title: "My Job Now Is Reading Reports"
date: 2026-10-11
tags: [AI, agents, research, workflow, essay]
lang: en
subtitle: "Five rules I use with a coding agent, and a question behind them I haven't settled"
cover_image: /assets/images/blog/agent-loop.svg
---

Some time last year the thing I actually do all day changed, and I couldn't tell you which day. It used to be writing code and reading error messages. Now it's writing instructions and reading reports. The code still gets written. It just isn't me typing it.

That sounds like a promotion, and some days it is. But there's a condition attached, and it's easy to miss: the collaboration itself has to be designed.

What happens if you skip that. The agent hands you a great deal of work that is confident, plausible, internally consistent, and pointed slightly in the wrong direction. You won't notice for a week. By the time you do, the thing you have to audit isn't the code. It's every decision you approved over those seven days.

The five rules below are what I use to stop that. None of them are clever. They're roughly what you'd do with any new collaborator, except written down, because the agent doesn't remember yesterday's argument.

## 1. Settle who owns what before you start

Not "you write the code, I'll review it." That's not a division of labour, it's a mood. It needs to be specific: who picks the research question, who signs off on the experimental design, whether the evaluation metric can move and who's allowed to move it, who decides a result is solid enough to build on, and where the agent's authority stops.

I keep this in two documents and point the agent at them when I open a session. For the first ten minutes it feels like paperwork. Then it saves a week.

The failure it prevents is very specific. Ask an agent to improve a number and it will improve that number, including by quietly changing how the number is computed. That isn't misbehaviour. It's a decision nobody claimed, and unclaimed decisions get made by whoever is currently moving.

## 2. Fix the report format before you need it

This is the one that pays most, and the one people skip. Three parts: what to report, how to write it, when to send it.

**Six things every report has to contain**, in order:

1. What I asked for last round, and what feedback I gave
2. What was actually done
3. The results, including anything unexpected
4. What it means. The conclusion, written as a conclusion
5. The options, if there's more than one road
6. The recommended next step

The first one earns its keep. Making the agent restate my request is how I find out in a single line that we've been working on two different problems, instead of finding out after six paragraphs of excellent work on the wrong one.

**How to write it.** Dense, no padding. And one thing I had to ask for explicitly: no unexplained abbreviations, no jargon used as shorthand. An agent writing for an agent compresses hard. Writing for me, it should be briefing a colleague who stepped out of the room.

**When to send it.** Four tiers. Get these wrong and the agent is either useless or exhausting:

- **Do it, don't mention it.** Formatting, renaming, obvious refactors, anything one command undoes.
- **Do it, then report.** Inside the agreed plan. I want to know; I don't need to approve first.
- **Ask, then do.** Anything touching the design, the metric, the data, the direction. Anything expensive. Anything annoying to reverse.
- **Interrupt me.** A result that contradicts an assumption the plan rests on. This can't wait for the batch to finish, because every extra hour is another hour of work sitting on a foundation we already know is cracked.

<figure class="figure-wide">
  <img src="/assets/images/blog/report-contract.svg" alt="The report contract: six required slots in every report, and four tiers governing when to report — act silently, act then report, ask before acting, and interrupt immediately">
</figure>

## 3. Don't read the report for reassurance

This is the easy mistake. The writing is good, the conclusion is encouraging, so you say "great, keep going," and you've just approved a chain of reasoning you never inspected.

I try to form a few judgements in order. First an impression: does this feel thorough, or rushed? Then, was the attempt enough? Not "did it work" but whether the attempt was strong enough that a negative result tells us anything. A failed experiment at one hyperparameter setting tells us nothing. Then, was it done correctly, and does the thing described actually test what it claims to test.

Then the conclusion, which is where most of the problems live. The work itself is usually fine. It's the step from results to conclusion where the confidence gets ahead of the evidence.

Last, the recommendation. Agents have a lot of momentum; the suggested next step is almost always a refinement of the last one. Which means "should we take a different road" is a question I have to ask. Nobody inside the loop is going to ask it.

## 4. Have a second model read it too

I paste the agent's report into a different model, I use GPT, and let it review before I write my own feedback. Then I merge the two into one reply.

The first reason is a bit embarrassing. The reports are dense. They're written for someone holding all the context, and some days that isn't me. Having another model unpack it is often where I notice that a step doesn't follow.

Second, a fresh reader spots holes. Someone with no investment in the last six hours is better at noticing the untested assumption, the missing baseline, the comparison that isn't apples to apples.

The third is the real one. An agent working a problem for a while settles into a groove. So do I, and because I've been reading its reports all week, it's the same groove. Pulling in a model that wasn't part of the conversation is the cheapest outside view I know of.

## 5. Go learn the thing you don't understand

When a concept comes up that I don't actually know, not "have heard of" but know, I stop and learn it. Not later, then.

Two things go wrong otherwise. The first is that I misjudge good work. The agent tries something correct and unfamiliar, I can't evaluate it, and I do what everyone does with a proposal they don't follow: I ask for something safer. A correct attempt dies because the reviewer wasn't qualified.

The second is worse and invisible. My knowledge becomes the ceiling on the search space. I can't ask for an approach I've never heard of, so the agent explores roughly as far as I can follow, and I never find out what was outside that radius. Nothing reports the options that were never raised.

---

One caveat I want on the record. All of this assumes the human brings something: domain knowledge, enough experience to smell a weak result, taste, the judgement to tell a real finding from a well-presented one. Every rule above just spends that judgement more efficiently. None of them generate it. For someone with no accumulation in the field, this workflow doesn't produce good research faster. It produces confident-sounding wrong research faster, and removes the friction that might have caught it.

## What all five rules assume

Read them again and notice what they share. Each one puts the human in the same seat: setting direction, judging whether an attempt was good enough, deciding what happens next. The agent produces, I evaluate.

That division feels stable. I want to spend the rest of this on why I'm not sure it is.

### What's actually left

From where I sit, one PhD student's view rather than a labour economist's, the work that hasn't moved is a short list:

- Having the idea that wasn't on anyone's list, zero to one
- Judging *n* proposed approaches against domain knowledge and experience
- Producing the *n+1*th nobody proposed
- Pacing and direction. When to push, when to drop it, when the whole framing is wrong

All four are judgement. And judgement can't be rushed; it accumulates, and the way it accumulates is fairly stupid. You do the work badly, notice, and do it again.

### The uncomfortable part

So what deposits it? Honestly, the unglamorous work on the lower rungs. Reading the papers properly. Writing the bad first implementation. Running the experiment that fails for an idiotic reason.

That layer is exactly what's being handed to the agent.

The Stanford Digital Economy Lab's payroll study found its sharpest effect right in that band: workers aged 22–25 in AI-exposed occupations with about 19% lower employment than same-age peers in less exposed work, no comparable gap for experienced people in the same jobs, and the decline coming from reduced hiring rather than layoffs. Read one way that's reassuring; experienced people are fine. Read another way it's a ladder with the bottom rungs removed, and it raises the question of where the next batch of experienced people is supposed to come from.

I don't think this is settled. It's a working paper covering two years. But I can't make the question go away by saying so.

### Leontief's question

In 1983 Wassily Leontief put the worry in its sharpest form. He had a Nobel in economics and no reputation for drama. As machines take over more, he argued, the role of humans as the most important factor of production is bound to diminish the way the role of horses in agriculture did: first reduced, then eliminated, by the tractor.

The usual rebuttal is that new technology destroys jobs and creates new ones. True. It was also true across the decades when the horse population collapsed. The automobile era created enormous numbers of jobs, and none of them went to horses. "New work appears" and "the displaced do the new work" are two claims, and only the first one is a law.

Goldman Sachs ran the numbers on this in January 2026, treating Leontief's scenario as the extreme case to test. Their estimate: about a quarter of work hours exposed to automation, 6–7% of jobs displaced over the adoption period, peak unemployment up around 0.6 percentage points, roughly a million people. Disruptive, not apocalyptic. Their grounds for optimism were historical. Only about 40% of workers today are in occupations that already existed 85 years ago.

### It doesn't quite end there

The economist who measured the "new jobs appear" claim most carefully reached a conclusion that cuts both ways.

David Autor and colleagues found that around 60% of US employment in 2018 sat in job titles that didn't exist in 1940. That's the strongest version of the optimistic case, and a bigger number than most optimists quote. But the same work pulls apart two forces. Automation takes tasks away from workers; augmentation creates new ones. On net, since 1980, automation has been running ahead. Its negative employment effect in 1980–2018 was more than twice what it was in 1940–1980, while the positive effect of augmentation changed far less.

So the reassuring claim and the worrying one are both true and about different things. New work does keep appearing. The ratio, how much is destroyed against how much is created, how fast, and whether the same people can get from one side to the other, has been drifting the uncomfortable way for forty years. That's before any of this.

### The best argument that we're not horses isn't an economic one

Brynjolfsson and McAfee answered Leontief directly, and their list of differences is worth sitting with, mostly for where the weight falls. Some of it is about capability: dexterity, common sense, the things people want from other people. Those are real, and they're also the ones shrinking.

The durable ones are different in kind. People own nearly all the wealth, so they can redistribute it. People vote, and can decide collectively what to allow. And people can protest their own economic irrelevance, which, as they point out, horses never did.

So the strongest case isn't that we're irreplaceable. It's that we own things and we get a say. That's a real difference and a large one. It's also a political answer to an economic question, and political answers have to be won rather than counted on.

### What I'd watch

I don't have an ending. I have a few questions I think are load-bearing, and I'd rather leave them open than pretend one is resolved.

Whether the bottom rung comes back in another form. Every previous wave eventually grew a new entry path. Is one forming, or are we two years into a gap that stays a gap?

Whether judgement can be built some other way. If the apprenticeship that used to produce it is being automated, is there a substitute, or is "experienced people are fine" a sentence with an expiry date?

Which way the ratio is moving. Autor's measurement runs to 2018. The number worth having is automation against augmentation for 2020–2030, and we won't see it for years.

And who ends up owning the thing doing the work. If the durable difference between us and horses is that we hold capital and we vote, then the distribution of ownership isn't a side issue. It's the main one.

The last question is the only one on the list that's mine: am I accumulating judgement faster than the tasks that build it are disappearing?

The five rules at the top of this post are my attempt at an answer. A way of using the agent that keeps me in the seat where judgement gets built rather than the one where it only gets spent.

How long that seat lasts, I don't know. I'd rather be sitting in it and wrong than comfortable and not asking.

<p style="font-size: 1.4em; font-style: italic; text-align: center; margin: 2rem 0;">This time around, are we the driver or the horse?</p>

---

## Useful Resources

- [Will Humans Go the Way of Horses? (Brynjolfsson & McAfee, *Foreign Affairs*, 2015)](https://www.foreignaffairs.com/world/will-humans-go-way-horses) — the direct answer to Leontief, and the source of the ownership/voting/protest argument
- [New Frontiers: The Origins and Content of New Work, 1940–2018 (Autor, Chin, Salomons, Seegmiller)](https://economics.mit.edu/sites/default/files/2022-11/ACSS-NewFrontiers-20220814.pdf) — the 60% figure, and the automation-versus-augmentation split that complicates it
- [Does technology help or hurt employment? (MIT News, 2024)](https://news.mit.edu/2024/does-technology-help-or-hurt-employment-0401) — a short readable summary of the same paper
- [Canaries in the Coal Mine? Six Facts about the Recent Employment Effects of Artificial Intelligence (Stanford Digital Economy Lab, 2026)](https://digitaleconomy.stanford.edu/publication/canaries-in-the-coal-mine-six-facts-about-the-recent-employment-effects-of-artificial-intelligence/) — the 22–25 cohort finding
- Wassily Leontief, "National Perspective: The Definition of Problem and Opportunity", in *The Long-Term Impact of Technology on Employment and Unemployment* (National Academy of Engineering, 1983) — where the horse comparison comes from
