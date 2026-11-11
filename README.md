# Kaioken

> Power up only when the fight calls for it.

A [Claude Code](https://code.claude.com) skill that picks the **effort level** for each request on its own and applies it for the rest of the turn. You don't call it: Claude decides while it works.

Like Goku's Kaioken, more power costs more. Max effort on a button color change burns time and tokens for the same result. Kaioken raises effort only when the task needs it.

| Kaioken | Effort | When |
|---|---|---|
| Base | `low` | You want to iterate together; small tweaks, variations, brainstorms |
| x2 | `medium` | Clear task, clear deliverable, you review at the end |
| x10 | `high` | A mistake costs money, trust or a decision; open investigations |
| x20 | `xhigh` | Long, multi-step, critical work you leave running alone |
| x100 | `max` | The riskiest or largest of those |

## How it works

Effort is not a quality dial. It sets how long Claude works alone, how much it investigates and verifies, and how many decisions it makes before coming back to you.

On every work request, the `kaioken` skill:

1. Uses your level if you named one ("do it on max").
2. Checks context first. A vague request gets a short question, not more effort.
3. Classifies the request with the table above. When torn, it picks the lower level.
4. Says it in one line: `Effort: high — numbers that drive a decision`.
5. Invokes `kaioken-<level>`, a tiny skill whose only job is its `effort` frontmatter field. Per the Claude Code docs, that field overrides the session effort for the rest of the current turn.

It also applies **draft cheap, verify expensive**: work you iterate on stays `low`/`medium`, and when it involves numbers or money, Claude suggests a final review on `high`.

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

## Credits

Inspired by a [reel by @brunoptrevisan](https://www.instagram.com/reel/DfCjTD5Pf5N/) on picking Claude's effort levels.

## License

[MIT](LICENSE)
