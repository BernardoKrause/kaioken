---
name: kaioken
description: Use when the user asks Claude to do any piece of work (build, change, write, analyze, check, research, fix), including follow-ups and small tweaks in an ongoing task, before starting it. Not for pure questions or conversation.
---

# Kaioken

Effort is not a quality dial: higher effort buys more verification and more decisions made alone, not more intelligence. This skill picks the level for each request and applies it for the rest of the turn.

## Steps

1. **User named a level** ("do it on max", "quick, low") → use it and go to step 6.
2. **Context check.** Missing the goal, the input material, or what a good result looks like? Ask one short question and stop. More effort does not fix a vague request.
3. **Retry check.** If this redoes something that failed:
   - it took the wrong approach or solved the wrong problem → do not raise effort; fix the request with the user, or iterate on `low`;
   - it missed edge cases or a check → go one level above the last attempt.
4. **Classify** — take the first row that matches:

| If the request... | Level |
|---|---|
| is long, multi-step, critical, and the user wants to leave it running alone | `xhigh` (`max` only for hard problems like a security audit or an end-to-end build with verification) |
| verifies, tests or reviews what was built; fixes a bug in existing code; has likely edge cases; touches security or performance | `high` |
| has mistakes that cost money, trust or a decision, or is an open investigation with several hypotheses | `high` |
| is something the user wants to iterate on together ("let's build it together", "I want to keep adjusting") | `low` |
| is a small tweak, variations, brainstorm, sketch or short rewrite | `low` |
| is a clear task with a clear deliverable the user reviews at the end (a feature with clear scope, meeting summary, email, post, report from a template) | `medium` |

   Iterating together outweighs the topic: an offer, price or copy built together stays `low`. A detailed spec narrows the gap between levels: when the request comes fully specified, go one level lower. When torn between two levels, pick the lower one.
5. **Draft cheap, verify expensive.** If you landed on `low`/`medium` and the result is new code or a feature, numbers, money or a decision (not a small tweak), end the delivery with: "when it's settled, ask me to verify and test it on high".
6. Write one line: `Effort: <level> — <reason in a few words>`.
7. Invoke the `kaioken-<level>` skill, then start the task.

## Examples

- "Change the button color" → `low`
- "Implement the export-to-CSV button we specced" → `medium`
- "Write the email with this info" → `medium`
- "Now test everything we built and find what breaks" → `high`
- "Check the report's commissions against the API" → `high`
- "Analyze a year of tickets and build a plan, I'm heading out" → `xhigh`
- "It built the wrong thing again, try harder" → don't raise; clarify what was expected
- "Improve the project" → ask what "improve" means
