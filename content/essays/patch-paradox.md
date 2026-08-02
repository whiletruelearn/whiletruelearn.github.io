---
title: "The Patch Paradox"
date: 2026-08-02T15:19:00+05:30
draft: false
---

I have noticed a strange side effect of AI coding agents that nobody seems to be talking about much, and it's not the usual stuff like job displacement or code quality. It's something closer to a math problem, and once you see it as a math problem, it's hard to unsee.

Here's the setup. Building software has never been this cheap. You can describe a feature to an AI coding agent and it writes it, wires it up, tests it, ships it. What used to take a team weeks now takes a person an afternoon. This is genuinely great and I don't want to undersell it.

But the same forces that make building cheap also make attacking cheap. An AI agent can just as easily be pointed at a piece of software to probe it, poke every input field, hit every API endpoint, and look for the one place where something breaks. It doesn't get bored, it doesn't need coffee breaks, and it can do this at a scale no human red team ever could.


So now we have two agents pulling in opposite directions on the same system leading to some interesting consequences.

# Setting Up the Two Agents

Let's call the coding agent `C`. Its job is to write and maintain software. Let's call the attack agent `A`. Its job is to study that software and find weaknesses in it.

Now the thing both of them are operating on is the software itself, and I want to define its size a bit more carefully than just "lines of code." Call this `S`. `S` is really the sum of everything that exists in the system, lines of code, sure, but also the number of features, the API endpoints, the third party packages pulled in, the services talking to each other, the permissions granted, the integrations wired up. Basically, everything that could possibly go wrong is part of `S`.

The intuition here is simple. The bigger `S` gets, the more places `A` has to search. More rooms in the house means more doors and windows a burglar could try. This doesn't mean every additional line of code is equally risky, some code is genuinely safer than other code, some architectures are cleaner than others, but on average, a bigger and more tangled system gives you more chances for something to slip through.

# What Happens When A Finds Something

Say `A` finds a vulnerability. Naturally, `C` has to respond. Maybe it's a new validation check, an extra permission gate, a new security microservice, one more field in a database schema, one more layer of code wrapped around the vulnerable part.

Let's call the size of this fix `P`. So after the fix goes in, the new size of the system is:

`S = S + P`

This is where the interesting part starts. The fix genuinely reduces the risk that `A` just found, that's not in question. But it also makes the system bigger. And the system being bigger means there's more code that hasn't been battle tested yet, more surface for the next round of scanning, and more ways this new code can quietly interact with the old code in ways nobody planned for.

That's the paradox in one line: **`C` reduces a known risk by increasing the size of the system, and increasing the size of the system may increase the unknown risk.**

# The Loop

Once you see it this way, the whole thing turns into a cycle rather than a one time event.

`A` finds a weakness. `C` patches it. The patch removes that specific weakness but adds to the total surface. The larger surface gives `A` more to search through next time. `A` searches again.

This is a fundamentally different kind of problem than the ones we're used to solving in engineering. Normally when you fix things, you're converging toward some stable, "done" state. Here, the act of fixing the problem changes the shape and size of the problem itself. It's a moving target, and the mover is you.

There's another wrinkle that makes this worse, and it's easy to miss if you only think about vulnerabilities that exist right now. A vulnerability doesn't have to exist at the moment code is written for it to eventually get exploited. It just has to exist, silently, until someone or something classifies it as a vulnerability. A library that was considered safe for years can turn unsafe overnight because a new technique for breaking it gets discovered, a new dependency it relies on gets compromised, or a new class of attack gets invented that nobody had thought to defend against. The code doesn't change. The definition of "vulnerable" changes underneath it.

This means `A` isn't just searching today's surface for today's known weaknesses. It's effectively searching today's surface for tomorrow's weaknesses too, weaknesses that don't have a name yet. Every piece of `S` that exists right now, even the parts that look completely clean under every scan available today, is a candidate for being reclassified as dangerous the moment new knowledge about attacks catches up to it. So the size of the system isn't just a bet on current risk, it's a standing bet on all future discoveries of risk, for as long as that code stays in production.

# A Simple Ratio To Think about.

Here's a way to make this concrete. Every patch, on average, removes one known vulnerability. But it also has some chance of quietly introducing a new, unknown one, just by virtue of adding more surface area.

If each patch introduces less than one future vulnerability on average, the system is slowly getting safer over time. If it introduces exactly one, you're running on a treadmill, fixing one thing while creating exactly enough new risk to replace it. If it introduces more than one, the whole system is getting more dangerous even while you're actively, diligently patching it.

You can boil this down to a single ratio:

`new risk created by a fix / old risk removed by the fix`

Below 1, you're winning. Equal to 1, you're standing still while looking busy. Above 1, you're actually making things worse, and the scary part is that from the outside, in terms of commits merged and tickets closed, it looks exactly like progress.

The problem with this ratio as you can guess is it's almost impossible to measure this correctly.

# Why "Lines Shipped" Is the Wrong Scoreboard

Right now, most teams measure coding agents (and engineers, for that matter) by output. Features shipped, bugs closed, PRs merged, lines written. These are easy to count, so we count them.

But none of these numbers tell you whether the system is becoming easier or harder to defend. An agent that writes ten thousand lines might look ten times more productive than one that writes a thousand. But if that smaller, tighter solution has fewer places where something can go wrong, it might genuinely be the better outcome, even though it looks worse on the leaderboard.

So here's a thought experiment. Two coding agents are given the same problem. The first one solves it by standing up five new services, pulling in a dozen packages, and writing thousands of lines of glue code. The second one solves it by deleting an old, half used feature, simplifying the design, and making a small, surgical change.

By the old scorecard, the first agent wins easily. But if you're measuring by the ratio above, the second agent probably made the better call. It delivered the same value to the user without meaningfully expanding what an attacker could go after.

This is why I think the real goal isn't maximizing code produced. It's maximizing useful behavior while keeping the surface, `S`, as small as you can get away with.

So when companies say that they are more productive than ever, are they measuring in terms of LoC, pull requests , tickets closed in an issue tracker. How useful are these metrics are going to be in the long term?

# Deletion Deserves Equal Billing With Creation

If adding code grows `S`, then removing unnecessary code shrinks it. And a smaller `S` isn't just safer in the abstract, it's fewer things for a human team to review, test, and actually keep in their heads.

Which means a coding agent facing a vulnerability really has two options, not one. Option one is the default: patch it, wrap it in another layer, add a check. Option two, and this is the one that almost never gets considered, is to simplify the system so that the vulnerable part doesn't need to exist at all.

The second option gets ignored constantly, and I think it's mostly because coding agents (like most engineers, honestly) are wired to add things when given a problem. Ask them to fix something and the instinctive move is to write new code, not to ask "can we just remove the thing that's causing this." But mathematically, if all you ever do is add, you're not converging on safety. You're just building a bigger structure with proportionally more cracks in it.

# Why Deletion Doesn't Actually Happen Much

Here's the honest complication though. Even though deletion is the mathematically cleaner move, it's also the scarier one, and for good reason.

Adding a patch is, by comparison, a low stakes action. If it's wrong, the blast radius is usually small, you get a new bug, maybe a failed test, something you can catch and roll back. But deleting something in a live system is a different category of risk entirely. An old table might look unused from the code, but some report that runs once a quarter might still read from it. An old endpoint might have exactly one internal script hitting it that nobody remembers writing. An agent that decides to "clean up" a system by dropping tables or removing code paths it judges to be dead weight can quietly cause an outage, and outages are a much more immediate, visible failure than a slow accumulation of surface area that might get exploited someday.

This asymmetry shapes how coding agents, and the harnesses and guardrails wrapped around them, actually behave in practice. A team building the scaffolding around a coding agent has every incentive to make deletion hard and addition easy. Reversibility is the whole reason. A patch you regret can be patched again. A table you dropped and a downstream job that quietly starts failing three weeks later is a much harder thing to trace back and undo. So most harnesses end up encoding a bias, sometimes explicitly, sometimes just as an emergent property of how they're built, that favors bolting new things on over removing old ones. It is safer, in the narrow, immediate sense, to glue one more layer onto a running system than to risk pulling out a piece that turns out to be load bearing.

The problem is that this immediate safety comes at the cost of the long term safety we talked about earlier. Every time the system opts for "add a patch" over "remove the vulnerable part," it's trading a small, visible, recoverable risk today for a small, invisible addition to `S` that compounds over time. Nobody makes this trade-off consciously in any single instance, it's just the locally rational choice every time. But locally rational choices, repeated enough times, are exactly how you end up with a system that's much larger and much harder to secure than anyone intended it to be.

# When Both Sides Get Faster, You Get an Arms Race

Here's where it gets genuinely uncomfortable. Both `C` and `A` are getting better at their jobs at the same time.

As `C` gets faster, `S` grows faster. As `A` gets faster, it finds vulnerabilities in that growing `S` faster too. Put those together and you get a straightforward arms race. `C` ships more features and fixes. `A` gets a bigger surface to search. `C` responds with even more code to plug the new holes. `A` gets handed an even bigger surface than before.

Both sides keep getting more capable, but the software itself keeps getting harder for the humans nominally in charge of it to actually understand.

You might have recently heard of the story of `huggingface` being attacked by  `openai` models during an unconstrained capability test and the team using `glm5.2` for forensnic analysis and to contain the intrusion. Also we have today ideas like `loop engineering` , `code review agents` which makes most of what I am discussing not a very far fetched idea. Even though most state of the art model providers say that they have checks in place to prevent their platform being used for an actual cyber attack, it's easy to see that specialised models could be built for this purpose by finetuning open weight models.  

# The Number Nobody's Tracking: Code vs Human Understanding

This brings up a second ratio that I think matters just as much as the first one, maybe more: the amount of software compared to the amount of human understanding of that software.

A team can use AI to double the size of their codebase without doubling the number of people who actually understand what's in it. The code scales. The human comprehension of that code does not scale at the same rate, it almost never does.

At some point, you cross a line where the system has become larger than the team's collective ability to reason about it. And that's genuinely dangerous, because security was never purely a function of whether the code technically works. It's also a function of whether a human being understands what that code does, what it's connected to, and what happens when one piece of it fails in a way nobody anticipated. Code that works but that nobody understands is a liability wearing the costume of an asset.

# Three Forces, Not Two

So really, this isn't a two player game between the coding agent and the attack agent. There's a third force in the room: the human team trying to hold onto some understanding of what's actually being built.

`C` grows the surface. `A` searches the surface for cracks. The human team tries to keep enough of a mental model to actually govern the whole thing.

If the surface grows faster than `A` can be contained and faster than the humans can keep up with understanding it, the whole system tips toward instability. Not because any single actor did something wrong, but because the structure of the game rewards speed over comprehension.

# Who Actually Wins This Game

Given all this, I don't think the long term winner is the coding agent that ships the most code. I think it's the one that creates the least new surface for every genuinely useful thing it delivers.

And I don't think the most "secure" agent is the one that's best at bolting on patches after the fact. I think it's the one that knows when the right move is to simplify, to restrict, to remove something entirely, or sometimes, to just refuse to build the thing at all because the value it adds doesn't justify the surface it would create.

AI has made the act of writing software nearly free. But I don't think it's made maintaining software cheaper, if anything it might be making that more expensive. The marginal cost of one more line of code is heading toward zero. The marginal cost of understanding that line, testing it properly, and defending it five years from now, is not heading anywhere close to zero.

That, to me, is the actual paradox sitting underneath all of this. The easier it becomes to create software, the easier it becomes to create more software than we can realistically keep safe. And every patch we write to defend what we've already built quietly opens up a few new places that will eventually need defending too.
