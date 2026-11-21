# Evidence First Debugging

A skill for AI coding agents that stops them from guessing.

Most agent debugging looks like this: write code, hit an error, guess a fix, hit the same error, guess again. Each guess costs tokens and teaches nothing. This skill replaces guessing with a discipline: pull evidence from every surface the system touches, correlate it, and never retry without a new fact.

## The incident that created it

A production integration failed. The agent spent hours and roughly 6 to 7 percent of a weekly token budget proposing fix after fix. Then someone opened the hosting logs of the frontend and the backend. The cause was written there in plain text. Fixed in 10 minutes.

The bug was a configuration mistake in our own setup. No search engine had the answer, because nobody else on earth has our exact DNS records, environment variables, and plan limits. The internet is a guesser trained on other people's systems. Your own logs are the only judge that knows your case.

## The core rule

**No second fix attempt without a new fact from reality.**

Before any retry, the agent must name the new fact the last evidence pull produced. If it cannot name one, it does not get to spend on another attempt.

Supporting rules, all in [SKILL.md](SKILL.md):

- **Declare the blind spots first.** List every surface (terminal, hosting logs, network, response bodies, database logs, DNS, CI) and mark which ones are actually reachable. A missing surface is a declared blind spot and conclusions stay provisional. Undeclared blindness is how a bad DNS record once fooled every local test.
- **Watch at checkpoints, correlate across surfaces.** Evidence pulls happen before the first attempt, after every failure, and before declaring fixed. Errors hide in the mismatch between surfaces, not inside one log.
- **The two failure fingerprint.** The same failure signature twice, after two different attempts, means the picture of the problem is stale, not that the attempts were weak. Stop, research the world's fresh knowledge (changelogs, status pages, issues from today), because a model knowledge base is old by default. If the world is silent, the issue is probably new: shrink to a minimal reproduction and work from first principles.
- **Failures become permanent rules.** Every understood failure is converted into a regression test, a lint rule, or a checklist line. In code, a rule can be a test, and a test enforces itself on every run at zero cost.
- **Retire rules too.** A rule that fires often and is often wrong gets demoted or deleted. Rulebooks that only grow become slow and contradictory.

## Use it

Copy `SKILL.md` into your agent's skills directory (for example `~/.claude/skills/evidence-first-debugging/SKILL.md` for Claude Code, or your agent framework's equivalent), or paste it into your system prompt or project instructions. It carries standard YAML frontmatter (name and description), so Claude Code and compatible agents load it automatically and trigger it on debugging tasks.

A worked example of the method catching what guessing could not is in [examples/the-dns-record.md](examples/the-dns-record.md).

## Why this works when self checking does not

A self improving loop is only as good as its judge, and a model judging itself eventually grades its own homework. In code, reality can judge instead: tests pass or fail, builds hold or break, logs say what actually happened. This skill is built to force the loop to consult that reality early and often, instead of arguing with itself.

## Honest limits

This skill has not been through a controlled evaluation yet. Its support is one real incident and the reasoning in this repo, not measured before and after numbers. Treat it as a strong discipline, not a proven result.

How to evaluate it properly: take a set of real debugging tasks, run each one twice with the same model, with the skill and without it, and compare attempts per fix, tokens per fix, and regressions introduced. If you run that comparison, this repo wants your numbers, good or bad. That is the difference between a claim and research, and the skill should be held to its own standard: no verdict without evidence.

## Contributing

Real incidents welcome. If this discipline caught something guessing would not, open an issue or a pull request with the story. New failure patterns, sharper fingerprint definitions, and per platform surface inventories (Vercel, Render, Supabase, Cloudflare, AWS) are the most useful contributions.

## License

MIT. See [LICENSE](LICENSE).

Built by Muhammad Abdullah Shah ([Flux Studio](https://fluxyai.codes)) with Muse, from a design session on self improving loops, 2026-11-21.
