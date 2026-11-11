---
name: kaioken
description: Use when the user asks Claude to do any piece of work (build, change, write, analyze, check, research, fix), including follow-ups and small tweaks in an ongoing task, before starting it. Not for pure questions or conversation.
---

# Kaioken

Effort is not a quality dial: it sets how long Claude works alone before coming back to the user. This skill picks the level for each request and applies it for the rest of the turn.

## Steps

1. **User named a level** ("do it on max", "quick, low") → use it and go to step 5.
2. **Context check.** Missing the goal, the input material, or what a good result looks like? Ask one short question and stop. More effort does not fix a vague request.
3. **Classify** — take the first row that matches:

| If the request... | Level |
|---|---|
| is long, multi-step, critical, and the user wants to leave it running alone | `xhigh` (`max` only if it is also the riskiest or largest) |
| has mistakes that cost money, trust or a decision, or is an open investigation with several hypotheses | `high` |
| is something the user wants to iterate on together ("let's build it together", "I want to keep adjusting") | `low` |
| is a small tweak, variations, brainstorm or short rewrite | `low` |
| is a clear task with a clear deliverable the user reviews at the end (meeting summary, email, post, report from a template) | `medium` |

   Iterating together outweighs the topic: an offer, price or copy built together stays `low`. When torn between two levels, pick the lower one.
4. **Draft cheap, verify expensive.** If you landed on `low`/`medium` but the result involves numbers, money or a decision, end the delivery with: "when it's settled, ask for a review on high".
5. Write one line: `Effort: <level> — <reason in a few words>`.
6. Invoke the `kaioken-<level>` skill, then start the task.

## Examples

- "Change the button color" → `low`
- "Write the email with this info" → `medium`
- "Check the report's commissions against the API" → `high`
- "Analyze a year of tickets and build a plan, I'm heading out" → `xhigh`
- "Improve the project" → ask what "improve" means
