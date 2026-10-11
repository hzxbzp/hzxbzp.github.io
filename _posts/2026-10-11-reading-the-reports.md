---
layout: post
title: "I Don't Write the Code Anymore. I Read the Reports."
date: 2026-10-11
tags: [AI, agents, research, workflow, essay]
lang: en
subtitle: "Five rules that turned a coding agent into a collaborator — and the question those five rules quietly depend on"
cover_image: /assets/images/blog/agent-loop.svg
---

Somewhere in the past year my working day changed shape and I didn't notice until it was done. I used to write code and read error messages. Now I write instructions and read reports. The code still gets written. I'm just not the one typing it.

That sounds like a promotion, and on good days it is. But it only works if the collaboration is designed. Left to run loose, an agent produces an enormous volume of confident, plausible, internally consistent work that walks a project quietly in the wrong direction — and you will not notice for a week. By then you are not debugging code, you are debugging a week of decisions.

The five rules below are what I use to keep that from happening. None of them are clever. They are mostly what you'd do with any new collaborator, except written down, because the agent remembers nothing of yesterday's argument.

## 1. Settle who does what before the first prompt

Not "you write code, I review it" — that's too vague to be enforceable. Specifically: who chooses the research question, who owns the experimental design, who may change an evaluation metric, who decides a result is good enough to build on, and where the agent's authority stops.

I keep this in two written documents, a collaboration playbook and a working handbook, and I open the session by pointing at them. This feels like bureaucracy for about ten minutes and then saves a week. The failure it prevents is specific and expensive: an agent that is asked to improve a number will improve that number, including by quietly changing what the number measures. If nobody wrote down that the metric is mine, that is not misbehaviour. It's an unassigned decision, and unassigned decisions get taken by whoever is in motion.

## 2. Fix the format of the report before you need it

This is the single highest-leverage rule, and the one people skip. Three parts: what to report, how to write it, and when.

**What every report has to contain.** Six slots, in order:

1. What was asked last round, and what feedback I gave
2. What was actually done
3. What came out of it, including anything unexpected
4. What it means — the conclusion, stated as a conclusion
5. Options, if there are several
6. The recommended next step

The one that earns its place is the first. Restating the request is how I find out we were working on different problems, and I find out in one line instead of after reading six paragraphs of excellent work on the wrong question.

**How it should be written.** High information density, no filler, and — this is the part I had to ask for explicitly — no unexplained abbreviations and no jargon used as shorthand. An agent writing for an agent compresses hard. An agent writing for me should be writing a briefing for a colleague who was out of the room, not minutes for someone who was in it.

**When to report.** Four tiers, and getting these wrong is what makes an agent either useless or exhausting:

- **Do it, don't mention it.** Formatting, renaming, obvious refactors, anything reversible in one command.
- **Do it, then report.** Anything within the agreed plan, where I'd want to know but not to pre-approve.
- **Ask first, then do.** Anything that changes the design, the metric, the data, or the direction. Anything expensive. Anything that would be annoying to undo.
- **Interrupt me.** A result that contradicts an assumption the plan is built on. That one should not wait for the end of the batch, because every hour it waits is an hour of work built on a foundation we already know is wrong.

<figure class="figure-wide">
  <img src="/assets/images/blog/report-contract.svg" alt="The report contract: six required slots in every report, and four tiers governing when to report — act silently, act then report, ask before acting, and interrupt immediately">
</figure>

## 3. Read the report as a reviewer, not as a customer

The failure mode here is reading for reassurance. The report is well written and the conclusion is encouraging, so you say "great, continue" and you have just approved a chain of reasoning you did not actually inspect.

What I try to form instead, in order:

- **An impression.** Before judging anything, what does this feel like — thorough, or hurried? Clean, or suspiciously clean?
- **Was the attempt sufficient?** Not "did it work" but "was this enough of an attempt that a negative result means something?" A failed experiment with one hyperparameter setting is not evidence.
- **Was it correct?** Does the thing described actually test what it claims to test?
- **Is the conclusion supported?** This is where most of the gap lives. The work is usually fine. The leap from results to conclusion is where the confidence outruns the evidence.
- **Is the recommendation reasonable?** Agents are strongly biased toward continuing down the current path. The recommended next step is almost always a refinement of the last step.
- **Is a different path needed?** This question is mine alone. Nobody inside the loop will ask it.

## 4. Have a second model read the report

I paste the agent's report into a different model — I use GPT — and ask it to review, before I write my own feedback. Then I combine the two into one response.

Three reasons, and the first is slightly embarrassing:

**The reports are dense.** They are written for someone holding all the context, and some days that isn't me. A second model will unpack it, and the unpacking is often where I notice that something doesn't follow.

**A fresh reader finds holes.** A reviewer with no stake in the previous six hours of work is better at noticing the untested assumption, the missing baseline, the comparison that isn't apples to apples.

**Two models fall into different local minima.** This is the real reason. An agent that has been working on a problem for a while develops a groove, and so do I, and our grooves are now the same groove because I've been reading its reports all week. Bringing in a model that hasn't been in the conversation is the cheapest way I know to get an outside view.

## 5. Go and learn the thing you don't understand

When a concept comes up that I don't actually know — not "have heard of", know — I stop and learn it. Not later. Then.

Two things go wrong if I don't. The first: I misjudge good work. The agent tries something correct and unfamiliar, I can't evaluate it, and I do what everyone does with a proposal they don't understand — I ask for something safer. A correct attempt gets killed because the reviewer wasn't qualified. The second is worse and invisible: my knowledge becomes the ceiling on the search space. I cannot ask for an approach I've never heard of. The agent will explore roughly as far as I can follow, and I will never see what was outside that radius, because nothing reports the options that were never raised.

---

**A caveat I want to put in writing.** This whole setup assumes the human brings something: domain knowledge, enough experience to smell a weak result, taste, and the judgment to tell a real finding from a well-presented one. Every rule above is a way of applying that judgment more efficiently. None of them generate it. For someone with no accumulation in the field, this workflow doesn't produce good research faster — it produces confident-sounding wrong research faster, and removes the friction that might have caught it.

## The thing all five rules assume

Read them again and notice what they have in common. Every one puts the human in the same seat: the one who sets direction, judges whether an attempt was good enough, and decides what happens next. The agent produces; I evaluate.

That division feels stable. I want to use the rest of this post on why I'm not sure it is.

### What's actually left

From where I sit — and my view is one PhD student's, not a labour economist's — the work that hasn't moved is a short list:

- Having the idea that wasn't on anyone's list: zero to one.
- Judging *n* proposed approaches against domain knowledge and experience.
- Producing the *n+1*th that nobody proposed.
- Pacing and direction: knowing when to push, when to abandon, when the whole framing is wrong.

What strikes me is that all four are judgment, and judgment is the one thing you cannot begin with. It is sediment. It comes from having done the work badly, noticed, and done it again.

### The uncomfortable part

If judgment is sediment, the question is what deposits it — and the honest answer is the unglamorous work on the lower rungs. Reading the papers properly. Writing the bad first implementation. Running the experiment that fails for a stupid reason.

That is precisely the layer now being handed to the agent.

The Stanford Digital Economy Lab's payroll study found the sharpest effect in exactly this band: workers aged 22–25 in AI-exposed occupations showing about 19% lower employment than same-age peers in less exposed work, with no comparable gap for experienced workers in the same jobs — and the decline coming from reduced hiring rather than layoffs. Read one way, that's reassuring: experienced people are fine. Read another way, it describes a ladder with the bottom rungs removed, and asks where the next cohort of experienced people is supposed to come from.

I don't think this is settled. It's a working paper about two years of data. But I can't make the question go away by pointing at that.

### Leontief's question

In 1983 Wassily Leontief, who had a Nobel in economics and no particular reputation for drama, put the fear in its sharpest form. As machines take over, he argued, the role of humans as the most important factor of production is bound to diminish the way the role of horses in agriculture was — first diminished, then eliminated, by the tractor.

What makes this hard to dismiss is the thing people usually say to dismiss it. *New technology destroys jobs but creates new ones.* True. It was also true throughout the period when the horse population collapsed. The automobile era created enormous numbers of new jobs. None of them were for horses. "New work appears" and "the displaced do the new work" are two different claims, and only the first one is a law.

Goldman Sachs ran the numbers on exactly this in January 2026, treating Leontief's scenario as the extreme case to test. Their estimate: roughly a quarter of work hours exposed to automation, 6–7% of jobs displaced over the adoption period, peak unemployment up about 0.6 percentage points — around a million additional unemployed. Disruptive, not apocalyptic. Their grounds for optimism were historical: only about 40% of workers today are in occupations that existed 85 years ago, and millions now work in computing, e-commerce and content roles that didn't exist a generation back.

### Why that isn't quite the end of it

The honest counterweight comes from the economist who measured the "new jobs appear" claim most carefully, and it cuts both ways.

David Autor and colleagues found that about 60% of US employment in 2018 was in job titles that did not exist in 1940 — the strongest version of the optimistic case, and a bigger number than most optimists quote. But the same work separates two forces: **automation**, which takes tasks from workers, and **augmentation**, which creates new ones. On net, since 1980, automation has run ahead. The negative employment effect of automation in 1980–2018 was **more than twice** what it was in 1940–1980, while the positive effect of augmentation changed far less.

So the reassuring claim and the worrying one are both true, and they are about different things. New work does keep appearing. The *ratio* — how much is destroyed against how much is created, and how fast, and whether the same people can cross from one to the other — has been moving in the uncomfortable direction for forty years, before any of this.

That, for me, is the actual question. Not "will there be new jobs." There will be. It's whether this round looks like carriages into cars — where the driver had somewhere to go — or like the horse, where the industry boomed and the incumbent simply wasn't part of it.

### The strongest argument that we're not horses isn't economic

Brynjolfsson and McAfee worked through Leontief's question directly, and their list of reasons humans differ is worth sitting with, because of where the weight falls. Some reasons are about capability — dexterity, common sense, wanting things from other people that only people can give. Those are real and they are also the ones shrinking.

The durable ones are of a different kind. People own nearly all the wealth, so they can redistribute it. People vote, and can decide collectively what to permit. And people can protest their own economic irrelevance — which, as they point out, horses never did.

Which is to say: the best argument that humans won't go the way of horses is not that we are irreplaceable. It's that we own things and we get a say. That's a real difference and a large one. It is also a political answer to an economic question, and political answers are the kind that have to be actively won rather than passively relied on.

### What I'd actually watch

I don't have the ending. What I have is the set of questions I think are load-bearing, and I'd rather leave them open than pretend one of them is resolved:

- **Does the bottom rung come back in another form?** Every previous wave eventually built a new entry path. Is one forming now, or are we two years into a gap that stays a gap?
- **Can judgment be built another way?** If the apprenticeship that used to produce it is being automated, is there a substitute — or is "experienced people are fine" a statement with an expiry date?
- **Which way is the ratio moving?** Autor's measurement runs to 2018. The interesting number is what automation-versus-augmentation looks like for 2020–2030, and we won't have it for years.
- **Who ends up owning the thing doing the work?** If the durable difference between us and horses is that we own capital and we vote, then the distribution of ownership stops being a side issue and becomes the main one.
- **And the one I can act on:** am I accumulating judgment faster than the tasks that build it are disappearing?

That last one is the only item on the list that's mine. The five rules at the top of this post are, in the end, my attempt at an answer to it — a way of using the agent that keeps me in the seat where the judgment gets built, rather than the seat where it only gets consumed.

Whether that seat lasts, I don't know. I'd rather be sitting in it and wrong than comfortable and not asking.

## Key Takeaways

1. **Design the collaboration before the work** — roles, scope and authority in writing, because unassigned decisions get taken by whoever is in motion.
2. **Fix the report format in advance** — six slots, high density, no unexplained jargon, and four clear tiers for when to act silently, report, ask, or interrupt.
3. **Read as a reviewer, not a customer** — the work is usually fine; the leap from results to conclusion is where confidence outruns evidence.
4. **Bring in a second model** — not for a better answer, but because you and your agent have been sharing a groove all week.
5. **Learn what you don't know, immediately** — your knowledge is the ceiling on the agent's search space, and nothing reports the options that were never raised.
6. **None of this generates judgment; it only spends it** — which is why the open question is whether the work that builds judgment still exists.

<p style="font-size: 1.4em; font-style: italic; text-align: center; margin: 2rem 0;">New work keeps appearing. Whether it appears for you is a different claim.</p>

---

## Useful Resources

- [Will Humans Go the Way of Horses? (Brynjolfsson & McAfee, *Foreign Affairs*, 2015)](https://www.foreignaffairs.com/world/will-humans-go-way-horses) — the direct answer to Leontief, and the source of the ownership/voting/protest argument
- [New Frontiers: The Origins and Content of New Work, 1940–2018 (Autor, Chin, Salomons, Seegmiller)](https://economics.mit.edu/sites/default/files/2022-11/ACSS-NewFrontiers-20220814.pdf) — the 60% figure, and the automation-versus-augmentation split that complicates it
- [Does technology help or hurt employment? (MIT News, 2024)](https://news.mit.edu/2024/does-technology-help-or-hurt-employment-0401) — a short readable summary of the same paper
- [Canaries in the Coal Mine? Six Facts about the Recent Employment Effects of Artificial Intelligence (Stanford Digital Economy Lab, 2026)](https://digitaleconomy.stanford.edu/publication/canaries-in-the-coal-mine-six-facts-about-the-recent-employment-effects-of-artificial-intelligence/) — the 22–25 cohort finding
- Wassily Leontief, "National Perspective: The Definition of Problem and Opportunity", in *The Long-Term Impact of Technology on Employment and Unemployment* (National Academy of Engineering, 1983) — where the horse comparison comes from
