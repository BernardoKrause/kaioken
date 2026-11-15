# Kaioken

> Power up only when the fight calls for it.

A [Claude Code](https://code.claude.com) skill that picks the **effort level** for each request on its own and applies it for the rest of the turn. You don't call it: Claude decides while it works.

Like Goku's Kaioken, more power costs more. Max effort on a button color change burns time and tokens for the same result. Kaioken raises effort only when the task needs it.

| Kaioken | Effort | When |
|---|---|---|
| Base | `low` | You want to iterate together; small tweaks, sketches, brainstorms |
| x2 | `medium` | Clear task, clear deliverable: a scoped feature, an email, a summary |
| x10 | `high` | Verifying and testing; bugs in existing code; edge cases, security, performance; mistakes that cost money or a decision |
| x20 | `xhigh` | Long, multi-step, critical work you leave running alone |
| x100 | `max` | Hard problems run alone, like a security audit |

## How it works

Effort is not a quality dial. Anthropic's own tests found that higher effort mostly buys **verification**: more edge cases tested, more checks, more decisions made without you. It cuts edge-case misses, but it does not fix a wrong approach.

On every work request, the `kaioken` skill:

1. Uses your level if you named one ("do it on max").
2. Checks context first. A vague request gets a short question, not more effort.
3. On a retry, looks at why the last attempt failed: wrong approach → fix the request, don't raise effort; missed edge cases → one level up.
4. Classifies the request with the table above. A fully specified request goes one level lower, since a detailed spec makes levels behave alike. When torn, it picks the lower level.
5. Says it in one line: `Effort: high — numbers that drive a decision`.
6. Invokes `kaioken-<level>`, a tiny skill whose only job is its `effort` frontmatter field. Per the Claude Code docs, that field overrides the session effort for the rest of the current turn.

It follows the feature loop from the Anthropic article: **draft cheap, verify expensive**. Build and iterate on `low`/`medium`; when the result is code, numbers or a decision, Claude suggests verifying and testing it on `high`.

## Install

1. Copy the skills:

   ```bash
   git clone https://github.com/BernardoKrause/kaioken.git
   cp -R kaioken/skills/* ~/.claude/skills/
   ```

2. Add this to `~/.claude/CLAUDE.md` so it runs on every request, not only when the skill description happens to match:

   ```markdown
   ## Effort
   Before carrying out any work request (including follow-ups and tweaks), use the `kaioken` skill. Pure questions and conversation are out.
   ```

3. Start sessions on `low` or `medium`. Kaioken raises the level when a request needs it.

## Limits

- The level lasts one turn. Each message is classified again.
- The classification itself runs at the session's effort.
- Each work request costs two small skill calls.
- The `effort` override does not beat the `CLAUDE_CODE_EFFORT_LEVEL` environment variable.

## Checking it works

Claude Code records the effort of each message in the session transcript (`~/.claude/projects/<project>/<session>.jsonl`). After `kaioken-low` runs, the following entries show `"effort":"low"`.

**Prompt cache.** The API docs warn that changing effort between requests can invalidate the prompt cache. Measured in a real Claude Code session on Opus 5.5, it did not: at each switch the cache read kept growing and only the usual per-turn tokens were written.

| Switch | Cache read before → after | Cache written after |
|---|---|---|
| high → low | 141,401 → 142,711 | 500 |
| low → high | 152,119 → 153,150 | 26 |
| high → medium | 163,624 → 166,387 | 1,675 |
| medium → high | 168,062 → 168,429 | 918 |

## Sources

- [Using Claude Code: Spending your effort](https://claude.dev/blog/spending-your-effort/) — Thariq Shihipar, Anthropic, 2026-09-25. The main reference: rules of thumb per level, the feature loop, and why effort buys verification.
- [Model configuration](https://code.claude.com/docs/en/model-config) — Claude Code docs: levels, defaults, `/effort`, and the `effort` field in skills.
- [Effort](https://platform.claude.com/docs/en/build-with-claude/effort) — Claude API docs.

## Credits

The idea came from a [reel by @brunoptrevisan](https://www.instagram.com/reel/DfCjTD5Pf5N/) explaining the article.

## License

[MIT](LICENSE)
